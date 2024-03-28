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

To achieve HA and reliability, data must be replicated asynchronously over N(configurable) servers. When the key is mapped on to the ring and if N = 3, the next 3 unique servers in the clockwise direction would replicate the data copies.
Nodes in the same data center often fail at the same time due to power outages, network issues, natural disasters. For better reliability, replicas are placed in distinct data centers and these DCs are connected through high-speed networks.

### Consistency Models

N = The number of replicas
W = A write quorum - For a write operation to be considered as successful, write op must be acknowledged from W replicas
R = A read quorum - For a read operation to be successful, a read op should wait for responses from at least R replicas

W = 1 or R = 1, operation is returned quickly as the coordinator needs to wait for a response from any of the replicas
W > 1 or R > 1, the system offers better consistency
W + R > N, strong consistency is guaranteed as there must be at least one overlapping node that has the latest data to ensure consistency

R = 1, W = N, the system is optimized for a fast read
W = 1, R = N, the system is optimized for a fast write
W + R <= N, strong consistency is not guaranteed.

Dynamo DB and Cassandra DB adopt eventual consistency model.

### Handling Failures

#### Failure detection 

Usually it requires two independent sources of information to mark a server down. Decentralized failure detection methods like Gossip protocol works best.
- Each node maintains a node-membership list, which contains member IDs and heartbeat counters
- Each node periodically increments its heartbeat counters
- Each node periodically sends heartbeats to random nodes, which in turn propogate to another set of nodes
- Once nodes receive heartbeats, membership list is updated to the latest info.
- If the heartbeat has not increased for more than predefined periods, the member is considered offline.


### Wrap Up
