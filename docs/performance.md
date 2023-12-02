# Performance aspects of CernVM-FS

One aspect we can not ignore in the context of software on HPC infrastructure is *performance* (the P in HPC).

## Start-up performance

When installations of scientific software applications are provided via a CernVM-FS repository,
the main performance metric to worry about is **start-up time**: the amount of time it takes until an
application can *start* running. This requires that not only the binary program that is being launched itself
is somehow available on the system (on disk, in memory, etc.), but also that all files required by it
(libraries, dependencies) are available, including the ones they require in turn, etc.

Parallel filesystems like [GPFS (now IBM Storage Scale)](https://en.wikipedia.org/wiki/GPFS) and
[Lustre](https://www.lustre.org), which are ubiquitous on HPC systems,
are notorious for not performing well in this respect, which is not surprising since they mainly target a very
different use case: large-scale high-performance I/O on large datasets.

This has led to to all sorts of creative workarounds for "the startup problem",
including for example tools like [Spindle](https://github.com/hpc/Spindle), and recommendations to not install
(in particular Python) software directly on the parallel filesystem but to use a container image instead
([see the documentation of the current flagship EuroHPC system LUMI](https://docs.lumi-supercomputer.eu/software/installing/python/)).


!!! example "A note on the presented performance results"

    The start-up timing results shown in this section are by no means meant to be a statistically
    rigorous study of software start-up time.

    That said, these results should be indicative of what you may see on production HPC systems,
    and give a good view on the start-up times that you will observe for software provided via CernVM-FS,
    relative to alternatives like GPFS, Lustre, NFS, local disk, etc.

    More details on the experimental setup are available [at the end of this section](#experimental_setup).

## Test workloads {: #test_workloads }

We will present and discuss the start-up timing results for software installed in different file systems,
when being accessed under different circumstances, using two small workloads:

* Start-up of TensorFlow: `python -c 'import tensorflow'` ([see details below](#tensorflow_details))
* MPI startup (`MPI_init`), via `osu_init` microbenchmark ([see details below](#mpi_init_details))


## Cache status

The status of the Linux kernel file system cache, and the CernVM-FS client cache (when relevant)
is a key factor in start-up performance.

We discriminate between 3 scenarios: [cold cache](#cold_cache), [hot cache](#hot_cache), and [warm cache](#warm_cache).

### Cold cache {: #cold_cache }

### Hot cache {: #hot_cache }

### Warm cache {: #warm_cache }


## Performance results

### TensorFlow start-up

#### Local filesystems

<https://tacc.utexas.edu/use-tacc/software-list>

<p align="center">
<img src="../img/perf/tensorflow-startup-local-fs-cold-vs-hot.png" alt="Start-up performance of TensorFlow: local disk vs RAM disk" width="100%"/></br>
</p>

#### Parallel filesystems

<p align="center">
<img src="../img/perf/tensorflow-startup-lustre.png" alt="Start-up performance of TensorFlow: Lustre" width="100%"/></br>
</p>


<p align="center">
<img src="../img/perf/tensorflow-startup-gpfs.png" alt="Start-up performance of TensorFlow: Lustre" width="100%"/></br>
</p>

#### CernVM-FS

<p align="center">
<img src="../img/perf/tensorflow-startup-cvmfs-cold.png" alt="Start-up performance of TensorFlow: CernVM-FS (cold
caches)" width="90%"/></br>
</p>

<p align="center">
<img src="../img/perf/tensorflow-startup-cvmfs-hot-warm.png" alt="Start-up performance of TensorFlow: CernVM-FS (hot + warm cache)" width="100%"/></br>
</p>


### MPI start-up

## Test configuration details

A multitude of different system configurations is considered to evaluate start-up performance
of the [test workloads](#test_workload_details).

### Client system

The client system used in the tests is a worker node of the HPC-UGent Tier-2 cluster "doduo", with two exceptions:

* When testing NFS, the HPC-UGent Tier-2 cluster "victini" was used instead;
* When testing Lustre, the VSC Tier-1 cluster "Hortense" was used instead;

*(see [System configurations](#system_configurations) below for more technical details)*

### Software stack

Software installations being used are available via either:

* a GPFS filesystem, directly attached to the cluster via a high-speed network;
* a Lustre filesystem, directly attached to the cluster via a high-speed network;
* an NFS mount of a GPFS filesystem, via a 10Gbit Ethernet connection;
* CernVM-FS, with:
    - with a client cache on local disk (SSD, `ext4`), or in RAM disk (`/dev/shm`);
    - without and with (only) a (private) proxy server in the local network;
    - without and with (only) a private Stratum 1 replica server in the network;
    - with (only) a *specific* public Stratum 1 replica server: one in AWS `eu-west` region,
      another in Azure `us-east` region;

*(see [System configurations](#system_configurations) below for more technical details)*


## Test workload details {: #test_workload_details }

We will present and discuss the start-up timing results for software installed in different file systems,
when being accessed under different circumstances, using two small workloads:

* [Start-up of TensorFlow (`python -c 'import tensorflow'`)](#tensorflow)
* [MPI startup (`MPI_init`, via `osu_init` microbenchmark)](#mpi_init)

### TensorFlow {: #tensorflow_details }

We evaluate the start-up performance of [TensorFlow](https://www.tensorflow.org),
which is considered to be a representative example of a scientific workload implemented in
[Python](https://www.python.org/), which is a tremendously popular programming language in scientific research.
We used TensorFlow version 2.13.0, installed with [EasyBuild](https://easybuild.io) v4.8.2,
on top of Python version 3.11.3, which is available in [EESSI](eessi/index.md).

Before starting TensorFlow we first load the module to update the environment such
that TensorFlow is available:

```{ .bash .copy}
module load TensorFlow/2.13.0-foss-2023a
```

To evaluate the start-up performance of TensorFlow, we simply run:

```{ .bash .copy }
python -c 'import tensorflow'
```

Timing information is collected using the GNU `time` command, as follows:

```{ .bash .copy}
/usr/bin/time --format '%e' python -c 'import tensorflow'
```

#### Required files

Based on the statistics for and contents of the CernVM-FS client cache after running the specified command,
starting from a cold CernVM-FS client cache, we know that importing the `tensorflow` Python package:

* triggers ~11,000 `open()` calls + ~1,500 `opendir` calls (which includes non-existing paths);
* requires ~3,500 files, including:
    - ~2,200 files from the TensorFlow installation itself (~94% `*.pyc` files);
    - ~1,300 files from Python packages outside of the TensorFlow installation directory,
       of which ~76% `*.pyc` files, ~14% shared libraries (`.so`);
    - ~30 files from the [EESSI compatibility layer](eessi/high-level-design.md#compat_layer);

As such, this is a challenge for parallel filesystems like GPFS and Lustre, as the results will show.


### `MPI_Init` {: #mpi_init_details }


## Experimental setup {: #experimental_setup }

### System configurations {: #system_configurations }

??? note "HPC-UGent Tier-2 cluster 'doduo'"

    Hardware:

    - Dual-socket AMD EPYC 7552 CPU (AMD Rome, 96 cores in total)
    - 256GB of DDR4 RAM memory
    - 240GB SSD local disk (`ext4`)
    - HDR-100 InfiniBand interconnect
    
    Operating system:

    - Red Hat Enterprise Linux 8.8
    - Linux kernel `4.18.0-477.27.1.el8_8.x86_64`
    - GPFS (IBM Storage Scale) version 5.1.8-2 (`pagepool=4G`, `maxStatCache=1000`, `maxFilesToCache=4000`)
    - CermVM-FS 2.11.2

    *(see also [HPC-UGent Tier-2 infrastructure overview](https://www.ugent.be/hpc/en/infrastructure))*

??? note "HPC-UGent Tier-2 cluster 'victini'"

    Hardware:

    - Dual-socket AMD EPYC 7552 CPU (AMD Rome, 96 cores in total)
    - 256GB of DDR4 RAM memory
    - 240GB SSD local disk (`ext4`)
    - 10Gbit Ethernet interconnect

    Operating system:

    - Red Hat Enterprise Linux 8.8
    - Linux kernel `4.18.0-477.27.1.el8_8.x86_64`
    - NFS-Ganesha 3.5
    - CermVM-FS 2.11.2

    *(see also [HPC-UGent Tier-2 infrastructure overview](https://www.ugent.be/hpc/en/infrastructure))*

??? note "VSC Tier-1 cluster 'Hortense'"

    Hardware:

    - Dual-socket Intel Xeon Gold 6140 (CPU Skylake, 36 cores in total)
    - 96GB of DDR4 RAM memory
    - 900GB SAS HDD local disk (`ext4`)
    - HDR-100 InfiniBand interconnect

    Operating system:

    - Red Hat Enterprise Linux 8.8
    - Linux kernel `4.18.0-477.27.1.el8_8.x86_64`
    - Lustre 2.12.9
    - CermVM-FS 2.11.2

    *(see also [VSC documentation page on Hortense](https://docs.vscentrum.be/gent/tier1_hortense.html#hardware-details))*

??? note "Network details"

    **Bandwidth**

    Network bandwidth to HPC-UGent Tier-2 `doduo` cluster worker node from relevant servers,
    as measured with [`iperf3` v3.15](https://software.es.net/iperf/):

    - private Squid proxy server in HPC-UGent network: ~22,500 Mbits/sec
    - private Stratum 1 replica server in HPC-UGent network: ~940 Mbits/sec
    - EESSI Stratum 1 replica server in AWS `eu-west` region: ~930 Mbits/sec
    - EESSI Stratum 1 replica server in Azure `us-east` region: ~280 Mbits/sec

    Server-side `iperf3` command: `iperf3 -V -s -p 80`
    Client-side `iperf3` command: `iperf3 -V -c SERVER_HOSTNAME_OR_IP -p 80 -f m`

    **Latency**

    Network latency between HPC-UGent Tier-2 `doduo` cluster worker node and relevant servers,
    as measured with [`tcptraceroute` v2.1.0-6](https://linux.die.net/man/1/tcptraceroute):

    - private Squid proxy server in HPC-UGent network: ~0.2 ms
    - private Stratum 1 replica server in HPC-UGent network: ~0.7 ms
    - EESSI Stratum 1 replica server in AWS `eu-west` region: ~14 ms
    - EESSI Stratum 1 replica server in Azure `us-east` region: ~84 ms


### Relevant commands

??? note "Kernel file system cache"

    To clear kernel file system cache:

    ```{ .bash .copy }
    sudo sysctl -w vm.drop_caches=3
    ```

    To check file system cache usage:

    ```{ .bash .copy }
    vmstat -s -S M | grep buffer
    ```

??? note "CernVM-FS client cache"

    To clear CernVM-FS client cache:

    ```{ .bash .copy }
    sudo cvmfs_config wipecache
    ```

    To check CernVM-FS client cache usage:

    ```{ .bash .copy }
    cvmfs_config stat -v
    ```

    To check CernVM-FS client cache usage for a particular repository:

    ```{ .bash .copy }
    cvmfs_config stat -v software.eessi.io
    ```

    To check size of CernVM-FS client cache on disk (path determined by `CVMFS_CACHE_BASE` configuration setting):

    ```{ .bash .copy }
    du -sh /var/lib/cvmfs
    ```
