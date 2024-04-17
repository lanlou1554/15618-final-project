# Milestone Report - Parallel Particle-In-Cell Method

Yuanxin Cao, Lan Lou

Andrew ID: yuanxinc, lanlou

## URL

[https://lanlou1554.github.io/15618-final-project/](https://lanlou1554.github.io/15618-final-project/)

## Setting Changes

In the original setting, the charged particles are initially positioned onto an irregular 3D mesh, and each particle can impose a Coulomb force on other particles within a certain distance. For every iteration, we should calculate the particle movement (i.e. their new positions) based on the Coulomb force. However, this original setting has some problems.

Actually it is not consistent with the real physical simulation model. We study the popular PIC simulation algorithms and find the PIC loop can usually be described as [1]:

1. Interpolating the electromagnetic fields at the particle positions.
2. Computing the new particle velocities and positions.
3. Computing the new electromagnetic fields on the grid according to the positions of the charged particles.

Introducing the electromagnetic field is not only closer to the real world, but also decreases the time complexity of serial PIC implementation. By limiting the grid length, the time complexity can be easily controlled compared to the original method when there are a lot of charged particles.

Also, the original algorithm which contains a double for loop of all the particles is too easy to parallelize because there is hardly any time complexity optimization. So, we decided to change the original setting to the PIC loop described above.

For parallelism details, we decide to further simulate the realistic scenario, with the combination of OpenMP and MPI. Specifically, the setting is that we have several cores, and their memory spaces are distributed, so the communication between cores can only be achieved via MPI. But within one core, we can have multiple threads sharing the same memory, so we can use OpenMP to achieve parallelism within single core.

Here are 2 ways to do the parallelism: parallelize by charged particles and parallelize by electromagnetic field grids. We could explore the trade-offs between these 2 methods after we implement both methods.

## Schedule

**4.8-4.16: Serial Algorithm Code and Basic Parallelism via OpenMP (Done)**

- Yuanxin, Lan: Implement the serial version of the modified 3D PIC algorithm.
- Yuanxin, Lan: Implement the basic OpenMP parallel version. When moving particles, we parallelize by particles; while when updating the electromagnetic field grid, we parallelize by grids.

**4.17-4.21: Basic OpenMP and MPI Parallelism**

+ Yuanxin: Basic OpenMP parallel implementation, incluing parallelize by particles and parallelize by grids.
+ Lan: Basic MPI parallel implementation, incluing parallelize by particles and parallelize by grids.

**4.22-4.25: Deep Performance Analysis and Parallel Mehhod Improvement**

+ Yuanxin: Deep analysis for OpenMP parallel implementation, and improve it.
+ Lan: Deep analysis for MPI parallel implementation, and improve it.
+ If time permits, we also want to explore MPI task stealing.

**4.26-4.29: Explore Faster Serial PIC and Its Parallelism**

+ Lan: Implement faster serial PIC algorithm, using a hashmap to record the charged particles for one certain grid point, so we don't need to traverse all the particles.
+ Yuanxin: Try to better parallelize this faster PIC algorithm, maybe including fine-grained/lock-free implementation of this hashmap.

**4.30-5.5: Documentation, Poster Event Slides**

+ Yuanxin: Conduct sensitive studies for OpenMP and finish final report.
+ Lan:  Conduct sensitive studies for MPI and finish poster event slides.

## Completed Work

1. Explore the complex real physical simulation for PIC algorithm.
2. Implement serial PIC algorithm based on reasonable physical equations.
3. Implement basic OpenMP parallel version, including parallelize by particles and parallelize by grids.

## Goals and Deliverables

### Goal

#### 75% Goal

- Complete the basic algorithm of 3D PIC (particle-in-cell) simulation, and the initial positions of charged particles are scattered above an irregular 3D mesh.
- Explore basic parallel methods of OpenMP and MPI to achieve preliminary speedup performance.

#### 100% Goal

- Analyze statistics like arithmetic intensity, cache hit rate, communication and synchronization time, and workload per core, and try to improve the parallel performance via OpenMP and MPI.
- The speedup performance of OpenMP and MPI version should be at least 4x under 8 cores in GHC machines.

#### 125% Goal

- Implement faster serial PIC algorithm, using a hashmap to record the charged particles for one certain grid point, so we don't need to traverse all the particles.
- Try to better parallelize this faster PIC algorithm, maybe including fine-grained/lock-free implementation of this hashmap.

### Deliverables

- Performance results under different cores in GHC and PSC, also under different initial position and velocity settings.
- A deep analysis of the above results, including the things we can improve in the future.

## Poster Session

1. A graph including performance results under different cores in GHC and PSC, also under different initial position and velocity settings.
2. A graph including cache misses under different cores in GHC and PSC, also under different initial position and velocity settings.
3. Sensitive study result of different initial position and velocity settings, also of different particle and grid numbers.
4. A brief summary of the above results' deep analysis.

## Preliminary Results

Our current parallel version is as follows: when we move particles, we parallelize by particles; when we update the electromagnetic field grid, we parallelize by grids. The table below shows the result of our experiment. We set the grid size as 16 * 16 * 16 and the number of charged particles as 15978.

| Thread Number | Time (s) | Particle Moving Rate (particles moved/s) | Speedup |
| ------------- | -------- | ---------------------------------------- | ------- |
| 1             | 1.713    | 0.093                                    | 1       |
| 2             | 1.024    | 0.156                                    | 1.672   |
| 4             | 0.559    | 0.285                                    | 3.064   |
| 8             | 0.322    | 0.495                                    | 5.319   |

As we can see from the table, the speedup is not ideal. The charged particles are initialized in a uniform distribution so that each worker will get roughly the same work. In other words, currently we have a good load balance among workers thanks to the way of initialization. We thereby need to figure out the potential reasons for the phenomenon and do some optimizations accordingly in the future.

We only explore this setting currently, and we will explore different experiment settings in the future.

## Remaining Unknowns

We need to explore the way to achieve a more significant speedup. This includes the determination of the charged particles' initial positions. If we initialize the positions in a very skewed way (e.g. almost all particles reside in the upper left corner of the whole space), we need to do more sophisticated work assignments to achieve load balance, and thus achieve more significant speedup.

## References

[1] Smilei PIC algorithms https://smileipic.github.io/Smilei/Understand/algorithms.html
