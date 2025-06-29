# Redis

## Cache
Redis is a fast in-memory data store. It reduces the load on the backend system by storing frequently accessed data in the cache, so the application doesn't have to retrieve it from the database. It's essential to select the right cache expiration policy to ensure that relevant and fresh information is always available in the cache.

## Redis as an In-memory Database
Redis is not only a cache but also an in-memory database (DB), supporting various data structures and operations.

## Scaling Redis
Scalability refers to the system's ability to handle increasing workloads by adding resources. Redis can scale in two ways:

- **Vertical Scaling**: Increasing the capacity of a single Redis server.
- **Horizontal Scaling**: Adding more Redis servers and distributing the data across them.

Redis is single-threaded, meaning it can only handle one request at a time per core. However, if data is split between two Redis servers, Redis can process requests in parallel, effectively doubling the throughput. This pattern of splitting data across multiple servers for scaling is called **sharding**, and the servers holding data are referred to as **shards**.

### Sharding and Algorithmic Sharding
Redis uses **algorithmic sharding** to track which shard holds a particular key. The general approach is:

  ```
  key -> Hash -> Mod (number of shards)
  ```

However, when a new shard is added, the same key might map to a different shard. Redis resolves this by assigning each key to a **hash slot** instead of a server. There are always 16,384 hash slots in a Redis cluster, which are divided among the shards. When shards are added or removed, only the distribution of hash slots is adjusted, minimizing data movement.

## High Availability
High availability ensures that the cluster remains operational even in case of failures. 

Redis achieves this by allowing each primary shard to have one or more replicas. For instance, in a cluster with 3 primary shards, you would need 6 servers—3 for the primary shards and 3 for the replicas. All servers are connected via TCP and constantly ping each other to maintain synchronization and detect failures.
