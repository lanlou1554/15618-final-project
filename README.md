# Parallel Particle-In-Cell Method

Yuanxin Cao, Lan Lou

Andrew ID: yuanxinc, lanlou

## URL

[https://lanlou1554.github.io/15618-final-project/](https://lanlou1554.github.io/15618-final-project/)

Project Proposal: https://github.com/lanlou1554/15618-final-project/blob/main/Project_Proposal.md

Milestone Report: https://github.com/lanlou1554/15618-final-project/blob/main/Milestone_Report.md

## Summary

The objective of this project is to implement the Particle-in-Cell (PIC) algorithm [1] in a 3D space and parallelize it using the OpenMP and MPI libraries. The parallelization aims to enhance the performance of the PIC simulation, enabling the simulation of larger systems and achieving faster simulation times.

## Background

In the PIC algorithm, the motion of charged particles is tracked through the Coulomb force between different charged particles. Each particle has attributes such as position and velocity, which evolve over time.

To calculate the effect of particles in a 3D space, the particle charges are interpolated onto an irregular 3D mesh. [2] Similarly, the Coulomb forces computed on the mesh are interpolated back to the particle positions to update their velocities.

To parallelize this algorithm using OpenMP and MPI, we need to divide the work evenly between the workers. Each worker should calculate the Coulomb forces for a number of charged particles, and then update the positions and the velocities of the charged particles accordingly.

The PIC algorithm loop can be described as follows:
1. Interpolating the electromagnetic fields at the particle positions.
2. Computing the new particle velocities and positions.
3. Computing the new electromagnetic fields on the grid according to the positions of the charged particles.


## Resources

We may take some inspirations from these repositories:
1. [Parallel Research Tools Kernels](https://github.com/ParRes/Kernels)
2. [Smilei](https://github.com/SmileiPIC/Smilei)
3. [ModeRepository](https://www.cs.cmu.edu/~kmcrane/Projects/ModelRepository/)

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

## Platform Choice

We plan to use C++ as our programming language because it has many easy-to-use parallel libraries like OpenMP and MPI. Also, we will test our performance in GHC and PSC machines since they have many cores and are easy to access.

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
