### 1. What is Apache Kafka?

- [Apache Kafka](https://www.geeksforgeeks.org/apache-kafka/apache-kafka/) is a distributed streaming platform that allows for publishing, subscribing to, storing, and processing streams of records in real-time.
- It's designed to handle high-throughput, fault-tolerant, and scalable data pipelines.
- Kafka is often used for building real-time data pipelines and streaming applications.

### 2. How is Kafka different from traditional message queues (e.g., RabbitMQ/JMS)?

Apache Kafka and traditional message queues such as RabbitMQ and JMS are messaging systems, but they are designed for different use cases.

**Apache Kafka**

- Distributed event streaming platform.
- Stores messages in an immutable log for a configurable retention period.
- Consumers read messages at their own pace using offsets.
- Supports very high throughput and horizontal scalability.
- Ideal for real-time analytics, event streaming, and log aggregation.
- ****Goal:**** Stream and process large volumes of events.

**Traditional Message Queues***

- Message broker for application communication.
- Messages are typically removed after being consumed.
- Focuses on reliable message delivery and task processing.
- Lower throughput than Kafka but supports rich messaging patterns.
- Ideal for request-response systems, task queues, and enterprise messaging.
- **Goal:*** Deliver messages reliably between applications.

### 3. What are the advantages of using Kafka?

- High throughput and low latency, even at large scale
- Horizontal scalability by adding brokers/partitions
- Durability via disk-based storage and replication
- Fault tolerance through leader/follower replication
- Decouples producers and consumers, enabling independent scaling
- Message replay capability since consumed messages aren't deleted
- Strong ecosystem: Kafka Connect, Kafka Streams, ksqlDB, Schema Registry

### 4. What are the limitations/disadvantages of Kafka?

- Operational complexity for self-managed clusters (though KRaft has simplified this considerably)
- No built-in message routing/transformation like a traditional message broker (needs Kafka Streams/Connect)
- Ordering is guaranteed only within a partition, not across a topic
- Not ideal for very large individual messages (default limit ~1 MB)
- Requires careful partition/key design upfront — repartitioning later has real costs

### 5. What are common real-world use cases of Kafka?

- Real-time analytics and dashboards
- Log aggregation and centralized logging
- Event-driven microservices communication
- [Change Data Capture](https://www.geeksforgeeks.org/system-design/change-data-capture-cdc/) (CDC) pipelines from databases
- Stream processing (fraud detection, monitoring, alerting)
- Messaging backbone between systems (ETL/data integration)

### 6. What are the key components of Kafka?

The key components of Kafka include:

- ****Producer:**** Publishes messages to Kafka topics.
- ****Consumer:**** Subscribes to topics and processes the published messages.
- ****Broker:**** A Kafka server that stores and manages topics.
- ****Topic:**** A category or feed name to which records are published.
- ****Partition:**** Topics are divided into partitions for scalability.

### 7. What is a topic in Kafka?

- A [topic](https://www.geeksforgeeks.org/java/apache-kafka-topics-using-cli/) in Kafka is a category or feed name to which records are published.
- Topics in Kafka are always multi-subscriber; that is, a topic can have zero, one, or many consumers that subscribe to the data written to it.
- Topics are split into partitions for improved scalability and parallel processing.

### 8. What is a partition in Kafka?

- A partition is an ordered, immutable sequence of records that is continually appended to.
- Each partition is a structured commit log, and records in the partitions are each assigned a sequential id number called the offset.
- Partitions allow Kafka to scale horizontally and provide parallel processing capabilities.

### 9. What is a broker in Kafka?

A [broker](https://www.geeksforgeeks.org/apache-kafka/monitoring-kafka-with-kafka-broker-api-versionssh/) is a Kafka server that runs in a Kafka cluster. It receives messages from producers, assigns offsets to them, and commits the messages to storage on disk. It also services consumers, responding to fetch requests for partitions and responding with the messages that have been published.

### 10. What is the role of ZooKeeper in Kafka?

- Historically, [ZooKeeper](https://www.geeksforgeeks.org/java/what-is-apache-zookeeper/) managed Kafka cluster metadata — broker registration, topic/partition configuration, and controller election.
- As of Apache Kafka 4.0, ZooKeeper support has been completely removed.
- Kafka now runs exclusively on KRaft (Kafka Raft), its own Raft-based consensus protocol, introduced via KIP-500 and declared production-ready in Kafka 3.3.

### 11. What is KRaft, and why did Kafka move away from ZooKeeper?

[KRaft](https://www.geeksforgeeks.org/advance-java/spring-boot-integration-with-kafka/) (Kafka Raft) is Kafka's built-in metadata-management protocol. Instead of relying on an external ZooKeeper ensemble, a set of dedicated controller nodes form a Raft quorum and store cluster metadata in an internal, replicated Kafka topic called `__cluster_metadata`.

- Millisecond-level metadata propagation vs. seconds with ZooKeeper
- Faster controller failover (sub-second)
- Support for far more partitions per cluster (into the millions)
- One system to operate and secure instead of two
- Simpler deployment — no separate ZooKeeper ensemble to size, patch, and monitor

### 12. What is the `__cluster_metadata` topic?

- It's an internal, system-level Kafka topic that the KRaft controller quorum uses to store and replicate cluster metadata via the Raft consensus algorithm.
- Because it's just a Kafka topic, standard Kafka replication, log compaction, and snapshotting apply to metadata recovery too.

### 13. What is the role of the controller in a Kafka cluster?

The controller is responsible for electing partition leaders, managing partition distribution across brokers, and handling administrative operations (creating/deleting topics, etc.).

- ****Pre-KRaft (ZooKeeper mode):**** One broker acted as controller; ZooKeeper helped elect a new controller from among brokers if it failed.
- ****KRaft mode (current, mandatory since Kafka 4.0):**** A dedicated quorum of controller nodes (which can be combined with broker roles in smaller clusters) manages this via Raft consensus — there's an active controller and standby controllers ready to take over almost instantly, without any external system.

### 14. How does Kafka ensure fault tolerance?

- Kafka ensures fault tolerance through data replication.
- Each partition is replicated across a configurable number of servers for fault tolerance.
- One of the servers is designated as the leader, which handles all read and write requests for the partition, while the others are followers that passively replicate the leader.

### 15. How does Kafka ensure high availability?

- Replication of partitions across multiple brokers
- Automatic leader election when a broker fails (handled by the KRaft controller quorum)
- Ability to add brokers to a cluster without downtime
- Configurable number of in-sync replicas for durability

### 16. What happens when a Kafka broker goes down?

1. The KRaft controller detects the broker is unreachable (missed heartbeats).
2. For every partition where the failed broker was the leader, the controller elects a new leader from the partition's in-sync replicas (ISR).
3. Producers/consumers refresh their metadata and start talking to the new leader.
4. When the failed broker comes back online, it rejoins as a follower and catches up by replicating from the current leader.
5. If `unclean.leader.election.enable=false` (the safe default), a partition with no in-sync replica left will stay unavailable rather than risk data loss by electing an out-of-sync replica.

### 17. What is `broker.rack`, and why does rack-aware replica placement matter?

- [`broker.rack`](https://www.geeksforgeeks.org/java/apache-kafka-topics-using-cli/) lets you tag each broker with a rack/availability-zone identifier.
- Kafka's rack-aware replica assignment then tries to spread a partition's replicas across different racks/zones rather than clustering them on brokers in the same physical location.
- This protects against correlated failures — e.g., losing an entire data-center rack or cloud AZ shouldn't take out every replica of a partition.

### 18. What is the purpose of the Kafka broker's controlled shutdown?

Controlled shutdown lets a broker leave the cluster gracefully: it stops accepting new produce requests, completes in-flight produce/fetch requests, and transfers leadership of its partitions to other brokers in an orderly way — minimizing data loss and service disruption during planned maintenance.

### 19. How does Kafka support scalability?

- Topics can be partitioned across multiple brokers, allowing parallel processing.
- Consumers can be grouped to read multiple partitions simultaneously. Brokers can be added to a cluster to increase capacity, and the cluster can scale without downtime.

### 20. How does Kafka handle data replication?

- Kafka replicates data by maintaining multiple copies of each partition across different brokers.
- One broker leads the partition; the others follow and replicate its data. If a leader fails, a follower is promoted. Replica count is configurable per topic.

### 21. What is ISR (In-Sync Replicas), and how is it different from replication factor?

- Replication factor is simply how many total copies of a partition exist (leader + followers).
- [ISR](https://www.geeksforgeeks.org/apache-kafka/understanding-in-sync-replicas-isr-in-apache-kafka/) is the subset of those replicas that are currently fully caught up with the leader.
- A replica falls out of the ISR if it lags behind beyond replica.lag.time.max.ms.
- This distinction matters because durability guarantees (via acks=all) are based on the ISR, not the full replica count — a partition can have replication factor 3 but only 1 replica in the ISR if the other two are lagging.

### 22. What is `min.insync.replicas`, and how does it relate to `acks`?

- [`min.insync.replicas`](https://www.geeksforgeeks.org/apache-kafka/apache-kafka-producer-acknowledgement-and-min-insync-replicas/) is a topic/broker-level setting defining the minimum number of replicas that must be in the ISR for a write to succeed when the producer uses `acks=all`.
- For example, with replication factor 3 and `min.insync.replicas=2`, at least 2 replicas (leader + 1 follower) must acknowledge a write; if fewer than 2 are in sync, the producer receives an exception rather than a silently under-replicated write.
- This combination (`acks=all` + `min.insync.replicas=2`+) is the standard configuration for strong durability.

### 23. What is `unclean.leader.election.enable`?

- It controls what happens if all in-sync replicas for a partition become unavailable.
- If `true`, Kafka may elect an out-of-sync replica as the new leader to restore availability — at the cost of potential data loss.
- If `false` (recommended for most production systems), the partition stays offline until an in-sync replica becomes available again, prioritizing consistency over availability.

### 24. How does Kafka ensure data consistency?

- ****Replication:**** Each partition is replicated across multiple brokers.
- ****ISR:**** Only replicas caught up with the leader are eligible to be promoted.
- ****Acknowledgments:**** Producers can wait for leader-only or full-ISR acknowledgment.
- ****Atomic, ordered writes**** to a partition.
- ****Idempotent producers**** to prevent duplicate writes on retry.

### 25. What is log compaction in Kafka?

- [Log compaction](https://www.geeksforgeeks.org/dsa/introduction-to-log-structured-merge-lsm-tree/) gives finer-grained, per-record retention rather than coarse time-based retention.
- Kafka selectively removes older records that have a more recent update sharing the same key, guaranteeing the log retains at least the last known value for every key.

### 26. What is the role of the Kafka broker's log cleaner thread?

The log cleaner thread performs log compaction in the background, removing redundant records and keeping only the latest value per key for topics configured with `cleanup.policy=compact` — useful for maintaining a changelog or the latest state of an entity.

### 27. How does Kafka handle data retention?

- [Retention](https://www.geeksforgeeks.org/data-science/what-is-data-retention-and-how-does-it-decide-how-long-data-should-be-kept/) can be time-based (e.g., 7 days) or size-based (e.g., 1 GB per partition).
- Once the limit is reached, old messages are discarded.
- Kafka also supports log compaction for topics where only the latest value per key is needed.

### 28. What is the role of `log.segment.bytes`?

- It controls the maximum size of a single log segment file.
- When a segment reaches this size, a new one is created.
- This affects how often segments become eligible for deletion, retention granularity (Kafka can only delete whole segments, not individual records), and how much data must move during partition reassignment.

### 29. How does Kafka handle message deletion?

Kafka doesn't delete messages individually — it deletes whole log segments once they exceed the retention limit. For more fine-grained control over what's kept, log compaction is used instead.

### 30. What happens when a topic is deleted in Kafka?

1. The topic is marked for deletion in cluster metadata.
2. Kafka stops serving data for that topic.
3. The underlying log segments on disk are asynchronously deleted.

This asynchronous approach ensures topic deletion doesn't block or slow down other cluster operations.

### 31. How do you increase the number of partitions for a topic, and what are the implications? Can partitions be decreased?

[Partitions](https://www.geeksforgeeks.org/python/get-current-number-of-partitions-of-a-dataframe-pyspark/) can be increased using `kafka-topics.sh --alter --topic <name> --partitions <new-count>`. Implications:

- ****Key-based ordering breaks**** for existing keys, because adding partitions changes the hash-to-partition mapping — messages with the same key may now land in a different partition than before.
- More partitions increase replication/network overhead and open file handles on brokers.
- Consumer group rebalancing becomes more expensive with very high partition counts.

Partitions cannot be decreased once created — Kafka has no built-in way to safely remove a partition without losing data or breaking offset continuity. The common workaround is to create a new topic with the desired (lower) partition count and migrate data into it.

### 32. What are the message size limits in Kafka, and how are they configured?

- The default maximum message size is 1 MB, controlled by `message.max.bytes` on the broker and `max.request.size` on the producer.
- Both can be increased, but very large messages hurt performance and memory usage, so it's generally better to keep messages small and store large payloads externally (e.g., object storage) with a reference in the message.

### 33. How does Kafka handle message timestamps?

Kafka supports two timestamp types, configurable at the topic level:

- ****CreateTime**** — when the producer created the message.
- ****LogAppendTime**** — when the broker received the message. These are used for retention, log compaction, and time-based lookups by consumers.

### 34. What is the purpose of the Kafka producer API?

The [producer API](https://www.geeksforgeeks.org/python/introduction-to-confluent-kafka-python-producer/) publishes streams of records to Kafka topics. It handles partitioning, compression, load balancing across brokers, and retrying failed sends, and can be configured for different delivery guarantees.

### 35. What is the significance of the `acks` parameter, and how does it interact with durability?

[`acks` controls](https://www.geeksforgeeks.org/apache-kafka/apache-kafka-producer-acknowledgement-and-min-insync-replicas/) how many broker acknowledgments a producer requires before considering a write successful:

- **`**acks=0**`** — Fire and forget. No acknowledgment is awaited. Lowest latency, weakest durability — the message can be lost if the broker goes down.
- **`**acks=1**`** — The leader acknowledges the write. Better durability than 0, but the message can still be lost if the leader fails immediately after acknowledging, before followers replicate it.
- **`**acks=all**`** — The write is acknowledged only after all replicas currently in the ISR (bounded by `min.insync.replicas`) have it. Strongest durability, highest latency.

The choice is a direct trade-off between latency and durability, and `acks=all` combined with `min.insync.replicas ≥ 2` is the standard for production systems that can't tolerate data loss.

### 36. What is the Idempotent Producer in Kafka?

- An [idempotent producer](https://www.geeksforgeeks.org/apache-kafka/apache-kafka-idempotent-producer/) guarantees that retried sends don't create duplicate messages in a partition.
- It works by assigning each producer a unique Producer ID and a per-partition sequence number; the broker uses these to detect and discard duplicate retries. Enabled via `enable.idempotence=true`.

### 37. What is the difference between a round-robin partitioner and a key-based partitioner?

- A [round-robin partitioner](https://www.geeksforgeeks.org/apache-kafka/introduction-to-apache-kafka-producer/) cycles through partitions evenly regardless of key.
- A key-based partitioner hashes the message key to consistently route all messages with the same key to the same partition — essential for maintaining per-key ordering.

### 38. What is the purpose of the producer's `Partitioner` interface?

- It determines which partition a message goes to.
- The default implementation hashes the key (if present); custom partitioners can be implemented to control distribution based on business logic (e.g., routing high-priority messages to a dedicated partition).

### 39. How does Kafka handle message batching?

- Producers can group multiple messages destined for the same partition into a single batch instead of sending them individually, reducing network overhead and improving throughput.
- [Batch size](https://www.geeksforgeeks.org/java/apache-kafka-linger-ms-and-batch-size/) and linger time are both configurable.

### 40. What is the purpose of the producer's `linger.ms` parameter?

- [`linger.ms`](https://www.geeksforgeeks.org/java/apache-kafka-linger-ms-and-batch-size/) controls how long the producer waits for additional messages before sending a batch.
- Higher values produce larger batches and higher throughput at the cost of increased latency; the default of 0 sends messages as soon as possible.

### 41. What is the purpose of the producer's `max.block.ms` parameter?

- It bounds how long `send()` (and metadata calls) will block.
- If a record can't be sent within this window, the producer throws a `TimeoutException` instead of blocking indefinitely.

### 42. How does Kafka handle message delivery timeouts?

- Producers can be configured with a delivery timeout; if a message isn't acknowledged within that window, the send is considered failed (and may be retried, depending on config).
- On the consumer side, `max.poll.interval.ms` bounds how long a consumer can go without calling `poll()` before it's considered dead and a rebalance is triggered.

### 43. How does Kafka handle message compression?

Compression reduces the size of data transferred and stored. It's configured at the producer level, with support for gzip, snappy, lz4, and zstd.

### 44. How does the broker handle already-compressed messages?

[Brokers](https://www.geeksforgeeks.org/cloud-computing/what-is-a-kafka-broker/) can be configured to: pass through the message in its original compressed format, decompress on receipt and store uncompressed, or decompress and recompress with a potentially different algorithm. The choice trades off CPU usage against network bandwidth and storage.

### 45. What is Kafka's zero-copy mechanism, and how does it boost performance?

- Kafka achieves much of its high throughput through the zero-copy optimization: when serving a fetch request, the broker uses the OS's `sendfile()` system call to transfer data directly from the disk page cache to the network socket buffer, bypassing the application (JVM) layer entirely.
- This avoids extra copies between kernel space and user space and avoids unnecessary CPU cycles, which is a major reason Kafka can sustain very high throughput on commodity hardware.

### 46. What is the difference between a Kafka consumer and a consumer group?

- A consumer is an application that reads data from Kafka topics.
- A [consumer group](https://www.geeksforgeeks.org/java/apache-kafka-create-consumers-in-a-consumer-group-using-cli/) is a set of consumers that work together to consume data from one or more topics, with each partition delivered to exactly one consumer instance within a subscribing group — enabling parallel processing and load balancing.

### 47. What is the purpose of the offset in Kafka?

- The offset is a unique, sequential identifier of a record within a partition, marking the consumer's position in that partition.
- Kafka tracks the offset per partition, per consumer group, so each group can independently read from its own position — enabling both queue-style and pub-sub-style consumption.

### 48. How does Kafka store and manage consumer offsets?

- Kafka maintains offsets per consumer group, per partition, representing the consumer's position in the log.
- Consumers can commit offsets automatically (at a configurable interval) or manually.
- Offsets are stored in a special internal topic, `__consumer_offsets`, so consumers can resume from where they left off after a restart or failure.

### 49. What is the role of the group/consumer coordinator?

The group coordinator is a broker responsible for managing a consumer group's membership, assigning partitions to consumers within the group, tracking committed offsets, and triggering a rebalance whenever a consumer joins or leaves the group.

### 50. What is the purpose of the consumer's heartbeat thread?

- The [heartbeat](https://www.geeksforgeeks.org/system-design/what-are-heartbeat-messages/) thread periodically signals to the group coordinator that the consumer is still alive.
- If heartbeats stop for longer than the configured session timeout, the coordinator considers the consumer dead and triggers a rebalance to reassign its partitions.

### 51. What partition assignment strategies does Kafka support?

- ****Range**** — assigns contiguous partition ranges per topic to consumers; can lead to uneven load if a consumer subscribes to many topics.
- ****RoundRobin**** — spreads all partitions across all consumers evenly, ignoring topic boundaries.
- ****Sticky**** — aims for a balanced assignment while minimizing partition movement between rebalances.
- ****CooperativeSticky**** — an incremental version of Sticky that avoids a full stop-the-world rebalance; consumers keep processing their unaffected partitions while only the changed partitions are reassigned.

### 52. What is KIP-848, and how does it change consumer rebalancing?

- KIP-848 introduces a new, simplified consumer group rebalance protocol, which reached general availability in Kafka 4.0.
- Under the older ("eager") protocol, every consumer in a group had to give up all its partitions during a rebalance, even ones unaffected by the membership change, causing a brief group-wide pause.
- KIP-848 moves rebalancing logic mostly server-side and uses incremental, cooperative reassignment — only the partitions that actually need to move are revoked, so unaffected consumers keep processing without interruption.
- This makes rebalances in large consumer groups dramatically faster and less disruptive.

### 53. What happens during a consumer group rebalance?

1. Affected consumers stop consuming and commit their current offsets (fully or incrementally, depending on the protocol in use).
2. The group coordinator reassigns partitions among the remaining/new consumers.
3. Consumers resume from the last committed offset for their newly assigned partitions.

This ensures messages are consumed correctly even as the set of consumers changes, assuming proper offset management.

### 54. What is the difference between the consumer's `poll()` and `subscribe()` methods?

[`subscribe()`](https://www.geeksforgeeks.org/angular-js/difference-between-subscribe-and-async-pipe/) registers a consumer's interest in one or more topics but doesn't fetch any data. `poll()` actually fetches records published since the last fetch for the subscribed partitions, and is typically called in a loop to continuously consume data.

****subscribe()****

- Registers the consumer to one or more Kafka topics.
- Joins a consumer group and enables automatic partition assignment.
- Does not retrieve any messages.
- Called once during consumer initialization (or when subscriptions change).
- ****Goal:**** Specify which topics the consumer should read from.

****poll()****

- Retrieves records from the subscribed topics.
- Returns available messages as ConsumerRecords.
- Also sends heartbeats to the Kafka broker to keep the consumer active in the group.
- Called repeatedly inside a loop.
- ****Goal:**** Fetch and process messages continuously.

### 55. What is the role of the consumer's `position()` method?

`position()` returns the offset of the next record to be fetched for a given partition — useful, alongside `committed()`, for tracking how far a consumer is from its last committed position.

### 56. What is the difference between `commitSync()` and `commitAsync()`?

- `commitSync()` synchronously commits the latest offset from `poll()` and retries until it succeeds or hits a non-retriable error.
- `commitAsync()` commits asynchronously without retrying on failure — faster, but less reliable.
- The choice depends on the reliability-vs-performance needs of the application.

### 57. What does `auto.offset.reset` control?

It determines what a consumer does when there's no committed offset, or the committed offset no longer exists on the broker:

- **`**earliest**`** — reset to the earliest available offset.
- **`**latest**`** — reset to the latest offset.
- **`**none**`** — throw an exception instead of resetting.

### 58. What is consumer lag, and how is it monitored?

- Consumer lag is the difference between the offset of the last produced message and the offset of the last consumed message for a partition.
- It's monitored via tools like `kafka-consumer-groups.sh --describe` or the AdminClient API.
- High lag indicates the consumer isn't keeping up and may need scaling or performance tuning.

### 59. Does Kafka push or pull messages to consumers?

Kafka uses a ****pull model****: consumers request (fetch) messages from brokers rather than brokers pushing messages out. This gives consumers control over their own consumption rate and makes it easier to handle consumers falling behind, since data simply waits until it's fetched.

### 60. What consumption patterns does Kafka support?

- ****Queue-style:**** partitions are split across multiple consumers in the same group, so each message is processed by exactly one consumer.
- ****Publish-subscribe:**** each consumer is placed in its own consumer group, so every group independently receives all messages. These patterns can be combined to fit different use cases.

### 61. How does Kafka handle message serialization and deserialization?

- Kafka itself treats message data as opaque byte arrays.
- Producers and consumers are configured with serializers/deserializers for keys and values (String, Integer, Avro, etc.); custom (de)serializers can be implemented for complex objects.

### 62. What is the purpose of the Kafka Schema Registry?

[Schema Registry](https://www.geeksforgeeks.org/apache-kafka/kafka-architecture/) provides a [RESTful](https://www.geeksforgeeks.org/node-js/what-makes-an-api-restful/) service for storing and retrieving schemas (typically Avro, Protobuf, or JSON Schema) and enforces compatibility rules between producer and consumer schemas — critical for evolving data models over time without breaking consumers.

### 63. How does Kafka handle schema evolution?

- Kafka itself is schema-agnostic.
- Schema evolution is handled at the application layer, typically via a schema registry combined with a serialization format that supports compatible changes (like Avro), allowing schemas to change over time while maintaining backward/forward compatibility.

### 64. How is message versioning typically handled in Kafka?

Kafka has no built-in versioning mechanism, but applications commonly include a version field in the message schema, or rely on a schema registry to manage schema evolution and enforce compatibility rules across versions.

### 65. How does Kafka handle message key hashing?

When a key is present, Kafka hashes it (using the murmur2 algorithm by default) to consistently determine the partition — ensuring all messages with the same key go to the same partition, which matters for ordering and for stateful stream processing.

### 66. How is message validation typically implemented?

- Kafka doesn't validate message content beyond enforcing the maximum size.
- Validation logic is implemented at the producer (before sending) or consumer (after receiving) level, or in an intermediate Kafka Streams application for more complex validation/transformation.

### 67. How can message de-duplication be achieved with Kafka?

Kafka doesn't de-duplicate messages natively, but supports it via: idempotent producers (preventing duplicates from producer retries), exactly-once semantics in Kafka Streams, or application-level unique message IDs checked at the consumer.

### 68. What is the purpose of the Kafka Streams API?

[Kafka Streams](https://www.geeksforgeeks.org/devops/what-is-kafka-streams-api/) is a client library for building stream-processing applications and microservices using standard Java and Kafka clients — no separate processing cluster needed. It supports stateful operations, windowing, and joining streams and tables.

### 69. What is the difference between a Kafka consumer and a Kafka Streams application?

A Kafka Consumer and a Kafka Streams application both read data from Kafka topics, but they serve different purposes.

****Kafka Consumer****

- Reads messages from Kafka topics.
- Requires manual implementation of processing logic.
- Typically consumes data and sends it to an external system (database, API, etc.).
- Simple and lightweight.
- Best for message consumption and integration.
- ****Goal:**** Consume and process messages.

[****Kafka Streams Application****](https://www.geeksforgeeks.org/java/apache-kafka-streams-simple-word-count-example/)

- Built on top of the Kafka Streams library.
- Reads, transforms, filters, joins, aggregates, and writes data back to Kafka.
- Supports stateful and stateless stream processing.
- Handles scaling, fault tolerance, and state management automatically.
- Best for real-time data processing and analytics.
- ****Goal:**** Process event streams in real time.

### 70. What does the Kafka Streams DSL provide?

The DSL (Domain Specific Language) is a high-level API for expressing stream-processing logic — filtering, transforming, aggregating, joining — abstracting away low-level processing details so developers can focus on business logic.

### 71. What is the difference between a KStream and a KTable?

- ****KStream**** represents an unbounded, continuous stream of independent records (an event log) — every record is a new, distinct event.
- ****KTable**** represents a changelog — a table-like, continuously updated view where each new record for a key ****overwrites**** the previous value for that key (conceptually similar to log compaction).

A common pattern is streaming raw events as a `KStream` while maintaining derived aggregate state as a `KTable` (e.g., "current account balance" built from a stream of "transaction" events).

### 72. What types of windowing does Kafka Streams support?

Used for time-bounded aggregations/joins so results don't accumulate forever:

- ****Tumbling window**** — fixed-size, non-overlapping; each event belongs to exactly one window (e.g., total sales every hour).
- ****Hopping window**** — fixed-size but overlapping, advancing by an interval smaller than the window size (e.g., a moving average updated every 10 seconds over a 1-minute window); an event can appear in multiple windows.
- ****Sliding window**** — created dynamically around each event relative to other nearby events, rather than on a fixed clock; commonly used for join operations.
- ****Session window**** — dynamically sized based on periods of activity separated by a configurable inactivity gap (e.g., grouping a user's clicks into a "session").

### 73. What is a state store in Kafka Streams?

A state store is local storage (backed by ****RocksDB**** by default, or an in-memory store) that a Kafka Streams application uses to keep track of state needed for stateful operations like aggregations, joins, and windowing. State stores are also backed by a changelog topic in Kafka, so state can be restored automatically if an application instance fails and is restarted elsewhere.

### 74. What is the purpose of the Kafka Transactions API, and how does it relate to exactly-once semantics?

The Transactions API allows atomic writes across multiple topics/partitions, enabling ****exactly-once processing**** for applications that read, process, and write data back to Kafka. It's central to how Kafka Streams achieves exactly-once semantics.

### 75. How does Kafka achieve end-to-end exactly-once semantics (EOS)?

EOS is achieved by combining several mechanisms rather than a single feature:

1. ****Idempotent producer**** — prevents duplicate writes caused by producer retries.
2. ****Transactions (******`**transactional.id**`******)**** — group a "read-process-write" cycle (consumed offsets + produced output) into a single atomic unit, so either everything commits or nothing does.
3. **`**isolation.level=read_committed**`** on the consumer — ensures downstream consumers only see committed transactional writes, not ones that were later aborted.

Together, these guarantee that even if a producer, consumer, or stream processor crashes and restarts, each input record affects the output exactly once — no duplicates, no gaps.

### 76. What is the difference between Kafka Streams and Apache Flink?

- Kafka Streams is a lightweight client library, tightly integrated with Kafka, for building applications/microservices where input and output live in Kafka.
- Flink is a general-purpose, standalone distributed processing engine that can work with many different sources and sinks, not just Kafka.
- Flink generally offers lower latency and more advanced processing capabilities for complex operations; Kafka Streams is simpler to deploy since it requires no separate cluster.

### 77. What is ksqlDB, and how does it differ from Kafka Streams?

- [ksqlDB](https://www.geeksforgeeks.org/python/introduction-to-confluent-kafka-python-producer/) is a SQL-based streaming engine built on top of Kafka Streams.
- It lets you express stream-processing logic (filtering, aggregating, joining, windowing) using [SQL](https://www.geeksforgeeks.org/sql/what-is-sql/)-like queries instead of writing Java/Scala code, and runs as its own server rather than being embedded in your application.
- It's a good fit for teams that want fast, declarative stream processing without writing a custom Streams application.

### 78. What is the purpose of the Kafka Connect API?

Kafka Connect scalably and reliably streams data between Kafka and other systems (databases, key-value stores, search indexes, file systems) by defining reusable source and sink connectors, without writing custom producer/consumer code.

### 79. What is Kafka MirrorMaker, and how does it handle cross-datacenter replication?

- [MirrorMaker](https://www.geeksforgeeks.org/apache-kafka/kafka-mirrormaker-management-via-cli/) replicates data between Kafka clusters — including across data centers — by consuming from a source cluster and producing to a target cluster.
- Common uses include disaster recovery, aggregating data from multiple regions into a central cluster, and cluster migration.

### 80. What is the purpose of the Kafka AdminClient API?

AdminClient provides administrative operations for managing and inspecting topics, brokers, configurations, and ACLs programmatically — creating/deleting/describing topics, managing quotas, and retrieving cluster information.

### 81. How do you create a topic using the Kafka CLI?

> bin/kafka-topics.sh --create \
> 
> --topic my-topic \
> 
> --bootstrap-server localhost:9092 \
> 
> --partitions 3 \
> 
> --replication-factor 2

### 82. How do you list and describe topics using the Kafka CLI?

> # List all topics
> 
> bin/kafka-topics.sh --list --bootstrap-server localhost:9092
> 
> # Describe a specific topic (partitions, leader, ISR, replicas)
> 
> bin/kafka-topics.sh --describe --topic my-topic --bootstrap-server localhost:9092

### 83. How do you alter the number of partitions for a topic?

> bin/kafka-topics.sh --alter \
> 
> --topic my-topic \
> 
> --bootstrap-server localhost:9092 \
> 
> --partitions 6

### 84. How do you produce and consume messages from the command line?

> # Console producer
> 
> bin/kafka-console-producer.sh --broker-list localhost:9092 --topic my-topic
> 
> # Console consumer (from the beginning of the topic)
> 
> bin/kafka-console-consumer.sh --topic my-topic --from-beginning --bootstrap-server localhost:9092

### 85. How do you check a consumer group's lag from the CLI?

> bin/kafka-consumer-groups.sh --describe \
> 
> --group my-consumer-group \
> 
> --bootstrap-server localhost:9092

### 86. How do you delete a topic from the CLI?

> bin/kafka-topics.sh --delete --topic my-topic --bootstrap-server localhost:9092

### 87. How do you monitor a Kafka cluster in production?

- JMX metrics exposed by brokers, producers, and consumers (broker throughput, under-replicated partitions, request latency, etc.)
- [Prometheus](https://www.geeksforgeeks.org/devops/prometheus-monitoring/) + [Grafana,](https://www.geeksforgeeks.org/devops/how-to-use-grafana-to-visualize-kubernetes-metrics/) typically scraping JMX via the JMX Exporter, for dashboards and alerting
- `kafka-consumer-groups.sh --describe` or dedicated lag-monitoring tools for consumer lag
- Cruise Control for automated partition rebalancing and broker load monitoring
- Key metrics to watch: under-replicated partitions, ISR shrink/expand rate, request queue time, consumer lag, and controller failover count

### 88. What is the purpose of the Kafka Quota API?

[Quotas](https://www.geeksforgeeks.org/linux-unix/how-to-use-user-quotas-in-linux/) prevent a single client from monopolizing broker resources by limiting produce/fetch request rates on a per-client or per-user basis — ensuring fair resource allocation and protecting against denial-of-service scenarios.

### 89. What security mechanisms does Kafka provide?

- ****Encryption in transit (SSL/TLS)**** — encrypts data between clients and brokers, and between brokers.
- ****Authentication (SASL)**** — mechanisms like SASL/PLAIN, SASL/SCRAM, and SASL/GSSAPI (Kerberos) verify client and broker identity.
- ****Authorization (ACLs)**** — fine-grained access control lists define which principals can produce to, consume from, or administer specific topics/groups.
- ****Quotas**** — protect against resource exhaustion.

### 90. How does Kafka handle message ordering? __(__

- Kafka guarantees ordering only within a single partition: messages sent to a specific partition are appended, and read back, in the exact order they were written.
- There is no ordering guarantee across partitions of a topic.
- For use cases needing strict global ordering, a single-partition topic can be used, but that limits scalability — the more common approach is to pick a partition key that routes related messages to the same partition, preserving the ordering that actually matters.

### 91. What is the difference between the different Kafka message delivery guarantees?

- ****At most once**** — messages may be lost but are never redelivered (no retries).
- ****At least once**** — messages are never lost but may be redelivered/duplicated (default with retries enabled, no idempotence).
- ****Exactly once**** — each message affects the result exactly once, achieved via idempotent producers + transactions + `read_committed` isolation .

Comment

[

K



](https://www.geeksforgeeks.org/user/kartik/)

[kartik](https://www.geeksforgeeks.org/user/kartik/)