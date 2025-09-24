So, there are some context around the cache:
    * Writing Policies
        * Write-Through: Data is written to the cache and the database at the same time.
           - ensures data consistency, but can be slower due to synchronous writes.
        * Write-Back (or Write-Behind): Data is written to the cache first, then asynchronously to the database.
            - low latency, high throughput, but risk of data loss if cache fails before writing to the database.
        * Write-around: update cache only when data is read, not when written.
            - reduces cache pollution, but can lead to cache misses on subsequent reads.
    * Reading Policies
        * Read-Through: Data is read from the cache, and if not present, fetched from the database.
        * Read-Behind: Data is read from the cache, and if not present, fetched from the database asynchronously.
        * Read-Once: Data is read from the cache only once, not from the database.
    * Eviction Policies
        * Least Recently Used (LRU): Removes the least recently used items first.
            Example usecase: least recently used (LRU) can be a good choice for social media services where recently uploaded content will likely get the most views
        * Least Frequently Used (LFU): Removes the least frequently used items first.
        * First In First Out (FIFO): Removes the oldest items first.
        * Time-To-Live (TTL): Removes items after a specified time period.
    * Consistency Models
        * Strong Consistency: Guarantees that all reads will return the most recent write.
        * Eventual Consistency: Guarantees that all updates will eventually propagate to all replicas.
    * Cache Coherence
        * Ensures that all nodes in a distributed cache see the same data at the same time.
        * Can be achieved through protocols like Two-Phase Commit or Paxos.
    * Cache Partitioning
        * Divides the cache into smaller, manageable parts.
        * Can be done by hashing keys or using consistent hashing.
    * Cache Replication
        * Copies data across multiple nodes to ensure availability and fault tolerance.
    * Cache Warm-Up
        * Preloading the cache with frequently accessed data to improve performance.
    * Cache Invalidation
        * Mechanism to remove or update stale data in the cache.
    * Cache Hit Ratio
        * The ratio of cache hits to total requests, indicating cache effectiveness.
    * Cache Size
        * The total amount of memory allocated for the cache.
    * Cache Latency
        * The time taken to retrieve data from the cache.
    * Cache Throughput
        * The number of requests processed by the cache in a given time period.
    * Cache Scalability
        * The ability to handle increased load by adding more nodes or resources.
    * Cache Security
        * Ensuring that sensitive data in the cache is protected from unauthorized access.
    * Cache Monitoring
        * Tools and techniques to track cache performance, hit ratios, and other metrics.
    * Cache Backup and Recovery
        * Mechanisms to back up cache data and recover it in case of failures.
    * Cache Configuration
        * Settings that control cache behavior, such as size limits, eviction policies, and timeouts.
    * Multilevel Caching
        * Using multiple layers of cache (e.g., local cache, distributed cache) to optimize performance.



TTL strategies:-
    1. Active expiration: scan expired items periodically and remove them.
    2. Passive expiration: remove items when they are accessed and found to be expired.


Cache Partitioning:-
    To distribute the cache, across multiple nodes we can use the hasing technique, mainly consistent hashing.
    * Consistent hashing allows for minimal disruption when nodes are added or removed.


DS to use in the cache:-
    1. HashMap: for fast key-value lookups.
    2. Doubly linked list: for implementing LRU eviction policy.
    3. Bloom filter: probabilistic data structure to quickly check if an item is  not in the cache.
    4. Priority queue: for LFU eviction policy.





Requirements:-

    1. Functional requirements:
        1. Set/ insert the data to the cache
        2. Get/ retrieve the data from the cache
        3. Delete/ remove the data from the cache
        4. Update the data in the cache
        5. Evict data based on the eviction policy

    2. Non-functioanal requirements:-
        1. Performance, low latency and high throughput
        2. Scalability: able to handle increase in load/data
        3. Availiability: able to recover form system failures
        4. Consistency: ensure data integrity from different nodes

