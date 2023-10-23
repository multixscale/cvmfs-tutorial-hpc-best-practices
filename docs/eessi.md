#

<div align="center">
<img src="../img/logos/EESSI_logo_horizontal.png" alt="EESSI logo" width="70%"/></br>
</div>


## What is EESSI? 

The [European Environment for Scientific Software Installations](https://www.eessi.io) (EESSI, pronounced as "easy")
is a collaboration between different European partners in the HPC (High Performance Computing) community.

It consists of a common stack of *optimized* scientific software installations that work on any Linux distribution,
and currently supports both `x86_64` (AMD/Intel) and `aarch64` (Arm 64-bit) systems, which is distributed
via [CernVM-FS](../cvmfs/what_is_cvmfs.md).


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
[AVX-512](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#AVX-512) instructions. 

## Inspiration (WIP)

The EESSI concept is heavily inspired by software stack provided by the
[Digital Research Alliance of Canada](https://alliancecan.ca/en/about/alliance),
for known as *Compute Canada* (see also [here](cvmfs/repositories.md)), but is significantly more ambitious by:

* Aiming to support a broader range of system architectures than what is currently supported by the
  Compute Canada software stack;
* Enabling the community


More information on the Compute Canada software stack is available in:

* [their documentation]();
* 

, which is a shared software stack used on all 5 major national systems in Canada and a bunch of smaller ones.

The design of the Compute Canada software stack is discussed in detail in the PEARC'19 paper "Providing a Unified Software Environment for Canada’s National Advanced Computing Centers"[^2].

It has also been presented at the 5th EasyBuild User Meetings (slides[^3], recorded talk[^4]), and is well documented.

[^2]: you can access the paper via the following link: https://dl.acm.org/doi/10.1145/3332186.3332210.

[^3]: the slides are available at https://easybuild.io/eum23/eum23_008_Digital-Research-Alliance-Canada.pdf.

[^4]: the recording is available at https://www.youtube.com/watch?v=gRNYp4gQKls.


## Layered structure (TO REVIEW)

The EESSI project consists of 3 layers.

<p align="center">
<img src="img/overview_layers.png" alt="EESSI layers" width="200px"/></br>
</p>
<!--Try and replace the diagram with something in mermaid-->

The top layer is the software layer, which contains the actual scientific software applications and their dependencies. Building, managing and optimising the software layer is supported by open source software such as EasyBuild, Archspec and Lmod.  

The middle layer is a compatibility layer, which ensures that the software stack is compatible with multiple different client operating systems. This is unique to the EESSI project and this relies on Gentoo Prefix. Gentoo Prefix installs a limited set of Gentoo Linux packages in a non-standard location (a "prefix"), using Gentoo's package manager Portage.

The bottom layer is the filesystem layer, which is responsible for distributing the software stack across clients. For this CernVM-FS is used that provides a reliable and scalable setup for distributing software.

In this layered architecture the host OS still provides a couple of things, like drivers for network and GPU, support for shared filesystems like GPFS and Lustre, a resource manager like Slurm, and so on.

<!--add something on the testing suites?-->

On the [What is cvmfs](00_what_is_cvmfs.md) page you can learn more about the file system layer. If you want to learn more about the other layers you can have a look at the [EESSI documentation](https://www.eessi.io/docs/).

## Pilot repositories (TO REVIEW)

### The current pilot repositories

- 2021.12
- 2023.04
- 2023.06

### How to get access

**step 1: Is EESSI accessible on your system?**

you can run a command to see if you already have access to the EESSI repository on your system.

If you run the command and get the following result. You have access to EESSI on your system:
```
$ ls /cvmfs/pilot.eessi-hpc.org
host_injections  latest  versions
```
If see an error message, you do not yet have aceess to EESSI on your system:
```
$ ls /cvmfs/pilot.eessi-hpc.org
ls: /cvmfs/pilot.eessi-hpc.org: No such file or directory
```
You and find in the EESSI documentation how to install CernVM-FS [natively](https://www.eessi.io/docs/getting_access/native_installation/) or in a [container](https://www.eessi.io/docs/getting_access/eessi_container/).

**step2: Setting up you environment**

To set up the EESSI environment, simply run the command:
```
$ source /cvmfs/pilot.eessi-hpc.org/latest/init/bash
```
If you would like specify the repository you can simply replace `latest` with one of the pilot repositories (`2021.12`, `2023.04`, `2023.06`).

<!-- Maybe link to EESSI documentation on how to get access -->

**Step 3: Run some basic commands**

To see which modules are available. you can run:
```
[EESSI pilot 2021.12] $ module avail 

---------------------------------------------------- /cvmfs/pilot.eessi-hpc.org/versions/2021.12/software/linux/x86_64/amd/zen2/modules/all ----------------------------------------------------
ant/1.10.8-Java-11                                              jbigkit/2.1-GCCcore-10.3.0                      OpenMPI/4.1.1-GCC-10.3.0
Arrow/0.17.1-foss-2020a-Python-3.8.2                            JsonCpp/1.9.4-GCCcore-9.3.0                     OpenPGM/5.2.122-GCCcore-9.3.0
Bazel/3.6.0-GCCcore-9.3.0                                       LAME/3.100-GCCcore-9.3.0                        OpenSSL/1.1                                        (D)
Bison/3.5.3-GCCcore-9.3.0                                       LAME/3.100-GCCcore-10.3.0                       OSU-Micro-Benchmarks/5.6.3-gompi-2020a
Bison/3.7.6-GCCcore-10.3.0                                      libarchive/3.5.1-GCCcore-10.3.0                 OSU-Micro-Benchmarks/5.7.1-gompi-2021a
Boost/1.72.0-gompi-2020a
```

Load modules with module load package/version, e.g., module load R/4.1.0-foss-2021a, and try out the software. See below for a short session:

```
[EESSI pilot 2021.12] $ module load R/4.1.0-foss-2021a
[EESSI pilot 2021.12] $ which R
/cvmfs/pilot.eessi-hpc.org/versions/2021.12/software/linux/x86_64/intel/skylake_avx512/software/R/4.1.0-foss-2021a/bin/R
[EESSI pilot 2021.12] $ R --version
R version 4.1.0 (2021-05-18) -- "Camp Pontanezen"
Copyright (C) 2021 The R Foundation for Statistical Computing
Platform: x86_64-pc-linux-gnu (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under the terms of the
GNU General Public License versions 2 or 3.
For more information about these matters see
https://www.gnu.org/licenses/.
```

**Step 4: run a demo with GROMACS**

First clone the eessi-demo git repository and move into the resulting directory.
```
$ git clone https://github.com/EESSI/eessi-demo.git
$ cd eessi-demo
$ls -l
drwxr-xr-x  5 example  users    160 Nov 23  2020 Bioconductor
drwxr-xr-x  3 example  users     96 Jan 26 20:17 CitC
drwxr-xr-x  5 example  users    160 Jan 26 20:17 GROMACS
-rw-r--r--  1 example  users  18092 Jan 26 20:17 LICENSE
drwxr-xr-x  3 example  users     96 Jan 26 20:17 Magic_Castle
drwxr-xr-x  4 example  users    128 Nov 24  2020 OpenFOAM
-rw-r--r--  1 example  users    546 Jan 26 20:17 README.md
drwxr-xr-x  5 example  users    160 Nov 23  2020 TensorFlow
drwxr-xr-x  6 example  users    192 Jan 26 20:17 scripts
```
Then run the following commands to do a demo with GROMACS
```
$ source /cvmfs/pilot.eessi-hpc.org/latest/init/bash
[EESSI pilot 2021.12] $ cd GROMACS
[EESSI pilot 2021.12] $ ./run.sh

GROMACS:      gmx mdrun, version 2020.1-EasyBuild-4.5.0
Executable:   /cvmfs/pilot.eessi-hpc.org/versions/2021.12/software/linux/x86_64/intel/haswell/software/GROMACS/2020.1-foss-2020a-Python-3.8.2/bin/gmx
...
starting mdrun 'Protein'
1000 steps,      2.5 ps.
```

## MultiXscale EuroHPC CoE

## More information

* [open access paper on EESSI (2022)](https://doi.org/10.1002/spe.3075)
* [EESSI website](https://www.eessi.io)
* [EESSI documentation](https://www.eessi.io/docs)
* [Talk on EESSI at EasyBuild User Meeting 2021](https://easybuild.io/eum21/#eessi)
