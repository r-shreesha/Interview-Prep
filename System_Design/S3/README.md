# What is Object Storage

- In comparison to file and block storage, Object storage is new. It makes a very deliberate tradeoff to `sacrifice performance` for `high durability, vast scale and low cost`.
- Object Storage targets "cold" data and is mainly used for archival and backup.
- Object Storage stores all data as objects in a `flat` structure. There is no hierarchical directory structure.
- Data access is normally provided via a RESTful API. It is relatively slow compared to other storage types.
- Ensures `Strong Consistency` similar to block and file storage

## Comparison

| | Block Storage | File Storage | Object Storage |
| --- | --- | --- | --- |
| Cost | High | Medium to High | Low |
| Performance | Medium to very High | Medium to High | Low to medium |
| Data Access | iSCSI/FC protocols | Standard file access, CIFS/SMB, NFS | RESTful API |
| Scalability | Medium | High | Vast |
| Good for | VM, High Performance Apps like DB | General purpose file system access | Binary data, unstructured data, video, images |
| Mutable Content | Y | Y | N (object versioning is supported, in place update is not) |

## Terminology

- **Bucket**. A logical container for objects. The bucket name is globally unique.
- **Object**. An object is an individual piece of data we store in a bucket. It contains object data(also called payload) and metadata.
  * Object data. can be any sequence of data we need to store.
  * Metadata. Set of name-value pairs that describe the object.
- **Versioning**. A feature that keeps multiple variants of an object in the same bucket. This is enabled at bucket level. This enables users to recover objects that are deleted or overwritten accidentally
- **Uniform Resouce Identifier (URI)**. Object storage provides RESTful APIs to access its resources - buckets and objects. Each resource is uniquely identified by its URI
- **Service Level Agreement(SLA)**. This is a contract between service provider adn a client. Example, S3 Standard Infrequent Access storage class provides the following SLA:
    * Designed for durability of 99.999999999% of objects across multiple Availability zones.
    * Data is resilient in the event of one entire Availability Zone destruction.
  

# Understanding Requirements
C: What features should be included in the design?

I: Create Buckets, List Buckets, Object uploading, Object download, Object Versioning, Listing objects within a bucket, similar to `aws s3 ls`

C: What is the typical data size?

I: We need to store massive objects(few GBs) and a number of small objects(tens of KBs) efficiently

C: How much data do we need to store in one year?

I: 100 petabytes (PB)

C: What are some of the non-functional requirements we need?

I: Data durability is 6 nines, Service availability is 4 nines, 100PB of data, Storage Efficiency. Reduce Storage costs while maintaining high degree of reliability and performance

## Estimation

Disk Capacity: Let's assume 20% small objects(< 1MB), 60% medium sized objects (1MB - 64MB), 20% large objects (> 64MB)

IOPS: 1 hard disk(7200 rpm) is capable of doing 100 ~ 150 random seeks per second (100 ~ 150 IOPS)

With this assumption, 40% usage ratio implies 0.68 billion objects. If each metadata is 1KB in size, we need 0.68TB space to store them.

