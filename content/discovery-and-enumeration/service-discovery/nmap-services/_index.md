+++
title = "Discovering Services with Nmap"
menuTitle = "Nmap once again"
weight = 30
+++

![](./nmap2.png)

## 1. Comon TCP Ports
Sometimes you have to perform service discovery against so many assets that it makes sense to first scan the most common ports.
Nmap has a flag specifically for this use case *--top-ports [number]*.

```bash
nmap -Pn -n -sV -iL targets.txt --top-ports 25 -oA scans/tcp-common 
```

* **--top-ports:**
  * Scan [number] most common ports
* **-sV:**
  * Probe open ports to determine service/version info

This will scan the following 25 ports against all IP addresses in the *targets.txt* file.  These 25 ports, according to Nmap are the 25 most commonly used ports.

```
TCP(25;21-23,25,53,80,110-111,135,139,143,199,443,445,587,993,995,1025,1720,1723,3306,3389,5900,8080,8888)
```


### 1.1. Commonly Attacked Ports

The above 25 ports may be the most commonly used but they aren't necessarily the most commonly attacked.  Meaning there might be a better list of ports to use for a quick scan.
You'll develop your own list of favorites as you conduct more engagements.  For now, I give you my own.

```bash
export MYPORTS='21-23,25,53,80-85,389,443,445,636,1433,3000,3306,3389,5800,5900,7443,8080,8443,8888'
nmap -Pn -n -sV -iL targets.txt -p $MYPORTS -oA scans/tcp-fav 
```

## 2. All TCP Ports
This scan can take a while but will check for all 65k+ ports and often identify *weird* services or maybe normal services listening on *non-standard* ports.
```bash
nmap -Pn -n -sV -A -iL targets.txt -p 1-65535 -oA scans/tcp-all
```
