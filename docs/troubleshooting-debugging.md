# Troubleshooting and debugging CernVM-FS

<!-- only client-side, assumption is that CernVM-FS replica servers and proxy
servers are working correctly (?) -->

In this section we list typical error messages, describe how their root cause
can be determined, and suggest actions to resolve them.

## Error messages

### `ls: cannot access '/cvmfs/software.eessi.io': No such file or directory`

One of the first actions to verify that a CernVM-FS repository, say
`software.eessi.io`, can be accessed, is simply running the command
```bash
ls /cvmfs/software.eessi.io
```
which could result in the following output
```
ls: cannot access '/cvmfs/software.eessi.io': No such file or directory
```

The three main causes for this error are:
1. The CernVM-FS client software is not installed.
2. The CernVM-FS client basic configuration is not fully set up.
3. The CernVM-FS client is not configured to access the repository.

(Cause 1) To verify that the CernVM-FS client is installed, run
``` { .bash .copy }
command -v cvmfs_config
```
The output is either empty or the full path to the executable, for example,
```
/usr/bin/cvmfs_config
```
In the latter case continue with (Cause 2) below. In the former case, see
section [Installing CernVM-FS client](access/client/#installing-cernvm-fs-client)
for installing the CernVM-FS client package `cvmfs`.

(Cause 2) The basic environment of the CernVM-FS is not fully set up. It
requires the following,
- a directory `/cvmfs` (verify with `ls /cvmfs`)
- a local service account with username `cvmfs` (verify with `getent passwd
  cvmfs`)
- a minimal configuration file `/etc/cvmfs/default.local` (verify with `cat
  /etc/cvmfs/default.local`)
- the command `sudo cvmfs_config setup` has been run

The first two requirements are likely fulfilled if the CernVM-FS package
`cvmfs` was installed with a package manager (see section [Installing CernVM-FS
client](access/client/#installing-cernvm-fs-client)). For the third
requirement, see paragraph [Minimal client configuration](access/client/#minimal_configuration).


```
failed to discover HTTP proxy servers (23 - proxy auto-discovery failed)
```

```
Failed to initialize root file catalog (16 - file catalog failure)
```

```
Failed to transfer ownership of /var/lib/cvmfs/shared to cvmfs
```

```
transport endpoint is not connected
```

## Configuration

```
cvmfs_config showconfig software.eessi.io
```

```
sudo cvmfs_talk -i software.eessi.io parameters
```

`CVMFS_REPOSITORIES` can be used to *limit* access to specific repositories

## Logs

only syslog?

debug log
- `CVMFS_DEBUGLOG=/tmp/cvmfs.log`
- https://cvmfs.readthedocs.io/en/stable/cpt-configure.html#debug-logs
- via xattrs: `attr -g logbuffer /cvmfs/software.eessi.io` (repo must be mounted, no sudo required)


## Stats

## Common problems

### Network issues

#### Firewall

- `telnet`
- `tcptraceroute`

#### Bandwidth

- `iperf`

#### Proxy

`CVMFS_HTTP_PROXY`

https://cvmfs.readthedocs.io/en/stable/cpt-squid.html

### Incorrect repository configuration

`/etc/cvmfs/keys`

`/etc/cvmfs/default.local`

`/etc/cvmfs/domain.d`

### Mount problems

manual mount:

- with `mount -t cvmfs`
- with `sudo /usr/bin/cvmfs2 ...`

### Cache corruption

```
sudo time cvmfs_fsck -j 8 /var/lib/cvmfs/shared
```

## Monitoring

```
cvmfs_talk -i software.eessi.io revision
```

```
cvmfs_config stat software.eessi.io
```
