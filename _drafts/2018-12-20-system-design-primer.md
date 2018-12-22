---
layout: post
title:  "《System Design Primer》笔记"
date:   2018-12-20 17:18:30 +0800
categories: posts
---

# System design topics

* Step 1: Review the scalability video lecture
  * Topics covered:
    * Vertical scaling
      * CPU
        * cores, L2 cache, ...
      * Disk
        * PATA, SATA, SAS, ..
        * RAID
      * RAM
    * Horizontal scaling
    * Caching
      * .html
      * MySQL Query Cache
      * memcached
      * ...
    * Load balancing
      * Software
        * ELB
        * HAProxy
        * LVS
        * ...
      * Hardware
        * Barracuda
        * Cisco
        * Citrix
        * F5
        * ...
    * Database replication
    * Database partitioning

* Step 2: Review the scalability article
  * Clones
  * Databases
  * Cache
    * never do file-based caching
    * Cached Database Queries
    * Cached Objects
  * Asynchronism

* High-level Trade-offs
  * Performance vs scalability
    * A service is scalable if it results in increased performance in a manner proportional to resources added. Generally, increasing performance means serving more units of work, but it can also be to handle larger units of work, such as when datasets grow.
  * Latency vs throughput
    * Latency is the time to perform some action or to produce some result. Latency is measured in units of time -- hours, minutes, seconds, nanoseconds or clock periods.
    * Throughput is the number of such actions or results per unit of time.
    * Generally, you should aim for maximal throughput with acceptable latency.
  * Availability vs consistency
    * CAP theorem
      * In a distributed computer system, you can only support two of the following guarantees:
        * Consistency - Every read receives the most recent write or an error
        * Availability - Every request receives a response, without guarantee that it contains the most recent version of the information
        * Partition Tolerance - The system continues to operate despite arbitrary partitioning due to network failures
      * Networks aren't reliable, so you'll need to support partition tolerance. You'll need to make a software tradeoff between consistency and availability.
      * CP - consistency and partition tolerance
        * Waiting for a response from the partitioned node might result in a timeout error. CP is a good choice if your business needs require atomic reads and writes.
      * AP - availability and partition tolerance
        * Responses return the most recent version of the data available on a node, which might not be the latest. Writes might take some time to propagate when the partition is resolved.
    * Consistency patterns
      * Weak consistency
      * Eventual consistency
      * Strong consistency
    * Availability patterns
      * Fail-over
        * Active-passive
        * Active-active
      * Disadvantage(s): failover
        * Fail-over adds more hardware and additional complexity.
        * There is a potential for loss of data if the active system fails before any newly written data can be replicated to the passive.
      * Replication
        * Master-slave and master-master
    * Domain name system
    * Disadvantage(s): DNS
      * Accessing a DNS server introduces a slight delay, although mitigated by caching described above.
      * DNS server management could be complex and is generally managed by governments, ISPs, and large companies.
      * DNS services have recently come under DDoS attack, preventing users from accessing websites such as Twitter without knowing Twitter's IP address(es).
    * Content delivery network
      * Push CDNs
      * Pull CDNs
    * Disadvantage(s): CDN
      * CDN costs could be significant depending on traffic, although this should be weighed with additional costs you would incur not using a CDN.
      * Content might be stale if it is updated before the TTL expires it.
      * CDNs require changing URLs for static content to point to the CDN.
    * Load balancer
    * Horizontal scaling
    * Reverse proxy (web server)
    * Application layer
    * Microservices
    * Relational database management system (RDBMS)
      * ACID is a set of properties of relational database transactions.
        * Atomicity - Each transaction is all or nothing
        * Consistency - Any transaction will bring the database from one valid state to another
        * Isolation - Executing transactions concurrently has the same results as if the transactions were executed serially
        * Durability - Once a transaction has been committed, it will remain so
      * Master-slave replication
      * Master-master replication
      * Federation (or functional partitioning)
      * Sharding
      * Denormalization
        * materialized views
      * SQL tuning
        * It's important to benchmark and profile to simulate and uncover bottlenecks.
    * NoSQL
      * NoSQL is a collection of data items represented in a key-value store, document-store, wide column store, or a graph database. Data is denormalized, and joins are generally done in the application code. Most NoSQL stores lack true ACID transactions and favor eventual consistency.
      * BASE is often used to describe the properties of NoSQL databases. In comparison with the CAP Theorem, BASE chooses availability over consistency.
        * Basically available - the system guarantees availability.
        * Soft state - the state of the system may change over time, even without input.
        * Eventual consistency - the system will become consistent over a period of time, given that the system doesn't receive input during that period.
      * Key-value store
      * Document store
      * Wide column store
      * Graph database
    * SQL or NoSQL
      * Reasons for SQL:
        * Structured data
        * Strict schema
        * Relational data
        * Need for complex joins
        * Transactions
        * Clear patterns for scaling
        * More established: developers, community, code, tools, etc
        * Lookups by index are very fast
      * Reasons for NoSQL
        * Semi-structured data
        * Dynamic or flexible schema
        * Non-relational data
        * No need for complex joins
        * Store many TB (or PB) of data
        * Very data intensive workload
        * Very high throughput for IOPS
      * Sample data well-suited for NoSQL:
        * Rapid ingest of clickstream and log data
        * Leaderboard or scoring data
        * Temporary data, such as a shopping cart
        * Frequently accessed ('hot') tables
        * Metadata/lookup tables
    * Cache
      * Client caching
      * CDN caching
      * Web server caching
      * Database caching
      * Application caching
      * Caching at the database query level
      * Caching at the object level
      * Cache-aside
      * Write-through
      * Write-behind (write-back)
      * Refresh-ahead
    * Asynchronism
      * Message queues
      * Task queues
      * Back pressure
    * Hypertext transfer protocol (HTTP)
    * Transmission control protocol (TCP)
    * User datagram protocol (UDP)
    * Remote procedure call (RPC)
    * Representational state transfer (REST)
    * Security
