# Motivation & Goals of EESSI

## Motivation

EESSI is motivated by the observation that the landscape of computational science is changing in various
ways, including:

* **Increasing diversity in system architectures**, like different families of general-purpose
  microprocessors like [Arm 64-bit (`aarch64`)](https://en.wikipedia.org/wiki/AArch64) and
  [RISC-V](https://en.wikipedia.org/wiki/RISC-V), and different types of GPUS (NVIDIA, AMD, Intel);
* **Rapid expansion of computational science** beyond traditional domains like physics and computational chemistry,
  including bioinformatis, Machine Learning and Artificial Intelligence, etc., which leads to a **significant
  growth of the software stack** that is used for running scientific workloads;
* **Emergence of commercial cloud infrastructure** ([Amazon EC2](https://aws.amazon.com/ec2/),
  [Microsoft Azure](https://azure.microsoft.com/en-us), ...)
  that has competitive advantages over on-premise infrastructure for computational workloads, such as near-instant
  availability, increased flexibility, and wider variety of hardware platforms;
* **Limited manpower** that is available in the HPC user support teams that are responsible for helping
  scientists with running the software they require on high-end (and complex) infrastructure like supercomputers
  (and beyond);

This results in a strong need for **more collaboration** to **avoid duplicate work** across HPC user support teams
and computational scientists.


## Goals

The main goal of EESSI is to provide a collection of scientific software installations that work across a
**wide range of different platforms**, including HPC clusters, cloud infrastructure, and personal workstations
and laptops, without making comprimes on the **performance** of that software.

While initially the focus of EESSI is to support Linux systems with an established system architectures like
AMD + Intel CPUs and NVIDIA GPUs, the ambition is to also cover emerging technologies like Arm 64-bit CPUs,
other accelerators like the [AMD Instinct](https://en.wikipedia.org/wiki/AMD_Instinct) and
[Intel Xe](https://en.wikipedia.org/wiki/Intel_Xe), and eventually also
[RISC-V](https://en.wikipedia.org/wiki/RISC-V).

The software installations included in EESSI are **optimized** for specific generations of the supported
[instruction set architectures (ISAs)](https://en.wikipedia.org/wiki/Instruction_set_architecture),
like for example Intel and AMD processors supporting
the [AVX2](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#Advanced_Vector_Extensions_2) or
[AVX-512](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#AVX-512) instructions, and
Arm processors supported [SVE](https://en.wikipedia.org/wiki/AArch64#Scalable_Vector_Extension_(SVE)) instructions.


*(next: [Inspiration for EESSI](inspiration.md))*
