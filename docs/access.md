# Accessing CernVM-FS repositories

## Setting up a client system

### Installing CernVM-FS client

=== "For RHEL-based Linux distros (incl. CentOS, Rocky, Fedora, ...)"

    ``` { .bash .copy }
    # install cvmfs-release package to add yum repository
    sudo yum install -y https://ecsft.cern.ch/dist/cvmfs/cvmfs-release/cvmfs-release-latest.noarch.rpm

    # install CernVM-FS client package
    sudo yum install -y cvmfs
    ```

=== "For Debian-based Linux distros (incl. Ubuntu)"

    ``` { .bash .copy }
    # install cvmfs-release package to add apt repository
    sudo apt install lsb-release
    curl -OL https://ecsft.cern.ch/dist/cvmfs/cvmfs-release/cvmfs-release-latest_all.deb
    sudo dpkg -i cvmfs-release-latest_all.deb
    sudo apt update

    # install CernVM-FS client package
    sudo apt install -y cvmfs
    ```

FIXME from source?

### Default configuration

`/cvmfs/cvmfs-config.cern.ch` default configuration repository

includes [flagship repositories](cvmfs/flagship-repositories.md)

```
cvmfs_config showconfig software.eessi.io
```

### Custom client configuration

(minimal requirement)

``` { .bash .copy }
# Create custom client configuration file for CernVM-FS
# (no squid proxy, 10GB local cache for CernVM-FS)
sudo bash -c "echo 'CVMFS_CLIENT_PROFILE="single"' > /etc/cvmfs/default.local"
sudo bash -c "echo 'CVMFS_QUOTA_LIMIT=10000' >> /etc/cvmfs/default.local"
```

### Additional repositories

To access additional CernVM-FS repositories for which the configuration is not included in the default CernVM-FS
configuration repository, you will need to install additional configuration files in `/etc/cvmfs`:

* `keys`
* `domain` or `repo` configuration

## Setting up a proxy server

## Setting up a replica server

## Alternative ways to access CernVM-FS repositories

(private)
