# Alternative ways to access CernVM-FS repositories

While a [native installation of CernVM-FS on the client system](client.md),
along with a [proxy server](proxy.md) and/or [Stratum 1 replica server](stratum1.md) for large-scale production setups,
is recommended, there are other alternatives available for getting access to CernVM-FS repositories.

We briefly cover some of these here, mostly to clarify that there are alternatives available,
including some that do not require system administrator permissions.

## `cvmfsexec`

Using [`cvmfsexec`](https://github.com/cvmfs/cvmfsexec), mounting of CernVM-FS repositories as
an unpriviledged user is possible, without having CernVM-FS installed system-wide.

`cvmfsexec` supports multiple ways of doing this depending on the system configuration,
where way requires that specific features are enabled in the system, like:

* like [FUSE mounting](https://www.kernel.org/doc/html/latest/filesystems/fuse.html) with `fusermount`;
* unprivileged user namespaces;
* unprivileged namespace fuse mounts;
* a `setuid` installation of Singularity 3.4+ (via `singcvmfs` which uses the `--fusemount` feature),
  or an unprivileged installation of Singularity 3.6+;

Start by closing the `cvmfsexec` repository from GitHub, and change to the `cvmfsexec` directory:

```
git clone https://github.com/cvmfs/cvmfsexec.git
cd cvmfsexec
```

Before using `cvmfsexec`, you first need to make a `dist` directory that includes CernVM-FS, configuration files,
and scripts. For this, you can run the `makedist` script that comes with `cvmfsexec`:

```
./makedist default
```

With the `dist` directory in place, you can use `cvmfsexec` to run commands in an environment
where a CernVM-FS repository is mounted.

For example, we can run a script named `test_eessi.sh` that contains:

```shell
#!/bin/bash

source /cvmfs/software.eessi.io/versions/2023.06/init/bash

module load TensorFlow/2.13.0-foss-2023a

python -V
python3 -c 'import tensorflow as tf; print(tf.__version__)'
```

which gives:
```
$ ./cvmfsexec software.eessi.io -- ./test_eessi.sh

CernVM-FS: loading Fuse module... done
CernVM-FS: mounted cvmfs on /home/rocky/cvmfsexec/dist/cvmfs/cvmfs-config.cern.ch
CernVM-FS: loading Fuse module... done
CernVM-FS: mounted cvmfs on /home/rocky/cvmfsexec/dist/cvmfs/software.eessi.io

Found EESSI repo @ /cvmfs/software.eessi.io/versions/2023.06!
archdetect says x86_64/amd/zen2
Using x86_64/amd/zen2 as software subdirectory.
Using /cvmfs/software.eessi.io/versions/2023.06/software/linux/x86_64/amd/zen2/modules/all as the directory to be added to MODULEPATH.
Found Lmod configuration file at /cvmfs/software.eessi.io/versions/2023.06/software/linux/x86_64/amd/zen2/.lmod/lmodrc.lua
Initializing Lmod...
Prepending /cvmfs/software.eessi.io/versions/2023.06/software/linux/x86_64/amd/zen2/modules/all to $MODULEPATH...
Environment set up to use EESSI (2023.06), have fun!

Python 3.11.3
2.13.0
```

By default, the CernVM-FS client cache directory will be located in `dist/var/lib/cvmfs`.

For more information on `cvmfsexec`, see <https://github.com/cvmfs/cvmfsexec>.


## Apptainer with `--fusemount`

## Alien cache

[see](../configuration_hpc.md#alien-cache)

---

*(next: [Configuration on HPC systems](../configuration_hpc.md))*
