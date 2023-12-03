# Troubleshooting for CernVM-FS

When you experience problems with getting access to a CernVM-FS repository,
it can be tricky to figure out what the actual underlying cause is,
giving the complexity of a typical CernVM-FS setup.

In this section we provide some guidelines on how to troubleshoot some potential problems
you may run into with CernVM-FS.
We focus on client-side issues: problems that may arise on a CernVM-FS client system itself,
or with the connection to external servers like a [Squid proxy](appendix/terminology.md#proxy)
or a [Stratum 1 replica server](appendix/terminology.md#stratum1).

While some commands suggested below that can help to determine the actual problem at hand
require system administrator privileges (indicated with the use of `sudo`),
several can also be run as an unprivileged user.

<p align="center">
<img src="../img/off_and_on.png" alt="Have you tried turning it off and on again?" width="60%"/></br>
</p>

!!! note

    In this section, we will continue to use the [EESSI CernVM-FS repository `software.eessi.io`](
    eessi/high-level-design.md#filesystem_layer) as a running example, but the troubleshooting guidelines
    or by no means specific to EESSI.

    Make sure you adjust the example commands to the CernVM-FS repository you are using, if needed.


## Typical problems

### Error messages

The error messages that you may encounter when accessing a CernVM-FS repository
are often quite cryptic, especially if you are not very familiar with CernVM-FS, or with
file systems and networking on Linux systems in general.

Here are a couple of examples:

* The CernVM-FS repository may not be known (yet) on your system, which will result
  in a (clear) error message like this when you try to access it:
  ```
  $ ls /cvmfs/software.eessi.io
  ls: cannot access '/cvmfs/software.eessi.io': No such file or directory
  ```
  
* You may see errors messages that suggest network connectivity problems, like:
  ```
  Failed to discover HTTP proxy servers (23 - proxy auto-discovery failed)
  ```
  ```
  Transport endpoint is not connected
  ```
  We will outline some approaches that should help you to determine what could be wrong exactly.


* Other problems may be quite specific to the internals of CernVM-FS,
  rather than being configuration or networking issues. Examples include:
  ```
  Failed to initialize root file catalog (16 - file catalog failure)
  ```
  ```
  Failed to transfer ownership of /var/lib/cvmfs/shared to cvmfs
  ```
  ```
  ls: cannot open directory '/cvmfs/config-repo.cern.ch': Too many levels of symbolic links
  ```
  Also here we will give some advice on how you might figure out what is wrong.

### Lag and/or hangs

When you notice lag of even (perceived) hanging when accessing a CernVM-FS repository,
you should consider revising the [connectivity](#connectivity)- and [cache](#cache)-related configuration settings.


## General approach

In general, it is recommended to take a step-by-step approach to troubleshooting:

* Start with verifying the CernVM-FS (client) [installation](#installation);
* Review the CernVM-FS [configuration](#configuration);
* Consider potential [network connectivity](#connectivity) issues;
* Keep an eye out for [mounting](#mounting) problems;
* Make sure you have sufficient available [resources](#resources) like memory and local disk space;
* Rule out any [cache](#caching)-related shenanigans;

Always keep in mind to [check the logs](#logs), and employ the [general tools](#general-tools)
that we put forward.


## Common problems

### CernVM-FS installation {: #installation }

Make sure that CernVM-FS is actually installed (correctly).

Check whether both the `/cvmfs` directory and the `cvmfs` service account exists on the system:
```{ .bash .copy }
ls /cvmfs
id cvmfs
```

Either of these errors would be a clear indication that CernVM-FS is not installed,
or that the [installation was not completed](access/client.md#completing-the-client-setup):
```
ls: cannot access '/cvmfs': No such file or directory
```
```
id: ‘cvmfs’: no such user
```

You can also check whether the `cvmfs2` command is available, and working:

``` { .bash .copy }
cvmfs2 --help
```

which should produce output that starts with:

```
The CernVM File System
Version 2.11.2
```

In case of problems, revise the section on
[installing the CernVM-FS client](access/client.md#installing-cernvm-fs-client).


### CernVM-FS configuration {: #configuration }

A common issue is incorrectly configuring CernVM-FS, either by making a silly mistake in a configuration file,
or by not taking into account the [hierarchy of configuration files](access/client.md#configuration_hierarchy)
that CernVM-FS considers.

#### Reloading

Don't forget to reload the CernVM-FS configuration after you've made changes to it:

``` { .bash .copy }
sudo cvmfs_config reload
```

#### Show configuration

Verify the configuration via `cvmfs_config showconfig`:

``` { .bash .copy }
cvmfs_config showconfig software.eessi.io
```

We strongly advise combining this command with `grep` to check for specific configuration settings, like:

```
$ cvmfs_config showconfig software.eessi.io | grep CVMFS_SERVER_URL
CVMFS_SERVER_URL='http://aws-eu-central-s1.eessi.science/cvmfs/software.eessi.io;http://azure-us-east-s1.eessi.science/cvmfs/software.eessi.io'    # from /cvmfs/cvmfs-config.cern.ch/etc/cvmfs/domain.d/eessi.io.conf
```

Be aware that `cvmfs_config showconfig` will read the configuration files as they are currently,
but that does not necessarily mean that those configuration settings are currently active;
see also [reloading](#reloading).

#### Non-existing repositories

Keep in mind that `cvmfs_config` does *not* check whether the specified
repository is actually known at all. Try for example querying the configuration
for the fictional `vim.or.emacs.io` repository:

``` { .bash .copy }
cvmfs_config showconfig vim.or.emacs.io
```

#### Inspect *active* configuration {: #active_configuration }

Inspect the *active* configuration that is currently used by *talking* to the running CernVM-FS service
via `cvmfs_talk`.

!!! note
    This requires that the specified CernVM-FS repository is currently mounted.

``` { .bash .copy }
ls /cvmfs/software.eessi.io > /dev/null  # to trigger mount if not mounted yet
sudo cvmfs_talk -i software.eessi.io parameters
```

`cvmfs_talk` can also be used to query other live aspects of a particular repository,
see the output of `cvmfs_talk --help`. For example:

* The current revision of repository contents (via `revision`);
* Information on the Stratum 1 replica server being used (via `host ...`);
* Information on the proxy server being used (via `proxy ...`);
* Information on the CernVM-FS client cache (via `cache ...`);

#### Non-mounted repositories

If running `cvmfs_talk` fails with an error like "`Seems like CernVM-FS is not running`",
try triggering a mount of the repository first by accessing it (with `ls`), or by running:

``` { .bash .copy }
cvmfs_config probe software.eessi.io
```

If the latter succeeds but accessing the repository does not,
there may be an issue with the (active) configuration,
or there may be a [connectivity problem](#connectivity).


### Connectivity issues {: #connectivity }

There could be various issues related to network connectivity,
for example a [*firewall*](https://en.wikipedia.org/wiki/Firewall_(computing)) blocking connections.

CernVM-FS uses plain `HTTP` as data transfer protocol, so basic tools can be used to investigate
connectivity issues.

#### Determine proxy server {: #determine_proxy }

You should check whether the client system can the Squid proxy and/or Stratum-1 replica server(s).

First figure out if a [proxy server](appendix/terminology.md#proxy) is being used via:
``` { .bash .copy }
sudo cvmfs_talk -i software.eessi.io proxy info
```

This should produce output that looks like:

```
Load-balance groups:
[0] http://PROXY_IP:3128 (PROXY_IP, +6h)
[1] DIRECT
Active proxy: [0] http://PROXY_IP:3128
```

(to protect the innocent, the actual proxy IP was replaced with "`PROXY_IP`" in the output above)

The last line indicates that a proxy server is indeed being used currently.

`DIRECT` would mean that *no* proxy server is being used.

#### Access to proxy server

If a proxy server is used, you should check whether it can be accessed at port `3128` (default Squid port).

For this, you can use standard networking tools (if available):

* `nc`, [ncat](https://nmap.org/ncat), a reimplementation of [netcat](https://sectools.org/tool/netcat):
  ```{ .bash .copy }
  nc -vz PROXY_IP 3128
  ```
* `telnet`:
  ```{ .bash .copy }
  telnet PROXY_IP 3128
  ```
* [`tcptraceroute`](https://linux.die.net/man/1/tcptraceroute):
  ```{ .bash .copy }
  sudo tcptraceroute PROXY_IP 3128
  ```

You will need to replace "`PROXY_IP`" in the commands above with the actual IP (or hostname) of the proxy
server being used.

#### Determine Stratum 1

Check which Stratum 1 servers are currently configured:

```{ .bash .copy }
cvmfs_config showconfig software.eessi.io | grep CVMFS_SERVER_URL
```

Determine which Stratum 1 is currently being *used* by CernVM-FS:

```
$ sudo cvmfs_talk -i software.eessi.io host info
  [0] http://aws-eu-central-s1.eessi.science/cvmfs/software.eessi.io (unprobed)
  [1] http://azure-us-east-s1.eessi.science/cvmfs/software.eessi.io (unprobed)
Active host 0: http://aws-eu-central-s1.eessi.science/cvmfs/software.eessi.io
```

In this case, the public Stratum 1 for EESSI in AWS `eu-central` is being used: `aws-eu-central-s1.eessi.science`.

#### Access to Stratum 1

If no proxy is being used (`CVMFS_HTTP_PROXY` is set to `DIRECT`, see also [above](#determine_proxy)),
you should check whether the active Stratum 1 is directly accessible at port `80`.

Again, you can use standard networking tools for this:

```{ .bash .copy }
nc -vz aws-eu-central-s1.eessi.science 80
```
```{ .bash .copy }
telnet aws-eu-central-s1.eessi.science 80
```
```{ .bash .copy }
sudo tcptraceroute aws-eu-central-s1.eessi.science 80
```

#### Download from Stratum 1

To see whether a Stratum 1 replica server can be used to download repository contents from,
you can use `curl` to check whether the `.cvmfspublished` file is accessible:

``` { .bash .copy }
S1_URL="http://aws-eu-central-s1.eessi.science"
curl --head ${S1_URL}/cvmfs/software.eessi.io/.cvmfspublished
```

If CernVM-FS is configured to use a proxy server, you should let `curl` use it too:
``` { .bash .copy }
P_URL="http://PROXY_IP:3128"
S1_URL="http://aws-eu-central-s1.eessi.science"
curl --proxy ${P_URL} --head ${S1_URL}/cvmfs/software.eessi.io/.cvmfspublished
```
or equivalently via the standard `http_proxy` environment variable that `curl` picks up on:
``` { .bash .copy }
S1_URL="http://aws-eu-central-s1.eessi.science"
http_proxy="PROXY_IP:3128" curl --head ${S1_URL}/cvmfs/software.eessi.io/.cvmfspublished
```

Make sure you replace "`PROXY_IP`" in the commands above with the actual IP (or hostname) of the proxy server.

If you see a `200` HTTP return code in the first line of output produced by `curl`, access is working as it should:

```
HTTP/1.1 200 OK
```

If you see `403` as return code, then something is blocking the connection:

```
HTTP/1.1 403 Forbidden
```

In this case, you should check whether a firewall is being used,
or whether an ACL in the [Squid proxy configuration](access/proxy.md#configuration) is the culprit.

If you see `404` as return code, you made a typo in the `curl` command :wink::
```
HTTP/1.1 404 Not Found
```
Maybe you forgot the '`.`' in `.cvmfspublished`?

### Mounting problems {: #mounting }

#### `autofs`

Keep in mind that (by default) CernVM-FS repositories are mounted [via `autofs`](access/client.md#autofs).

Hence, you should not rely on the output of `ls /cvmfs` to determine which repositories
can be accessed with your current configuration, since they may not be mounted currently.

You can check whether a specific repository is available by trying to access it directly:

```{ .bash .copy }
ls /cvmfs/software.eessi.io
```

#### Currently mounted repositories

To check which CernVM-FS repositories are currently mounted, run:
``` { .bash .copy }
cvmfs_config stat
```

#### Probing

To check whether a repository can be mounted, you can try to probe it:

```
$ cvmfs_config probe software.eessi.io
Probing /cvmfs/software.eessi.io... OK
```

#### Manual mounting

If you can not get access to a repository via auto-mounting by `autofs`,
you can try to manually mount it, since that may reveal specific error messages:

``` { .bash .copy }
mkdir -p /tmp/cvmfs/eessi
sudo mount -t cvmfs software.eessi.io /tmp/cvmfs/eessi
```

You can even try using the `cvmfs2` command directly to mount a repository:
``` { .bash .copy }
mkdir -p /tmp/cvmfs/eessi
sudo /usr/bin/cvmfs2 -d -f \
    -o rw,system_mount,fsname=cvmfs2,allow_other,grab_mountpoint,uid=$(id -u cvmfs),gid=$(id -g cvmfs),libfuse=3 \
    software.eessi.io /tmp/cvmfs/eessi
```
which prints lots of information for debugging (option `-d`).

### Insufficient resources {: #resources }

Keep in mind that the problems you observe may be the result of a shortage in resources,
for example:

* Lack of sufficient memory, for example for the kernel file system cache, which will typically
  lead to degrated (start-up) performance;
* Lack of sufficient disk space, for the CernVM-FS client cache, for the proxy server,
  or for the private Stratum 1 replica server;
* Network latency issues, either within the local network (to the proxy server or Stratum 1 replica server),
  or to the outside world (public Stratum 1 replica servers) &endash; see also the [Connectivity](#connectivity)
  section;


### Caching woes {: #caching }

Clean the cache to exclude any cache corruption as root cause of issues
``` { .bash .copy }
sudo cvmfs_config wipecache
```

Cache usage is included in the output of
``` { .bash .copy }
cvmfs_config stat -v software.eessi.io
```

Check consistency of the CernVM-FS cache directory
```
sudo time cvmfs_fsck -j 8 /var/lib/cvmfs/shared
```

## Logs {: #logs }

By default CernVM-FS logs to syslog, for example, `/var/log/messages` or
`/var/log/syslog`. Scanning these logs for `cvmfs` may help to determine the root
cause of an issue.

For obtaining more detailed information, CernVM-FS provides the setting
`CVMFS_DEBUGLOG`. If set as follows
``` { .ini .copy }
CVMFS_DEBUGLOG=/tmp/cvmfs_debug.log
```
CernVM-FS logs more information to `/tmp/cvmfs_debug.log` after the command
``` { .bash .copy }
sudo cvmfs_config reload
```
has been run. See
[CernVM-FS documentation / debug-logs](https://cvmfs.readthedocs.io/en/stable/cpt-configure.html#debug-logs)
for more information.

An interesting command for mounted repositories is
``` { .bash .copy }
attr -g logbuffer /cvmfs/software.eessi.io
```


## General tools

If the repository `software.eessi.io` is mounted, the following command provides
useful information and statistics
``` { .bash .copy }
cvmfs_config stat -v software.eessi.io
```

To verify whether the basic setup is sound, run
``` { .bash .copy }
sudo cvmfs_config chksetup
```
which should print something like
```
OK
```
or a message indicating a problem such as
```
Warning: autofs service is not running
```

Listing mounted repositories with
``` { .bash .copy }
cvmfs_config status
```

Printing non-empty configuration settings for a repository
``` { .bash .copy }
cvmfs_config showconfig -s software.eessi.io
```

Check if a CernVM-FS repository can be mounted
``` { .bash .copy }
cvmfs_config probe software.eessi.io
```


#### Bandwidth

_(maybe skip? or something for performance section?)_

- `iperf`

#### Proxy

`CVMFS_HTTP_PROXY`

https://cvmfs.readthedocs.io/en/stable/cpt-squid.html

_(the examples below didn't work ... squid.vega.pri seems not a known DNS name)_

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

_(maybe skip?)_

`/etc/cvmfs/keys`

`/etc/cvmfs/default.local`

`/etc/cvmfs/domain.d`

---

*(next: [Performance aspects of CernVM-FS](performance.md))*
