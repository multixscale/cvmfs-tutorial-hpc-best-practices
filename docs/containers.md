# Containers and CernVM-FS

CVMFS can also be used to distribute container images, providing many of the same benefits that come with any CVMFS installation. Especially the on-demand download of accessed files means that containers start nearly instantly, and are more efficient for large images when only a fraction of the files are read, which is typically the case.

Any CVMFS repository can be used to distribute container images (although often, dedicated repositories are used, like `/cvmfs/unpacked.cern.ch`. In order to provide de-duplication and on-demand download, images must be stored unpacked. This requires some dedicated tools, provided by cvmfs itself - see the section "Ingesting container images in a CernVM-FS repository" below.

## Accessing a CernVM-FS repository via Apptainer

[Apptainer](https://apptainer.org/) is the recommended way to run containers from cvmfs, as it can start a container directly from an unpacked root file system, which is ideal for CVMFS.
Docker can be used as well but the setup is more complicated, requiring the cvmfs graphdriver plugin. More details can be found in the [documentation](https://cvmfs.readthedocs.io/en/stable/cpt-graphdriver.html).

For example, to run the [`https://registry.hub.docker.com/tensorflow/tensorflow:2.15.0-jupyter`](https://hub.docker.com/layers/tensorflow/tensorflow/2.15.0-jupyter/images/sha256-3bf17d6d5f2ed968543238936cca0725ca664d24729c537778b1333a315036d7?context=explore) image that has been unpacked on /cvmfs/unpacked.cern.ch, use the command

```
apptainer exec /cvmfs/unpacked.cern.ch/registry.hub.docker.com/tensorflow/tensorflow\:2.15.0-jupyter /bin/bash
...
```

This directory just contains the root file system of the image:

```
ls /cvmfs/unpacked.cern.ch/registry.hub.docker.com/tensorflow/tensorflow\:2.15.0-jupyter
...
```



## Ingesting container images in a CernVM-FS repository

CVMFS provides a suite of container unpacking tools called `cvmfs_ducc` (provided by the `cvmfs-ducc` package). This can be used to unpack and ingest container images by simply running

```
cvmfs_ducc convert recipe.yaml 
```
where recipe.yaml is a 'wishlist' of container images available in external registries that should be made available:

```
version: 1
user: cvmfsunpacker
cvmfs_repo: 'unpacked.test.repo'
input:
    - 'https://registry.hub.docker.com/tensorflow/tensorflow:2.15.0-jupyter'
    ...
```


## Using /cvmfs inside containers

The easiest way to access cvmfs from a container is to set it up on the host and bind-mount it inside the container:

```
docker run -it --volume /cvmfs:/cvmfs:shared ubuntu ls -lna /cvmfs/atlas.cern.ch
``
