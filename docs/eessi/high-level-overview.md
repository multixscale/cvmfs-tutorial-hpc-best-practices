# High-level Overview of EESSI

## Layered structure (TO REVIEW)

The EESSI project consists of 3 layers.

<p align="center">
<img src="../../img/overview_layers.png" alt="EESSI layers" width="400px"/></br>
</p>
<!--Try and replace the diagram with something in mermaid-->

The top layer is the software layer, which contains the actual scientific software applications and their dependencies. Building, managing and optimising the software layer is supported by open source software such as EasyBuild, Archspec and Lmod.  

The middle layer is a compatibility layer, which ensures that the software stack is compatible with multiple different client operating systems. This is unique to the EESSI project and this relies on Gentoo Prefix. Gentoo Prefix installs a limited set of Gentoo Linux packages in a non-standard location (a "prefix"), using Gentoo's package manager Portage.

The bottom layer is the filesystem layer, which is responsible for distributing the software stack across clients. For this CernVM-FS is used that provides a reliable and scalable setup for distributing software.

In this layered architecture the host OS still provides a couple of things, like drivers for network and GPU, support for shared filesystems like GPFS and Lustre, a resource manager like Slurm, and so on.

<!--add something on the testing suites?-->

On the [What is CernVM-FS](../cvmfs/what-is-cvmfs.md) page you can learn more about the file system layer. If you want to learn more about the other layers you can have a look at the [EESSI documentation](https://www.eessi.io/docs/).


*(next: [Using EESSI](using-eessi.md))*
