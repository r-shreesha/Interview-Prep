# Scaling from Zero to 1M users
### Client - WebServer
### Separate Web Tier from Data Tier - Add Database Server
### Add Load Balancer to redirect requests
### Database Replication (Slave reads, Master Writes)
- Better Performance
- Reliability
- High Availability
- If Master goes down, one of the slaves can assume Master role

### Cache Tier
#### Advantages
- Reduce Database requests
- Improve system performance
- Suitable for read intensive systems

#### Challenges
- Cache inconsistency
- Single Point of Failure(SPOF) (Multiple cache servers across DC are recommended)
- Expiration policy
- Eviction Policy

### CDN
Caches the static content and gets returned for all the requests until TTL for that content hasn't reached. When it does, the requests goes to the webserver/S3 storage and gets cached again.

### Stateless Web Tier - Scale Web Tier horizontally
Make the web tier stateless by storing the state information in some form of persistent storage such as RDBMS or NoSQL databases. Each web server fetches the state information from the database. This is called Stateless Web Tier.
This is simpler, more robust and scalable.

### Multi DC replication

### Message Queues
A durable component stored in memory, that supports asynchronous communication. Web servers(Producer) can distribute the requests to Workers (Consumers) through these in-memory buffers called Message queues. This way, based on the load, the workers can web servers can be scaled independently.

### Database Scaling
a) Vertical Scaling (Not feasible, higher cost, SPOF)
b) Horizontal Scaling (Sharding - separates large databases into smaller, easily managed parts(shards))

11. Logging, Metrics, Monitoring, Automation

### Useful References
https://newsletter.systemdesign.one/p/aws-scale

# How to Approach Database Scaling

If your application is experiencing load problems, it's time to bring out the champagne! Your app's growing pains are actually a sign of its success.

As the user base expands, so do the demands on your system. You may notice database queries become sluggish, network requests time out, and user interfaces start to lag.

These signs indicate that it's time to scale your infrastructure to keep up with demand.

Today we’ll be honing in on how to approach scaling your database. Let’s jump in.

## The Cost of Scaling

Before you start you start diving into implementing scaling solutions, an important principle should be kept in mind.

You shouldn’t implement premature optimizations or attempt to scale your app before it’s actually needed.

Implementing scaling solutions introduces complexities, including:

* New features take longer to develop

* The system becomes more complex with more pieces and variables involved

* Code can be more difficult to test

* Finding and resolving bugs becomes harder

You should only accept these trade-offs if your app is reaching capacity. Keep the system simple, don’t introduce scaling complexities unless it’s warranted.

## Finding Bottlenecks Using Metrics
To scale effectively, first, identify your bottlenecks.

Time to check your resource monitoring system or create one if you haven’t already.

If you’re running on any of the leading IaaS providers, such as AWS and Azure, there are already application performance management tools available out of the box.

Analyze and observe resource usage. Pinpoint what’s holding your app back. Look for spikes or flat tops in your monitoring tools, indicating that a resource is overwhelmed.

If performance issues aren't immediately apparent, but your app seems to be running slow, try sprinkling logs in heavily used operations to check for slow-loading resources.

This analysis will guide your scaling strategy, ensuring you address the right problems.

## Scaling an App From a Birdseye View

Now that we’ve got a good sense of what/where the problems/bottlenecks are, we can start implementing solutions to address these issues.

Remember, simplicity is key, we want to avoid introducing unnecessary complexities.

The high-level goal of scaling solutions is to have the stack do less work for the application’s most common requests or effectively distribute across multiple resources the workload that can’t be eliminated.

The way that scaling techniques do this usually translates into one or more of the following:

## 1. Reusing data the app has already looked up

Eliminating requests from the client for data the app already possesses

Storing results of common operations in order to reduce repeating computations

Avoiding complex operations in the request-response cycle

Many scaling techniques boil down to some form of caching.

## 2. Cache Database Queries
Caching database queries is one of the simplest improvements you can make to handle database load.

Usually, an application will have a handful of queries that make up the majority of the requests made.

Rather than making a round trip over the network each time for that data, it can simply be cached in memory on the webserver.

To take it one step further, it can be cached in a dedicated in-memory store such as Redis.

Data that’s cached can become ‘stale’ or out of date quite quickly. You will have to be mindful of **which data you chose to cache and how long for**.


## 3. Database Indexing
Proper indexing is another efficient lower hanging fruit to enhance performance.

Indexes help locate data quickly without scanning the entire database, significantly speeding up query times.

It’s another simple “early on” solution that provides outsized returns.

However, maintaining the right balance in indexing is important.

Overly indexed databases can slow down because each insert, update, or delete operation requires additional processing to update the indexes. 
**This can lead to slower write performance and increased storage requirements, if not done correctly**.


## 4. Database Read Replication

If your database is still under heavy read load even after implementing caching, and efficient indexing, considering read replication can often be a next step.

Read replication involves writing to a single primary database, which is then cloned into multiple read replicas on different machines.

This distributes the read load across several servers, taking the pressure off the primary database and improving write performance.

Additionally, having replicas in different regions can dramatically increase read speed and reduce latency.

However, writes to the primary database need to propagate to the replicas, which can lead to temporary data inconsistencies. 

_For immediate read-after-write consistency, such as updating and immediately viewing a profile, read from the primary database._

Read replication is a powerful scaling solution but comes with its fair share of complexities. It's advisable to implement it only after exhausting simpler solutions and ensuring optimal application performance.


## 5. Database Sharding

Most scaling solutions focus on reducing database read loads.

Database sharding, however, manages both reads and writes by partitioning the primary database into multiple smaller databases (shards). This horizontal scaling technique splits data across multiple nodes or servers.

There are two types of sharding: horizontal and vertical.

**Horizontal sharding involves placing rows across different machines with identical columns.**

**Vertical sharding splits a table into distinct tables with separate rows and columns across multiple machines.**

Sharding can speed up queries and improve resilience to failures, as an _outage typically affects only a single shard rather than the entire database._

Database sharding is powerful and carries a lot of benefits. However, **It should be considered after exhausting other scaling solutions as it is complex and costly to implement and costly to maintain**. Further, the ramifications of ineffective implementation can be quite severe.


## 6. Leveraging DBaaS for Efficient Scaling

These days a lot of the manual implementation can be abstracted away.

Database as a service (DBaaS) solutions, including Amazon RDS, Google Cloud SQL, and Microsoft Azure SQL Database, offer scalable database management with minimal manual intervention.

These services facilitate the scaling process (both vertical and horizontal) with automation features, enabling you to concentrate more on optimizing your application and less on intensive infrastructure management.

## Wrapping Up

Scaling your database effectively involves more than just throwing resources at the problem.

It requires a strategic approach tailored to your specific challenges.

There are general patterns, best practices, and solutions that are typically applied early on and later on.

As you plan your scaling strategy, remember that each step not only addresses immediate needs but also sets the groundwork for sustained growth.
