# Using EESSI

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

---

*(next: [Accessing a CernVM-FS repository](../access.md))*
