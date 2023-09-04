# What is CernVM-FS?

## In a nutshell

[CernVM-FS](https://cernvm.cern.ch/fs/), the CernVM File System (also known as *CVMFS*),
is a *file distribution service* that is particularly well suited to distribute *software installations*
across a large number of systems world-wide in an efficient way.

It is implemented as a *POSIX read-only file system in user space* with [repositories](#repository)
of files that are served via outgoing HTTP connections only, thus avoiding problems with firewalls.
Internally, CernVM-FS uses [content-adressable storage (CAS)](https://en.wikipedia.org/wiki/Content-addressable_storage)
and [Merkle trees](https://en.wikipedia.org/wiki/Merkle_tree) (like [Git](https://git-scm.com/) also does)
to store file data and metadata.
Files in a CernVM-FS repository are *automatically downloaded* to a client system as
they are accessed, from web servers that support the CernVM-FS repository being used.

From an end user perspective, files in a CernVM-FS repository are available via subdirectory in `/cvmfs`,
with a user experience similar to that of an *on-demand streaming service* for music or video,
but then for software installations (or other types of data).

CernVM-FS was developed specifically for [distributing software](#distributing-software-as-main-use-case),
and provides several interesting features, including
[de-duplication of files](#de-duplication-of-files),
[compression of data](#compression-of-data),
[multi-level caching](#multi-level-caching),
and [verification of data integrity](#verification-of-data-integrity),
and has been proven to scale to hundreds of millions of files and tens of thousands of clients.


## Use cases and features

### Distributing software as main use case

CernVM-FS was developed at [CERN](https://home.cern/) to let High Energy Physics (HEP) collaborations
like the [Large Hadron Collider (LHC) project](https://home.cern/science/accelerators/large-hadron-collider)
deploy software on the worldwide-distributed computing infrastructure used to run data processing applications,
but can also be used to distribute large *data* repositories.

The software use case is a particular one, since software often comprises many small files that
are frequently opened and read as a whole, and frequent look-ups for files in multiple directories are
triggered when search paths are examined.

### De-duplication of files

Due to the content-addressable storage that is used under the hood, CernVM-FS automatically
effectively stores the contents of a file only once, even when it is included multiple times
in a particular repository at different paths.

This can result in a significant reduction in storage capacity that is required to host a large stack of
many software installations, especially when identical files are spread out across the repository,
as often happens with particular files like example data files across multiple versions of the same software.

### Compression of data

CernVM-FS [stores file content with compression](https://cvmfs.readthedocs.io/en/stable/cpt-repo.html#compression-and-hash-algorithms),
on the server, which not only further reduces required storage space but also significantly limits the
network bandwidth that is used to serve that a stored files in a repository across the network.
On the client side, the data is transparently decompressed as a part of the POSIX-compatible way in which
the contents of a CernVM-FS repository is presented under `/cvmfs`.

### Automatic updating of repository contents

...

### Multi-level caching

To reduce the latency when accessing files in a CernVM-FS repository, a multi-level caching hierarchy
is employed.

First of all, CernVM-FS benefits from the standard Linux kernel file system cache on a client system:
the content of a file that have been read recently will already be cached in memory by the kernel,
so that file does not need to be read from disk again.

CernVM-FS also maintains a custom [local cache](https://cvmfs.readthedocs.io/en/stable/cpt-details.html#disk-cache)
on the client system with a configurable size, which uses a
[least-recently used (LRU)](https://en.wikipedia.org/wiki/Cache_replacement_policies#LRU) replacement policy.
Files in a CernVM-FS repository that have been accessed sufficiently recently on a particular system
will already be available in the local cache of that system, and do not need to be downloaded again from
the network of CernVM-FS servers that support that repository.

When the contents of a file that is being accessed is not available yet in the local client cache,
CernVM-FS will automatically (and transparently) download it either from a [forward proxy server](#squid-proxy)
(like [Squid](http://www.squid-cache.org/)), or from a [Stratum-1 replica server](#stratum-1-replica-server).
Both the proxy and the replica server could be within the same local network as the client, or not.
To help reduce performance problems regarding network latency and bandwidth, clients can leverage
the [Geo API](https://cvmfs.readthedocs.io/en/stable/cpt-replica.html#geo-api-setup) supported by
Stratum-1 replica servers to automatically sort them geographically,
in order to prioritize connecting to the closest ones.

Furthermore, additional caches can be made available to CernVM-FS, such as an
[*alien cache*](https://cvmfs.readthedocs.io/en/stable/cpt-configure.html#alien-cache) on a shared cluster file system
like GPFS or Lustre that is not managed by CernVM-FS, and a
[Content Delivery Network (CDN)](https://en.wikipedia.org/wiki/Content_delivery_network)
can be used to help limit the time required to download files that are not cached yet.

### Verification of data integrity

The integrity of data provided by a CernVM-FS server is ensured on a client system by verifying a cryptographic
hash, which is again a direct result of content-addressable storage mechanism that is used by CernVM-FS.
This is an essential security aspect since CernVM-FS uses (possibly untrusted) caches and HTTP connections
to distribute the contents of a repository.

## Terminology

### CernVM

(what is CernVM, relation with CernVM-FS)

### Repository

A CernVM-FS **repository** is ...

### Software *installations*

vs software packages

### Publishing

### Client

### Stratum-0 server

### Stratum-1 replica server

### Squid proxy

## Use cases

## Example repositories

### Digital Research Alliance of Canada

[Digital Research Alliance of Canada](https://alliancecan.ca/en/about/alliance)

### EESSI

### Unpacked containers

`/cvmfs/unpacked.cern.ch` + `/cvmfs/singularity.opensciencegrid.org`

## Additional resources

* [CernVM-FS website](https://cernvm.cern.ch/fs)
* [CernVM-FS documentation](https://cvmfs.readthedocs.io)
* [CernVM-FS GitHub repository](https://github.com/cvmfs/cvmfs)
* [Introduction to CernVM-FS by *Jakob Blomer (CERN)*](https://easybuild.io/eum21/#cvmfs-talk)
* [Introductory tutorial to CernVM-FS (2021)](https://cvmfs-contrib.github.io/cvmfs-tutorial-2021)
