+++
title = "SMB Shares"
menuTitle = "SMB Shares"
weight = 60
+++
![](./sharecare.png)

## 1. Accessing From Commandline
When in doubt, one of the quickest/easiest ways to connect to an SMB share is to use the *smbclient* commandline utility.

`smbclient --help`

```bash
Usage: smbclient [OPTIONS] service <password>
  -M, --message=HOST                           Send message
  -I, --ip-address=IP                          Use this IP to connect to
  -E, --stderr                                 Write messages to stderr instead of stdout
  -L, --list=HOST                              Get a list of shares available on a host
  -T, --tar=<c|x>IXFvgbNan                     Command line tar
  -D, --directory=DIR                          Start from directory
  -c, --command=STRING                         Execute semicolon separated commands
  -b, --send-buffer=BYTES                      Changes the transmit/send buffer
  -t, --timeout=SECONDS                        Changes the per-operation timeout
  -p, --port=PORT                              Port to connect to
  -g, --grepable                               Produce grepable output
  -q, --quiet                                  Suppress help message
  -B, --browse                                 Browse SMB servers using DNS

Help options:
  -?, --help                                   Show this help message
      --usage                                  Display brief usage message

Common Samba options:
  -d, --debuglevel=DEBUGLEVEL                  Set debug level
      --debug-stdout                           Send debug output to standard output
  -s, --configfile=CONFIGFILE                  Use alternative configuration file
      --option=name=value                      Set smb.conf option from command line
  -l, --log-basename=LOGFILEBASE               Basename for log/debug files
      --leak-report                            enable talloc leak reporting on exit
      --leak-report-full                       enable full talloc leak reporting on exit

Connection options:
  -R, --name-resolve=NAME-RESOLVE-ORDER        Use these name resolution services only
  -O, --socket-options=SOCKETOPTIONS           socket options to use
  -m, --max-protocol=MAXPROTOCOL               Set max protocol level
  -n, --netbiosname=NETBIOSNAME                Primary netbios name
      --netbios-scope=SCOPE                    Use this Netbios scope
  -W, --workgroup=WORKGROUP                    Set the workgroup name
      --realm=REALM                            Set the realm name

Credential options:
  -U, --user=[DOMAIN/]USERNAME[%PASSWORD]      Set the network username
  -N, --no-pass                                Do not ask for a password
      --password=STRING                        Password
      --pw-nt-hash                             The supplied password is the NT hash
  -A, --authentication-file=FILE               Get the credentials from a file
  -P, --machine-pass                           Use stored machine account password
      --simple-bind-dn=DN                      DN to use for a simple bind
      --use-kerberos=desired|required|off      Use Kerberos authentication
      --use-krb5-ccache=CCACHE                 Credentials cache location for Kerberos
      --use-winbind-ccache                     Use the winbind ccache for authentication
      --client-protection=sign|encrypt|off     Configure used protection for client
                                               connections

Deprecated legcacy options:
  -k, --kerberos                               DEPRECATED: Migrate to --use-kerberos

Version options:
  -V, --version                                Print version
```

### 1.1 Basic Usage
To connect to an SMB share use the following command:

`smbclient \\\\192.168.0.130\\xamp -U "sciencerocks\richard.f" -p`




## 2. GUI File Browser
Most GUI file browsers have a built in capability to connect to an smb share with something like *smb://[ip address]* 

## 3. SMB Spidering
Manualy browsing with *smbclient* or a GUI file explorer works well for small shares.  If you have access to something substantial like the *C$* Windows hare for example.
You might consider using an automated tool to spider (recursivly browse) all directories in the share.  Metasploit has a great module for this.

