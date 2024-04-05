## Scaling from Zero to 1M users
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
