# Parallel Cuckoo Hashing
Yuanxin Cao, Lan Lou
Andrew ID: yuanxinc, lanlou
### URL
[https://lanlou1554.github.io/15618-final-project/](https://lanlou1554.github.io/15618-final-project/)

### Summary
We are going to implement the parallel cuckoo hashing in Rust, including coarse-locked, fine-locked/lock-free versions. We also will use suitable workloads to benchmark these three versions.

### Background
Cuckoo hashing is a hashing algorithms offering efficient insertion, lookup, and deletion operations. It utilizes multiple hash functions to determine several potential locations within the hash table for storing a given key-value pair. Cuckoo hashing uses these hash functions to check multiple locations, and try to pick an empty slot.

If all designated locations are occupied, the algorithm employs a unique eviction policy to make room for the new element. This involves removing an existing element from one of the occupied locations and re-inserting it using its alternative hash function. This process continues recursively until an empty slot is found or a predefined maximum number of iterations is reached.

The key advantage of cuckoo hashing is its O(1) complexity for lookups and deletions since only one location per hash table is examined. However, insertions can potentially lead to cascading evictions, impacting performance. In short, cuckoo hashing is suitable for read-intensive workloads.

In this project, we will implement parallel version for cuckoo hashing, supporting concurrent reader and writer threads. Also, we will benchmark there different versions under different workloads, to better explore their pros and cons.

### The Challenge
Parallelizing cuckoo hashing poses significant challenges due to the nature of concurrent access to the hash table. One major obstacle is the risk of deadlock, particularly in scenarios where multiple writers are simultaneously attempting to insert elements into the hash table. During insertion, a new element may need to traverse multiple buckets until it finds an available spot. However, predicting exactly which buckets will be affected is difficult since each move depends on the previous one. This unpredictability makes the application of standard deadlock prevention techniques infeasible, such as acquiring all necessary locks in advance. Without a clear understanding of which locks will be needed, ensuring that concurrent insertions proceed smoothly without risking deadlock becomes a daunting task.

Another significant challenge in parallel cuckoo hashing is the occurrence of false misses, especially in situations involving both readers and writers. When a key is in the process of being moved from its original bucket to a new one, it becomes temporarily inaccessible from both locations. During this transitional period, if a reader attempts to look up the key, it may mistakenly conclude that the key does not exist in the hash table, leading to incorrect lookup results. For instance, consider a scenario where key 'b' is being moved from bucket 4 to bucket 1. At the moment when 'b' is neither in its original nor its new bucket, any attempt to look up 'b' would result in a false miss. Addressing this challenge requires ensuring atomicity in insert operations to prevent readers from accessing keys in an inconsistent state.[1]
### Resources
We may take some inspirations from these papers:
1. MemC3: compact and concurrent MemCache with dumber caching and smarter hashing [1]
2. Algorithmic improvements for fast concurrent Cuckoo hashing [2]
3. Lock-Free Cuckoo Hashing [3]

### Goals and Deliverables

### Platform Choice

### Schedule

### Reference
[1] Bin Fan, David G. Andersen, and Michael Kaminsky. 2013. MemC3: compact and concurrent MemCache with dumber caching and smarter hashing. In Proceedings of the 10th USENIX conference on Networked Systems Design and Implementation (nsdi'13). USENIX Association, USA, 371–384.

[2] Xiaozhou Li, David G. Andersen, Michael Kaminsky, and Michael J. Freedman. 2014. Algorithmic improvements for fast concurrent Cuckoo hashing. In Proceedings of the Ninth European Conference on Computer Systems (EuroSys '14). Association for Computing Machinery, New York, NY, USA, Article 27, 1–14. https://doi.org/10.1145/2592798.2592820

[3] N. Nguyen and P. Tsigas, "Lock-Free Cuckoo Hashing," 2014 IEEE 34th International Conference on Distributed Computing Systems, Madrid, Spain, 2014, pp. 627-636, doi: 10.1109/ICDCS.2014.70.

