<!-- Shared abbreviation definitions — include at the bottom of any page via:
     --8<-- "_abbreviations.md"
-->

<!-- CAP / Consistency models -->
[CAP]: CAP Theorem: In a distributed system you can guarantee at most two of — Consistency, Availability, Partition Tolerance — simultaneously.
*[PACELC]: Extension of CAP: if Partition → choose Availability or Consistency; Else (normal ops) → choose Latency or Consistency.
*[CP]: CP System: Chooses Consistency over Availability during a network partition — returns an error rather than stale data.
*[AP]: AP System: Chooses Availability over Consistency during a network partition — returns potentially stale data rather than an error.
*[CA]: CA System: Requires no network partitions — impossible in real distributed systems; only applies to single-node databases.

<!-- Consistency properties -->
*[Consistency]: Every read receives the most recent write or an error; no client sees stale data.
*[Availability]: Every request receives a non-error response, though it may not reflect the latest write.
*[Partition Tolerance]: The system continues to operate despite network partitions (message loss / delay between nodes).

<!-- Consensus & quorum -->
*[Raft]: Distributed consensus algorithm used by etcd and CockroachDB; elects a leader to serialise writes.
*[Paxos]: Classic distributed consensus algorithm; basis for many CP databases (Chubby, Spanner).
*[quorum]: A majority of nodes (N/2 + 1) that must agree before a read or write is considered successful.
*[QUORUM]: Cassandra consistency level that waits for a majority (N/2 + 1) of replica acknowledgements.

<!-- Conflict resolution -->
*[LWW]: Last Write Wins — conflict resolution strategy where the write with the latest timestamp is kept; risks data loss on concurrent writes.
*[CRDT]: Conflict-free Replicated Data Type — a data structure mathematically guaranteed to merge concurrent edits without conflicts.
*[Vector Clocks]: Per-node counters attached to each write to track causality and detect concurrent (conflicting) updates.
*[Vector clocks]: Per-node counters attached to each write to track causality and detect concurrent (conflicting) updates.
*[vector clocks]: Per-node counters attached to each write to track causality and detect concurrent (conflicting) updates.

<!-- Common systems -->
*[etcd]: Strongly-consistent distributed key-value store using Raft consensus; backing store for Kubernetes configuration.
*[Zookeeper]: CP coordination service using ZAB consensus; used for leader election, distributed locks, and configuration.
*[Cassandra]: AP distributed wide-column store with tunable consistency (ONE → QUORUM → ALL) and no single point of failure.
*[DynamoDB]: AWS managed key-value store; AP (eventually consistent) by default with optional per-request strong consistency.
*[HBase]: CP distributed column-family store built on HDFS; strongly consistent reads and writes within a single row.

<!-- Consistency levels (Cassandra) -->
*[ONE]: Cassandra consistency level: wait for acknowledgement from exactly one replica — fastest but may read stale data.
*[ALL]: Cassandra consistency level: wait for acknowledgement from every replica — slowest but highest consistency guarantee.
*[LOCAL_QUORUM]: Cassandra consistency level: quorum within the local datacenter only — low cross-datacenter latency with local consistency.

<!-- DDD / Microservices terms that appear on the CAP page -->
*[ACID]: Atomicity, Consistency, Isolation, Durability — the four properties that guarantee reliable relational database transactions.
*[BASE]: Basically Available, Soft-state, Eventually consistent — the relaxed consistency model used by most AP distributed systems.
*[SLA]: Service Level Agreement — contractual commitment on uptime/performance between provider and customer.
*[SLO]: Service Level Objective — internal target (e.g. 99.9% uptime) used to drive engineering reliability decisions.