# High Level Design
![image](https://github.com/r-shreesha/Interview-Prep/blob/main/Design_Diagrams/S3_HLD.png)

**Load Balancer** - Distributes RESTful API across a number of API servers

**API Service** - Orchestrates RPCs to the IAM service, Metadata service, Storage stores. This service is *stateless* so it can be horizontally scaled

**IAM** - Central place to handle AAA(Authn, Authz, Access Control).

**Data store** - Stores and retrieves actual data. All data-related operations are based on object ID(UUID)

**Metadata store** - Stores metadata of the objects.

In Ceph, there is no metadata store. All are persisted as one or multiple Rados objects.

## Uploading an object

Let's walk through an example of creating a bucket named `bucket-to-share` and then upload a file named `script.txt` to the bucket.

1. Client sends HTTP PUT request to create a bucket named `bucket-to-share`. The request is forwarded to API service
2. The API service calls the IAM to ensure the user is authorized and has WRITE permission
3. The API service calls metadata store to create an entry with the bucket info in the metadata database. Once the entry is created, a success message is returned to the client.
4. After the bucket is created, client sends HTTP PUT request to create an object named `script.txt`
5. The API service verifies user's permission and ensures the user has the WRITE permission on the bucket.
6. Once validation succeeds, the API service sends the object data in the HTTP PUT payload to the data store. The data store persists the payload as an object and returns the UUID of the object.
7. The API service calls the metadata store to create a new entry in the metadata database. It contains important metadata such as the `object_id`(UUID), `bucket_id`, `object_name`, etc.

| object_name | object_id | bucket_id |
| --- | --- | --- |
| script.txt | a2eb7b9a-2fdd-49b5-8c8d-7cce462de8f7 | 39d42efd-4f1d-468d-9dd6-1b3a046cc827 | 

## Downloading an object

As the datastore doesn't store the name of the object, we should first get the mapping of the `object_id` for the given `object_name`.
1. Client sends an HTTP GET request to the load balancer: `GET /bucket-to-share/script.txt`
2. The API service queries the IAM to verify that the user has READ access to the bucket.
3. Once validated, the API service fetches the corresponding object's UUID from the metadata store.
4. Next, the API service fetches the object data from the data store by its UUID.
5. The API service returns the object data to the client in HTTP GET response.

# Design Deep Dive

We deep dive into a few areas:

* Data store
* Metadata data model
* Listing objects in a bucket
* Object versioning
* Optimizing uploads of large files
* Garbage collection

## Data store
![image](https://github.com/r-shreesha/Interview-Prep/blob/main/System_Design/S3/Data_Store_Upload.jpg)

### High level design of data store
![image](https://github.com/r-shreesha/Interview-Prep/blob/main/System_Design/S3/DataStoreComponents.jpg)

#### Data Routing Service (DRS)
- Query placement service to get the best data to store data
- Read data from data nodes and return it to API service
- Write data to data nodes

#### Placement Service
Placement service determines which data nodes(Primary and replicas) should be chosen to store an object.
- Given a UUID, return its replication group to the data routing service while reading the object
- Given a UUID, return the primary data node to DRS so that it can contact directly for the data writes.
- Get periodic heartbeats from these data nodes.
- Maintain virtual cluster map (physical topology of the cluster) with location information for each data nodes
- It ensures that the replicas are physically seperated (key for high durability)
  
#### Data Node
- Ensures reliability and durability by replicating data to multiple data nodes, called replication group.
- Periodically sends heartbeats to the placement server. HB msg contains:
    - How many disk drives (HDD/SSD) does the data node manager?
    - How much data is stored in each drive?
- For the **First Heart Beat**, placement service returns <unique ID of the data node, virtual cluster map, where to replicate data(Replication group)>
  
#### Data Persistence Flow

1. API service forwards the object data to the data store
2. DRS generates UUID for this object and queries placement service for the data node to store this object
3. Placement service checks the virtual cluster map and returns the primary data node
4. DRS directly sends the data to the primary data node, together with its UUID.
5. Primary data node stores the data locally, replicates it among the secondary replicas before sending the response back to DRS.
6. UUID of the object is returned back to API service.

Step 2. How does placement service looks up the replication group for a given UUID? Consistent hashing.

#### How data is organized

If each object is stored in a stand-alone file, two issues may arise:
 a) each block would consume 4KB even for very small objects.
 b) it might exceed system's inode capacity. 
2. Better option: Merge small objects into a larger file. It works conceptually like a Write Ahead Log. 

When we save an object, it gets written to a `read-write` file. Once the capacity of the file is reached (usually set to few GBs), we mark the file as `read-only` and create a new `read-write` file.
For this to work, the write access needs to be serialized. This might be a problem in a multi-core system when incoming write requests are coming in parallel, thereby restriting write throughput.
To fix this we could provide dedicated read-write files, one for each core processing incoming requests.

#### Object Lookup
The data node needs:
1. Data file containing the object
2. The starting offset of the object in the data file
3. The size of the object

Database schema: 
(object_mapping: <object_uuid, file_name, start_offset, object_size>)
To store this information, we could use
- RocksDB - a file based key-value store. It is based on SSTable, it is fast for writes but slower for reads
- Relational DB - Fast for reads but slower for writes
As this is a read heavy system, we could use Relational DB.
How to deploy this DB? Deploying a single large cluster to support all data nodes could work, but difficult to manage.
Further these data are specific to the data node.
We could deploy a **SQLite(file based relational database)** on each node.


#### Durability - Replication vs Erasure Coding
Replicate across different availability zones. 
Replication is widely adopted in latency-sensitive applications
Erasure coding offers low storage cost, high durability. But it complicates the data node design.

#### Correctness Verification
Add the checksum in the read-write file at the end of every object.
Also, add a checksum of the entire file at the end.

## Metadata Data model

### Schema
The schema need to support 3 queries essentially
Q1: Find the object ID by object name
Q2: Insert/Delete an object based on the object name
Q3: List objects in a bucket sharing the same prefix

| Bucket |
| --- |
|bucket_name|
|bucket_id|
|owner_id|
|enable_versioning|

| Object |
| --- |
|bucket_name|
|object_name|
|object_version|
|object_id|


### Scaling the bucket table

Usually there is a limit on the number of buckets user can create. If 1M users are allowed to create 10 buckets and if each record takes 1KB, we need 10GB of storage space. Any modern database server can accommodate this.

However, a single database server might not have enough CPU or network bandwidth to handle all the read requests. So we can spread the read load among multiple database replicas.

### Scaling the object table

The object table holds object metadata. The dataset at our design scale will likely not fit in a single database instance. We can scale the object table by `Sharding`.

If we shard by `bucket_id`, each shard will have all the objects belonging to the same bucket. However, we can have hotspot shards as bucket might contain billions of objects.

If we shard by `object_id`, it evenly distributes the load. However, we will not be able to execute Q1 and Q2 efficiently as they are based on object URI.

We choose to shard by a combination of `bucket_name` and `object_name`. This is because metadata operations are based on object URI. For example, finding object ID by URI, uploading an object via URI. etc
To evenly distribute the data, we can use the hash of `<bucket_name, object_name>` as the sharding key.

## Listing Objects in a bucket
With distributed databases, metadata store needs to run the query in each database, aggregate the results and return to the client.
It might pose a problem with pagination of the records as each database may contain varying amount of records.

As the performance of this operation can be lowered as a trade off to better scale and high durability, we could denormalize the listing data into a separate table shared by bucket id and use this table for listing objects only. Even with billions of records, this offers acceptable performance.

## Object Versioning

## Optimizing uploads of large files

## Garbage collection

# Wrap Up
