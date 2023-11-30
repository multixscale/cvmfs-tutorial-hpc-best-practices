# Configuring CernVM-FS on HPC infrastructure

In the previous section we provided recommendations for setting up a robust CernVM-FS infrastructure for HPC systems, by having a private Stratum 1 replica server and/or dedicated Squid proxy servers. While this approach will work for many HPC systems, some may have setups that require more dedicated solutions, which we will discuss in this section.


## Diskless worker nodes

Some HPC systems may have worker nodes without a hard disk, which would make it impossible for CernVM-FS to have a local cache on the worker nodes. Without this local cache, CernVM-FS is not be able to pull in any files that are being accessed by users, which renders the repository useless. In order to work around this, we will a few workarounds.

### In-memory cache

An easy solution to provide diskless worker nodes with a cache is by using a RAM disk like `/dev/shm`. This just involved setting `CVMFS_CACHE_BASE` to this location, and setting `CVMFS_QUOTA_LIMIT` to an appropriate amount of memory that you would like to dedicate to the cache. Obviously, a huge drawback of this solution is that (much) less memory can now be used by jobs running on the worker nodes.

### Loopback cache on a shared filesystem

The recommended solution for diskless worker nodes is to use loopback filesystems for the CernVM-FS caches. This loopback filesystem can be stored on the cluster's shared filesystem, and every worker node will need its own file in this case. This ensures that the parallelism of the shared file system can be exploited, while metadata accesses are performed within the loopback filesystems, and hence not overloading the shared filesystem's metadata server(s).

The loopback filesystem files can be created using the `dd` or `mkfs` tools. They should be formatted as an `ext3`, `ext4`, or `xfs` file system, and should be 15% larger than the cache size configured on the nodes (with `CVMFS_QUOTA_LIMIT`). On the nodes, the file system can be mounted from the shared file system, and they should be made available at the location defined with `CVMFS_CACHE_BASE` (by default `/var/lib/cvmfs`).

### Alien cache

An alien cache is basically a cache that is outside of the (full) control of CernVM-FS.
In this scenario it allows you to store the cache on a shared filesystem, and have the CernVM-FS processes on all worker nodes use and fill it simultaneously. These processes can pull in the required files that are being accessed by users/jobs, or you can even preload a cache manually.

Using the alien cache still requires a very small local cache on the nodes for storing some control files; these can be stored in, for instance, memory.

Compared to the loopback cache described in the previous subsection, the drawback of storing the alien cache on your shared filesystem is that all metadata operations are now performed on the shared filesystem. Typically, this will result in a large number of metadata operations, and for many shared filesystems this will be the bottleneck.


## Offline worker nodes

Another scenario for HPC systems is that worker nodes do not have (direct) access to the internet. In the context of CernVM-FS, this means that the clients running on the worker nodes would not be able to pull in files from (external) Stratum 1 replica servers.

For this scenario, several solutions are available as well.

### Squid proxy

Setting up a Squid proxy in the internal network of the cluster, which is highly recommended anyway, should circumvent the issue, as the nodes will only connect to Stratum 1 servers via the Squid proxy. This means that only the Squid proxy would need internet access in order to fetch files from the Stratum 1 servers, and the clients will, in turn, fetch the files from the proxy using the internal network.

### Private Stratum 1 replica server

Similar to having a Squid proxy in the internal network of the cluster, one could also opt for setting up a private Stratum 1 replica server that is accessible by the worker nodes, or even do both. Again, only the Stratum 1 server should be able to connect to the internet, as it will need to regularly synchronize with the Stratum 0 server.

### Alien cache

Finally, a last resort could again be to use an alien cache that is being prepopulated on a machine with internet access. This alien cache can then be made available on the worker nodes, for instance by having it stored on the shared filesystem of the cluster. But for the same reason as mentioned before, this is not recommended due to the load it will put on the metadata server(s) of the shared filesystem.


## Security

TODO or drop the section


## Worker nodes without CernVM-FS client

The last scenario that we describe is for HPC systems that do not have CernVM-FS clients installed on the worker nodes. This can, for instance, be the case if the administrators of the cluster are not willing to install the clients. Though less ideal, solutions to make CernVM-FS repositories available still exist in this case.

### Syncing a CernVM-FS repository to another filesystem

A solution that sounds straightforward is to synchronize (a part of) the contents of a CernVM-FS repository to another filesystem, and make that available on worker nodes. CernVM-FS provides [a utility `cvmfs_shrinkwrap`](https://cvmfs.readthedocs.io/en/stable/cpt-shrinkwrap.html) that can be used to achieve this. However, though the solution may sound easy, it has some severe disadvantages: this utility puts a heavy load on the server that is being used to pull in the contents, as it has to fetch all the contents (which may be a large amount of data) in one large bulk. Also, the contents will have to be kept synchronized in some way, which involves rerunning this process regularly. Finally, it somewhat defeats the purpose of CernVM-FS, as you will be replacing a filesystem that is optimized for distributing software by one that most likely is not.

### Mount the CernVM-FS repository in a container

More and more HPC systems provide a container runtime that can be used by users, and Singularity or Apptainer is a popular one nowadays. These two have support for FUSE mounting CernVM-FS repositories inside the container, allowing users to access the repository without special privileges. This is further explained in [Accessing a CernVM-FS repository via Apptainer](containers.md#accessing-a-cernvm-fs-repository-via-apptainer).

### Use `cvmfs-exec` to mount a CernVM-FS repository

A [`cvmfs-exec` package](https://github.com/cvmfs/cvmfsexec) is available to mount CernVM-FS repositories as an unprivileged user. This tool does depend on the availability of certain kernel features and/or Singularity/Apptainer being available on the host. Also, it currently only supports RHEL and SUSE and their derivatives.
