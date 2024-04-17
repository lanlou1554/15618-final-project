# Parallel Particle-In-Cell Method

Yuanxin Cao, Lan Lou

Andrew ID: yuanxinc, lanlou

## URL

[https://lanlou1554.github.io/15618-final-project/](https://lanlou1554.github.io/15618-final-project/)

## Summary

The objective of this project is to implement the Particle-in-Cell (PIC) algorithm [1] in a 3D space and parallelize it using the OpenMP library. The parallelization aims to enhance the performance of the PIC simulation, enabling the simulation of larger systems and achieving faster simulation times.

## Background

In the PIC algorithm, the motion of charged particles is tracked through the Coulomb force between different charged particles. Each particle has attributes such as position and velocity, which evolve over time.

To calculate the effect of particles in a 3D space, the particle charges are interpolated onto a irregular 3D mesh. [2] Similarly, the Coulomb forces computed on the mesh are interpolated back to the particle positions to update their velocities.

To parallelize this algorithm using OpenMP, we need to divide the work evenly between the workers. Each worker should calculate the Coulomb forces for a number of charged particles, and then update the positions and the velocities of the charged particles accordingly.


## The Challenge

The first challenge of the project is to correctly model the irregular 3D mesh and the movement of the charged particles. We need to come up with a data representation that can represent different irregular meshes as well as the moving charged particles. We also need to decide how to set the distance within which we calculate the Coulomb force for a given charged particle, so that the algorithm is not too computationally heavy while not losing its simulation accuracy.

In terms of parallelization, the challenge is the workload balancing for each worker in OpenMP. Since the shape of the mesh is irregular, and given the fact that each charged particle should compute the Coulomb force with regard to an area of neighbor charged particles, there could be large number of false sharing if we don't assign work carefully, taking into account the memory access patterns of the algorithms. We may need to do semi-static scheduling because the charged particles are moving and we need to minimize the false sharing of the cache.

## Resources

We may take some inspirations from these repositories:
1. [Parallel Research Tools Kernels](https://github.com/ParRes/Kernels)
2. [Smilei](https://github.com/SmileiPIC/Smilei)
3. [ModeRepository](https://www.cs.cmu.edu/~kmcrane/Projects/ModelRepository/)

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

[1] F.H. Harlow (1955). "A Machine Calculation Method for Hydrodynamic Problems". Los Alamos Scientific Laboratory report LAMS-1956.
[2] Dawson, J.M. (1983). "Particle simulation of plasmas". Reviews of Modern Physics. 55 (2): 403–447.
