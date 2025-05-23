---
layout: page
title: Dynamo DB - Effectiveness of Anti-Entropy and Gossip Protocol
description: A DynamoDB-inspired distributed key-value store that explores how gossip, anti-entropy, and quorum settings impact eventual consistency and fault recovery.
img: assets/img/ds.jpg
importance: 2
category: Graduate Studies
giscus_comments: false
---

[[Project Report]](https://satyachillale.github.io/assets/pdf/ds_proj_report.pdf) [[Code]](https://github.com/satyachillale/dynamo)

### Introduction

Inspired by Amazon’s DynamoDB, this project explores the trade-offs in building eventually consistent, highly available distributed systems. It implements core design principles like consistent hashing, sloppy quorums, gossip-based membership, hinted handoff, vector clock versioning, and Merkle tree-based anti-entropy. 
Through a series of experiments, we study the effectiveness of these protocols in maintaining consistency, reducing stale reads, and recovering from failures. The results highlight how periodic background repair can outperform static configurations—even in failure-free conditions.

### Introduction
Modern distributed systems are often built on the premise of eventual consistency, especially when prioritizing availability and partition tolerance. Amazon’s DynamoDB famously introduced a design that relaxes strict consistency guarantees in favor of system uptime and fault resilience. Inspired by Dynamo, this project replicates key mechanisms in a simplified environment to better understand their design trade-offs.

We implemented a decentralized key-value store with tunable consistency via configurable read (R) and write (W) quorums, supporting a total of N replicas per key. Our system’s behavior under different failure and repair conditions was thoroughly evaluated through simulation, allowing us to draw empirical conclusions about consistency guarantees in real-world distributed storage.

### Implementation Setup

We built the entire system using Elixir, leveraging its actor model and lightweight concurrency (from the Erlang VM) to simulate distributed nodes and communication patterns. Below are key implementation components:

- Consistent Hashing: Maps keys to virtual nodes using SHA-1 hashes for even key distribution and seamless rebalancing during membership changes.

- Vector Clocks: Enable conflict detection by tracking causality across updates.

- Sloppy Quorums: Supports highly available reads/writes by allowing temporary deviations from the ideal replica set.

- Gossip Protocol: Periodically shares node membership and metadata updates across peers.

- Anti-Entropy: Uses Merkle trees to efficiently synchronize data across replicas.

- Hinted Handoff: Temporarily stores updates intended for unreachable nodes and delivers them when those nodes recover.

We developed a custom test suite that simulates node failures, message delays, drops, and reordering through a fuzzing pipeline. The emulation framework allows deterministic evaluation of distributed behaviors.

### Experimental Setup

All experiments were run on a simulated cluster of logical nodes (Elixir processes), each configured with N = 4 replicas per key. The system was subjected to 100 interleaved read/write operations for each experiment, targeting a small keyspace with optional message drop rates (1%, 5%, and 10%).

We evaluated three key aspects:

##### 1. Impact of Repair Protocols (Gossip & Anti-Entropy)

- Compared configurations with and without background repair.
- Simulated partial write failures and measured stale read rates under drop conditions.

##### 2. Effect of Quorum Settings (R/W)

- Tested combinations of (R, W): (1,4), (2,3), (3,4), and (4,1).
- Measured stale reads with all repair protocols disabled to isolate quorum effects.

##### 3. Synchronization Interval Tuning

- Varied background sync intervals (20ms, 50ms, 100ms, and 100,000ms) for gossip and anti-entropy.
- Fixed quorum at R=1, W=3 to maximize the chance of stale reads.

### Key Results

##### 1. Repair Protocols vs Message Drops

- Without repair, stale reads increased sharply with packet loss (up to **14% at 10% drops**).
- Enabling **gossip** and **anti-entropy** reduced stale reads to under **7%**, even under the worst conditions.
- Systems with failures **and** recovery protocols **outperformed** failure-free systems with no repair.

##### 2. R/W Quorum Trade-offs

- **R=1** resulted in approximately **15% stale reads**, even with **W=4**.
- **R=4** eliminated all stale reads.
- These findings validate that **R + W > N** improves consistency, but **does not guarantee** it when operations overlap in time.

##### 3. Synchronization Frequency Impact

- Higher sync frequency (**20ms**) led to the fewest stale reads (~**4%**).
- Disabling repair (**100,000ms**) more than **doubled** stale reads (~**12%**).
- This highlights the **importance of background repair**, even when quorum conditions are technically met.
