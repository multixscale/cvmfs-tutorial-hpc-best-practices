# Troubleshooting and debugging CernVM-FS

We focus on client-side issues, that is any issues that may arise on the
client itself or when it connects to a Squid proxy or a Stratum-1.

## Error overview

Considering the CernVM-FS client, errors could be related to

- the installation of the client software
  (see [Installation issues](#installation-issues)),
- the configuration of the client,
  (see [Configuration issues](#configuration-issues)),
- the current status of the client,
  (see [Mounting problems](#mounting-problems) and [Cache problems](#cache-problems)),
- the network connection to a Squid proxy and/or Stratum-1.
  (see [Connectivity issues](#connectivity-issues)),

_(add diagram illustrating a potential setup)_

## Common problems

### Installation issues

The CernVM-FS client is not installed or not accessible. A basic test could
be
``` { .bash .copy }
cvmfs2 --version
```
If the executable cannot be found, please see the section on
[installing the CernVM-FS client](access/client/#installing-cernvm-fs-client).

### Configuration issues

The configuration could be incomplete or wrong. Basic tests could be
``` { .bash .copy }
cvmfs_config showconfig software.eessi.io
```

and

``` { .bash .copy }
ls /cvmfs/software.eessi.io
sudo cmvfs_talk -i software.eessi.io version
```

Note, `cvmfs_config` uses the configuration files (typically under `/etc/cvmfs`
or provided through the repository `/cvmfs/cvmfs-config.cern.ch`), while
`cvmfs_talk` reports on the state of a (mounted) CernVM-FS repository.

If the above `cvmfs_talk` fails, try
``` { .bash .copy }
ls /cvmfs/cvmfs-config.cern.ch
sudo cmvfs_talk -i cvmfs-config.cern.ch version
```

If the latter succeeds, there rather is an issue with the configuration of or
the access to the former (`software.eessi.io` in this example).


### Connectivity issues

- firewall blocking ports or hosts

- squid proxy configuration

- tools: `curl`, `telnet`, `tcptraceroute` (*), `cvmfs_config stat`

### Mounting problems

`autofs`

try manual mount:

```
mount -t cvmfs software.eessi.io /tmp/eessi
```

use cvmfs directly to mount, see https://github.com/cvmfs/cvmfs/blob/devel/doc/developer/60-debugging-and-testing.md#client

### Resource problems

disk full on proxy or Stratum 1 or client cache

### Cache problems

```
sudo cvmfs_config wipecache
```


## Logs

## General tools

```
sudo cvmfs_config chksetup
```

```
cvmfs_config status
```

```
cvmfs_config probe software.eessi.io
```

---

## Logs

only client-side, assumption is that CernVM-FS replica servers and proxy servers are working correctly (?)

see also https://github.com/EESSI/filesystem-layer/blob/main/README.md

## Client to S1 / proxy connection

`telnet STRATUM1_IP 80`

`curl --head http://STRATUM1_IP/cvmfs/software.eessi.io/.cvmfspublished`

`Connection` line in `cvmfs_config stat -v software.eessi.io`


## Error messages

```
$ ls /cvmfs
ls: cannot access '/cvmfs': No such file or directory
```

```
$ ls /cvmfs/software.eessi.io
ls: cannot access '/cvmfs/software.eessi.io': No such file or directory
```

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
ls: cannot access '/cvmfs/software.eessi.io': Transport endpoint is not connected
```

```
$ /cvmfs/config-repo.cern.ch
ls: cannot open directory '/cvmfs/config-repo.cern.ch': Too many levels of symbolic links
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

## Monitoring

## Mounting in debug mode

### Network issues

#### Firewall

- `telnet` (port 80 for Stratum 1, port 3128 for Squid proxy)
- `tcptraceroute`
- `curl --head http://aws-eu-central-s1.eessi.science/cvmfs/software.eessi.io/.cvmfspublished`

#### Bandwidth

- `iperf`

#### Proxy

`CVMFS_HTTP_PROXY`

https://cvmfs.readthedocs.io/en/stable/cpt-squid.html

`http_proxy=http://squid.vega.pri:3128 curl -vs http://aws-eu-central-s1.eessi.science/cvmfs/software.eessi.io/.cvmfspublished | cat -v`

```
http_proxy=http://squid.vega.pri:3128 curl --head http://aws-eu-west1.stratum1.cvmfs.eessi-infra.org/cvmfs/pilot.eessi-hpc.org/.cvmfspublished
HTTP/1.1 200 OK
```
```
$ http_proxy=http://squid.vega.pri:3128 curl --head http://aws-eu-central-s1.eessi.science/cvmfs/software.eessi.io/.cvmfspublished
HTTP/1.1 403 Forbidden
```

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
