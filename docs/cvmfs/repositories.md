# Flagship CernVM-FS repositories

## LHC experiments

CernVM-FS repositories are used to distribute the software required to analyse the data produced by the
[Large Hadron Collider (LHC)](https://home.cern/science/accelerators/large-hadron-collider) at each of the
[LHC experiments](https://home.cern/science/experiments).

Examples include *(click to browse repository contents)*:

* [`/cvmfs/alice.cern.ch`](https://cvmfs-monitor-frontend.web.cern.ch/browse/alice.cern.ch): software for [ALICE](https://home.cern/science/experiments/alice) experiment
* [`/cvmfs/atlas.cern.ch`](https://cvmfs-monitor-frontend.web.cern.ch/browse/atlas.cern.ch): software for [ATLAS](https://home.cern/science/experiments/atlas) experiment
* [`/cvmfs/cms.cern.ch`](https://cvmfs-monitor-frontend.web.cern.ch/browse/cms.cern.ch): software for [CMS](https://home.cern/science/experiments/cms) experiment
* [`/cvmfs/lhcb.cern.ch`](https://cvmfs-monitor-frontend.web.cern.ch/browse/lhcb.cern.ch): software for [LHCb](https://home.cern/science/experiments/lhcb) experiment
* [`/cvmfs/sft.cern.ch`](https://cvmfs-monitor-frontend.web.cern.ch/browse/sft.cern.ch): LCG Software Stacks

## The Alliance

The [Digital Research Alliance of Canada](https://alliancecan.ca/en/about/alliance), a.k.a. *The Alliance* and formerly
known as *Compute Canada*, uses CernVM-FS to distribute the software stack for the Canadian national compute clusters.

Documentation on using their CernVM-FS repository `/cvmfs/soft.computecanada.ca` is available
[here](https://docs.alliancecan.ca/wiki/Accessing_CVMFS/en), and an overview of available software is available
[here](https://docs.alliancecan.ca/wiki/Available_software).

## Unpacked containers

CernVM-FS repositories can be used to provide a more efficient way to access container images,
by serving unpacking container images that can be consumed by container runtime like [Apptainer](https://apptainer.org).

Examples include:

* [`/cvmfs/unpacked.cern.ch`](https://cvmfs-monitor-frontend.web.cern.ch/browse/unpacked.cern.ch)
* `/cvmfs/singularity.opensciencegrid.org`

More information on how to use the `unpacked.cern.ch` repository is available
[here](https://awesome-workshop.github.io/docker-cms/06-unpacked/index.html).

## EESSI

The [*European Environment for Scientific Software Installations (EESSI)*](https://eessi.io) provides optimized installations
of scientific software for `x86_64` (Intel + AMD) and `aarch64` (64-bit Arm) systems that work on any Linux
distribution.

We will use EESSI as an example repository throughout this tutorial.

More detailed information on EESSI is available in the [next part of this tutorial](../eessi.md).
