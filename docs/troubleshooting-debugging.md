# Troubleshooting and debugging CernVM-FS

only client-side, assumption is that CernVM-FS replica servers and proxy servers are working correctly (?)

## Error messages

```
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
