# Parallel Cuckoo Hashing

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

- Complete the implementation of a coarse-locked version and a fine-locked/lock-free version of cuckoo hashing with testing and debugging to ensure correctness and concurrency safety.
- The fine-locked/lock-free version should achieve at least 2x speedup over the coarse-locked version.

#### 100% Goal

- Complete the implementation of a coarse-locked version and a fine-locked/lock-free version of cuckoo hashing with testing and debugging to ensure correctness and concurrency safety.
- The fine-locked/lock-free version should achieve at least 5x speedup over the coarse-locked version. The coarse-locked version suffers from high contention, as the entire hash table is protected by a single lock. By implementing fine-grained locking or lock-free techniques, we can reduce contention by allowing multiple threads to access different parts of the hash table concurrently. This reduction in contention can result in significant performance improvements.

#### 125% Goal

- Complete the implementation of three parallel versions: coarse-locked, fine-locked, and lock-free. Each version should be with testing and debugging to ensure correctness and concurrency safety.
- The lock-free version should achieve at least 10x speedup over the coarse-locked version. The rationale should be the same as above.

### System Capabilities

- The system will offer O(1) complexity for lookups and deletions, as only one location per hash table needs to be examined. This ensures fast retrieval and removal of key-value pairs.
- The system will support concurrent access by multiple reader and writer threads. It will handle synchronization to prevent data races and ensure thread safety while allowing efficient parallelism.

### Performance Goals

- The system aims to achieve high throughput for insertions, lookups, and deletions under various workloads. It will strive to process a large number of operations per unit time to meet the demands of real-world applications.
- The system aims to minimize latency for individual operations, ensuring quick response times for user queries and requests.

## Platform Choice

We plan to use Rust as our programming language for the following reasons:

1. Rust's ownership system and strict compiler guarantees memory safety without the need for garbage collection. This is crucial for parallel programming, where data races and memory errors can be challenging to debug (especially in C and C++).

2. Rust provides powerful concurrency primitives out of the box, such as threads, mutexes, atomic types, and channels. These primitives enable developers to implement various parallel algorithms efficiently.

3. Rust's performance is comparable to C and C++, making it well-suited for high-performance computing tasks like parallel hashing. Additionally, Rust's zero-cost abstractions and control over memory layout allow for fine-tuning performance-critical code.

## Schedule

### Week 1: Basic Code Structure and Data Structure

- Set up a version control system (e.g., Git) and project repository.
- Develop basic data structures for the hash table and hash functions.

### Week 2: Coarse-Locked Version Implementation

- Implement the coarse-locked parallel version of cuckoo hashing in Rust. Refine data structures to support coarse-grained locks.
- Write unit tests to ensure correctness of basic functionalities.

<!-- ### Week 3: Fine-Locked Version Implementation

- Implement the fine-locked parallel version of cuckoo hashing in Rust.
- Refine data structures to support finer-grained locking at the bucket or cell level.
- Incorporate more sophisticated concurrency control mechanisms.
- Write additional unit tests and integration tests to validate the implementation. -->

<!-- ### Week 4: Lock-Free Version Implementation

- Implement the lock-free parallel version of cuckoo hashing in Rust.
- Utilize atomic operations and synchronization primitives to achieve thread safety without explicit locking.
- Conduct thorough testing and debugging to ensure correctness and concurrency safety. -->

### Week 3-4: Fine-Locked (Lock-Free) Version Implementation

- Implement the fine-locked (lock-free) parallel version of cuckoo hashing in Rust.
<!-- - Utilize atomic operations and synchronization primitives to achieve thread safety without explicit locking. -->
- Conduct thorough testing and debugging to ensure correctness and concurrency safety.

### Week 5: Benchmarking Setup and Execution

- Design benchmarking workloads to evaluate the performance of each parallel version.
- Set up benchmarking infrastructure and tools (e.g., Rust's benchmark crate, external benchmarking libraries).
- Run benchmark tests. Collect and analyze benchmarking data to measure throughput, latency, scalability, and other performance metrics.

### Week 6: Documentation, Analysis, and Finalization

- Document the implementation details, design decisions, and benchmarking results in a comprehensive report.
- Analyze the performance data to identify strengths, weaknesses, and trade-offs of each parallel version.
- Summarize key findings, lessons learned, and recommendations for future improvements.
- Prepare presentation materials for the parallelism competition or any project presentations.

## Reference

[1] Bin Fan, David G. Andersen, and Michael Kaminsky. 2013. MemC3: compact and concurrent MemCache with dumber caching and smarter hashing. In Proceedings of the 10th USENIX conference on Networked Systems Design and Implementation (nsdi'13). USENIX Association, USA, 371–384.

[2] Xiaozhou Li, David G. Andersen, Michael Kaminsky, and Michael J. Freedman. 2014. Algorithmic improvements for fast concurrent Cuckoo hashing. In Proceedings of the Ninth European Conference on Computer Systems (EuroSys '14). Association for Computing Machinery, New York, NY, USA, Article 27, 1–14. https://doi.org/10.1145/2592798.2592820

[3] N. Nguyen and P. Tsigas, "Lock-Free Cuckoo Hashing," 2014 IEEE 34th International Conference on Distributed Computing Systems, Madrid, Spain, 2014, pp. 627-636, doi: 10.1109/ICDCS.2014.70.