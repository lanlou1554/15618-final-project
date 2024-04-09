# Parallel Particle-In-Cell Method

Yuanxin Cao, Lan Lou

Andrew ID: yuanxinc, lanlou

## URL

[https://lanlou1554.github.io/15618-final-project/](https://lanlou1554.github.io/15618-final-project/)

## Summary

We are going to implement the parallel cuckoo hashing in Rust, including coarse-locked, fine-locked/lock-free versions. We also will use suitable workloads to benchmark these three versions.

## Background

Cuckoo hashing is a hashing algorithms offering efficient insertion, lookup, and deletion operations. It utilizes multiple hash functions to determine several potential locations within the hash table for storing a given key-value pair. Cuckoo hashing uses these hash functions to check multiple locations, and try to pick an empty slot.

If all designated locations are occupied, the algorithm employs a unique eviction policy to make room for the new element. This involves removing an existing element from one of the occupied locations and re-inserting it using its alternative hash function. This process continues recursively until an empty slot is found or a predefined maximum number of iterations is reached.

The key advantage of cuckoo hashing is its O(1) complexity for lookups and deletions since only one location per hash table is examined. However, insertions can potentially lead to cascading evictions, impacting performance. In short, cuckoo hashing is suitable for read-intensive workloads.

In this project, we will implement parallel version for cuckoo hashing, supporting concurrent reader and writer threads. Also, we will benchmark there different versions under different workloads, to better explore their pros and cons.

## The Challenge

Parallelizing cuckoo hashing poses significant challenges due to the nature of concurrent access to the hash table. One major obstacle is the risk of deadlock, particularly in scenarios where multiple writers are simultaneously attempting to insert elements into the hash table. During insertion, a new element may need to traverse multiple buckets until it finds an available spot. However, predicting exactly which buckets will be affected is difficult since each move depends on the previous one. This unpredictability makes the application of standard deadlock prevention techniques infeasible, such as acquiring all necessary locks in advance. Without a clear understanding of which locks will be needed, ensuring that concurrent insertions proceed smoothly without risking deadlock becomes a daunting task.

Another significant challenge in parallel cuckoo hashing is the occurrence of false misses, especially in situations involving both readers and writers. When a key is in the process of being moved from its original bucket to a new one, it becomes temporarily inaccessible from both locations. During this transitional period, if a reader attempts to look up the key, it may mistakenly conclude that the key does not exist in the hash table, leading to incorrect lookup results. For instance, consider a scenario where key 'b' is being moved from bucket 4 to bucket 1. At the moment when 'b' is neither in its original nor its new bucket, any attempt to look up 'b' would result in a false miss. Addressing this challenge requires ensuring atomicity in insert operations to prevent readers from accessing keys in an inconsistent state.[1]

## Resources

We may take some inspirations from these papers:
1. MemC3: compact and concurrent MemCache with dumber caching and smarter hashing [1]
2. Algorithmic improvements for fast concurrent Cuckoo hashing [2]
3. Lock-Free Cuckoo Hashing [3]

## Goals and Deliverables

### Goals

#### 75% Goal

- Complete the basic algorithm of 3D PIC (particle-in-cell) simulation, and the initial positions of charged particles are scattered above an irregular 3D mesh. Also, the charged particles can influence each other's position and velocity in every step.
- Explore basic parallel methods of OpenMP to achieve preliminary speedup performance.

#### 100% Goal

- Analyze statistics like arithmetic intensity, cache hit rate, communication and synchronization time, and workload per core, and try to improve the parallel performance via OpenMP.
- The speedup performance of OpenMP version should be at least 4x under 8 cores in GHC machines.

#### 125% Goal

Here are 2 different directions:

1. Explore MPI parallel methods:
- Use MPI to parallelize PIC problem, and also try to improve the performance by collecting and analyzing the above statistics.
- Benchmark MPI method with OpenMP method, and then analyze the advantages/disadvantages of each method.
2. Explore more challenging settings:
- Extend the original algorithm by adding other 3D objects which can give some force to change its velocity direction.
- Find better ways to improve parallel performance under this new setting.

### Deliverables

- Performance results under different cores in GHC and PSC, also under different initial position and velocity settings.
- A deep analysis of the above results, including the things we can improve in the future.
- A quick demo including the 3D visualization of our PIC results under different initial positions and velocities, also with the display of its parallel performance.

## Platform Choice

We plan to use C++ as our programming language because it has many easy-to-use parallel libraries like OpenMP and MPI. Also, we will test our performance in GHC and PSC machines since they have many cores and are easy to access.

## Schedule

### Week 1: Algorithm Code and Basic OpenMP Parallel Implementation (4.8-4.14)

- Implement the serial version of 3D PIC algorithm.
- Try basic methods to parallelize this method via OpenMP.
  
### Week 2: Deep Performance Analysis and Parallel Method Improvement (4.15-4.21)

- Analyze the workload balance, cache hit rate, synchronous and communication time.
- Find a better way to improve the parallel performance.

### Week 3: Explore MPI or New Setting (4.22-4.28)
- Try to finish our 125% goal, including exploring MPI parallel method or parallelize PIC in a more challenging setting.

### Week 4: Documentation, Demo, and Finalization (4.29-5.5)

- Document the implementation details, design decisions, and benchmarking results in a comprehensive report.
- Analyze the performance data to identify strengths, weaknesses, and trade-offs of past parallel version.
- Summarize key findings, lessons learned, and recommendations for future improvements.
- Prepare presentation materials for the parallelism competition or any project presentations.

## Reference
