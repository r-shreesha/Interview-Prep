# Key Value Store

## Understanding Requirements

- The size of the key-value pair is less than 10KB
- Ability to store big data
- High availability: System responds quickly, even during failures
- High scalability: They system can be scaled to support large data set
- Automatic scaling: The addition/deletion of servers should be automatic based on traffic
- Tunable consistency
- Low latency

### CAP theorem

It is impossible for a distributed system to support more than two of these three guarantees: 
- Consistency: all clients see the same data at the same time, no matter which node they connect to.
- Availability: client which requests data gets a response even if some of the nodes are down
- Partition Tolerance: system continues to operator despite network partitions (communication break between two nodes)

Since network failure is unavoidable, a distributed system myst tolerate network partition. a CA system cannot exist in real-world applications.

### High Level Design

System components:
- Data Partition
- Data Replication
- Consistency models
- Inconsistency resolution
- Handling failures
- System Architecture diagram
- Write Path
- Read Path

### Data Partition
Challenges:
- Distribute data across multiple servers evenly
- Minimize data movement when nodes are added/removed

Solution: Consistent Hashing

Advantages:
- Automatic scaling: servers could be added and removed automatically depending on the load
- Heterogeneity: # of virtual nodes for a server is proportional to the server capacity. Higher capacity servers are assigned with more virtual nodes.

### Data Replication

To achieve HA and reliability, data must be replicated asynchronously over N(configurable) servers. 

When the key is mapped on to the ring and if N = 3, the next 3 unique servers in the clockwise direction would replicate the data copies.

Nodes in the same data center often fail at the same time due to power outages, network issues, natural disasters. 

For better reliability, replicas are placed in distinct data centers and these DCs are connected through high-speed networks.

### Consistency Models
*****
N = The number of replicas

W = A write quorum - For a write operation to be considered as successful, write op must be acknowledged from W replicas

R = A read quorum - For a read operation to be successful, a read op should wait for responses from at least R replicas
*****

`W = 1 or R = 1`, operation is returned quickly as the coordinator needs to wait for a response from any of the replicas

`W > 1 or R > 1`, the system offers better consistency

`W + R > N`, strong consistency is guaranteed as there must be at least one overlapping node that has the latest data to ensure consistency

`R = 1, W = N`, the system is optimized for a fast read

`W = 1, R = N`, the system is optimized for a fast write

`W + R <= N`, strong consistency is not guaranteed.

`Dynamo DB` and `Cassandra DB` adopt eventual consistency model.

With eventual consistency approach, we need to have an inconsistency resolution, like versioning

#### Inconsistency resolution: Versioning/Vector Clock

Versioning: Treating each data modification as a new immutable version of data.

Vector Clock: `[server, version]` pair associated with a data item. 

Vector clock is represented as `D([S1, v1], [S2, v2],...,[Sn, vn])`. Here `D` is a data item, `v1` is a version counter, `s1` is a server number. 

If data item `D` is written to server `Si`, the system must perform one of the following tasks:
- Increment vi if `[Si,vi]` exists.
- Otherwise, create a new entry `[Si,1]`

Example:
1. `D1([Sx,1])` ------> write handled by Sx
2. `D2([Sx,2])` ------> write handled by Sx
   The below two operations happen in parallel
3. `D3([Sx, 2], [Sy, 1])` ----------> write handled by Sy
4. `D4([Sx, 2],[Sz, 1])` -----------> write handled by Sz
   Reconciled and written by Sx
5. `D5([Sx, 3], [Sy, 1], [Sz, 1])`

The `[server:version]` pair could grow rapidly. To fix this problem, we set a threshold for the length, and if it exceeds the limit, the oldest pairs are removed.

This could lead to inefficiencies in reconciliation as the descendant relationship cannot be determined accurately. 

However, Dynamo DB has not yet encountered this problem in production. So it is still an acceptable solution for most companies.

### Handling Failures

#### 1. Failure detection - Gossip Protocol

Usually it requires two independent sources of information to mark a server down. Decentralized failure detection methods like Gossip protocol works best.
- Each node maintains a node-membership list, which contains member IDs and heartbeat counters
- Each node periodically increments its heartbeat counters
- Each node periodically sends heartbeats to random nodes, which in turn propogate to another set of nodes
- Once nodes receive heartbeats, membership list is updated to the latest info.
- If the heartbeat has not increased for more than predefined periods, the member is considered offline.

#### 2. Handling Temporary Failures - Sloppy Quorum - Hinted Handoff

System needs to deploy certain mechanisms to ensure availability after detecting failures through the Gossip protocol

A technique called `"Sloppy Quorum"` can be used to improve availability.

System chooses the first W healthy servers for writes and first R healthy servers on the hash ring. Offline servers are ignored.

If a server is unavailable due to network or server failures, another server will process requests temporarily. When the down server comes back up, changes will be pushed back to achieve data consistency. 
This process is called `hinted handoff`. 

#### 3. Handling Permanent Failures - Merkle Tree

If a replica goes completely unavailable, we implement an anti-entropy protocol to keep replicas in sync. Anti-entropy involves comparing each piece of data on replicas and updating each replica to the newest version. 

A `merkle tree` is used for inconsistency detection and minimizing the amount of data transferred.

A hash tree or Merkle tree is a tree in which every non-leaf node is labeled with the has of the labels or values(in case of leaves) of its child nodes. 

Hash trees allow efficient and secure verification of the contents of large data structres.

Using Merkle trees, the data needed to be synchronized is proportional to the differences between the two replicas and not the amount of data they contain. 

In real-world systems, the bucket size is quite big. Eg: 1M buckets per 1B keys, so each bucket contains 1000 keys.

#### DC outage

Replicate across multiple data centers. Even if a DC is completely offline, users can still access data through the other data centers.

### Write Path

- Write request is persisted on a commit log file
- Data is saved in the memory cache
- When the memory cache is full or reaches a pre-defined threshold, data is flushed to SSTable(A sorted string table) on disk.
- SSTable is a sorted list of <key, value> pairs.

### Read Path

- Check if data is in the memory cache. If so, return directly
- If not, it will be retrieved from the disk instead.
- We need an efficient way to find out which SSTable contains the key. `Bloom filter` is commonly used to solve this problem.
- SSTables return the result of the data set
- The result of the data set is returned to the client

### Wrap Up

| Goal/Problems | Technique |
| --- | --- |
| Ability to store big data | Use consistent hashing to spread the load across servers|
| High availability reads | Data replication, Multi-data center setup |
| High availability writes | Versioning and conflict resolution with vector clocks |
| Dataset partition | Consistent Hashing |
| Incremental scalability | Consistent Hashing |
| Heterogeneity | Consistent Hashing |
| Tunable Consistency | Consistent Hashing |
| Handling temporary failures | Sloppy Quorum and hinted handoff |
| Handling permanent failures | Merkle tree |
| Handling data center outage | Cross-data center replication |
