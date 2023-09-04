# What is CernVM-FS?

[CernVM-FS](https://cernvm.cern.ch/fs/), the CernVM File System (also known as *CVMFS*),
is a *file distribution service* that is particularly well suited to distribute *software installations*
across a large number of systems world-wide in an efficient way.

It is implemented as a *POSIX read-only file system in user space* with [repositories](cvmfs-terminology-repository)
of files that are served via HTTP, thus avoiding problems with firewalls.
Internally, CernVM-FS uses [content-adressable storage (CAS)](https://en.wikipedia.org/wiki/Content-addressable_storage)
and [Merkle trees](https://en.wikipedia.org/wiki/Merkle_tree) (like Git also does) to store file data and metadata.

Files in a CernVM-FS repository are *automatically downloaded on-demand* as they are accessed from web servers
that support the repository being used, and a multi-level caching hierarchy is employed to mitigate
latency issues.
Data integrity is verified via cryptographic hashes.

From an end user perspective, files in a CernVM-FS repository are available via subdirectory in `/cvmfs`,
The user experience is similar to that of an *on-demand streaming service* for music or video,
but then for software installations (or other types of data).

CernVM-FS provides several interesting features, including
[de-duplication](cvmfs-features-deduplication) of files,
[compression](cvmfs-features-compression),
[verification of data integrity](cvmfs-features-verification-data-integrity) via cryptographic hashes.
It has been proven to scale to hundreds of millions of files and tens of thousands of clients.

## Project history

## Features

### De-duplication of files {: #cvmfs-features-deduplication }

### Compression of data {: #cvmfs-features-compression }

### Verification of data integrity {: #cvmfs-features-verification-data-integrity }

## Terminology

### CernVM

(what is CernVM, relation with CernVM-FS)

### Repository {: #cvmfs-terminology-repository }

A CernVM-FS **repository** is ...

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
