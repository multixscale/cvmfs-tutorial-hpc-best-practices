# European Environment for Scientific Software (EESSI)

<!-- EESSI logo
<p align="center">
<img src="" alt="" width="100px"/></br>
</p>
-->

## What is EESSI 

The European Environment for Scientific Software Installations (EESSI, pronounced as "easy") is a collaboration between different European partners in the HPC community.

The goal of this project is to build a common stack of scientific software installations for HPC systems and beyond, including laptops, personal workstations and cloud infrastructure.

## What was the start point of forming EESSI

There are more and more scientists that are running large computations. Who have an increasing number of open source scientific software available to them. Next to the vairity in software there is also increasing variety in CPUs (Intel, AMD, Arm, RISC-V, ...), types of accerators (NVIDIA & AMD GPUs, Intel Xe, ...), use of the cloud (Amazon EC2, Microsoft Azure, Google, Oracle, ...). 

In stark contrast with the number of users and the variety of systems that they can use, is the number of available manpower that serve as HPC support teams. This is problem since it is often beneficial for the software to be optimised fo the system that it will run on. 

In the paper, _EESSI: A cross-platform ready-to-us optimised scientific software stack_ (2022)[^1], it is shown that the performance of GROMACS can vary significantly if the software is optimised to the system or not.

[^1]: you can access the paper via the following link: https://doi.org/10.1002/spe.3075.

## Scope and goals

Through the EESSI project, we want to set up a shared stack of scientific software installations, and by doing so avoid a lot of duplicate work across HPC sites.

For end users, we want to provide a uniform user experience with respect to available scientific software, regardless of which system they use.

Our software stack should work on laptops, personal workstations, HPC clusters and in the cloud, which means we will need to support different CPUs, networks, GPUs, and so on. We hope to make this work for any Linux distribution and maybe even macOS and Windows via WSL, and a wide variety of CPU architectures (Intel, AMD, ARM, POWER, RISC-V).

Of course we want to focus on the performance of the software, but also on automating the workflow for maintaining the software stack, thoroughly testing the installations, and collaborating efficiently.

## Inspiration

The EESSI concept is heavily inspired by Compute Canada software stack, which is a shared software stack used on all 5 major national systems in Canada and a bunch of smaller ones.

The design of the Compute Canada software stack is discussed in detail in the PEARC'19 paper "Providing a Unified Software Environment for Canada’s National Advanced Computing Centers".

It has also been presented at the 5th EasyBuild User Meetings (slides, recorded talk), and is well documented.

## Layered structure

The EESSI project consists of 3 layers.

<!--image-->

The bottom layer is the filesystem layer, which is responsible for distributing the software stack across clients.

The middle layer is a compatibility layer, which ensures that the software stack is compatible with multiple different client operating systems. <!--Also write something more on compatibility layer?-->

The top layer is the software layer, which contains the actual scientific software applications and their dependencies. <!--Also write something more on software layer layer?-->

The host OS still provides a couple of things, like drivers for network and GPU, support for shared filesystems like GPFS and Lustre, a resource manager like Slurm, and so on.

<!--add something on the testing suites-->

## The filesystem layer

CernVM-FS provides a reliable and scalable setup for distributing software <!--expand-->

Distriuting access via HTTP (so firewall friendly) <!--expand-->

The same software stack is available everywhere

## Pilot repositories

<!-- Maybe link to EESSI documentation on how to get access -->

## Partners

<!--starting partners
MulitXscale project-->

## Links

Documentation: https://www.eessi.io/docs/

## Slides

- https://easybuild.io/eum21/006_eum21_eessi.pdf
