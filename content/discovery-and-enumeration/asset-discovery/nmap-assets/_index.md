+++
title = "Host discovery with Nmap"
menuTitle = "Nmap"
weight = 10
+++

![](./trinity.png)

Your first objective is to identify live assets or "hosts".  CD into your *engagements/sciencerocks/discovery* directory.

## 1. Basic Ping Sweep
Usually all you need to uncover lots of juicy hosts (potential attack targets).

```bash
nmap -sn -iL scope.txt -oA scans/pingsweep
```
 * **-sn:**
   * Ping Scan - disable port scan
 * **-iL [inputfilename>]:**
   * Input from list of hosts/networks
 * **-oA [basename]:**
   * Output in the three major formats at once

### 1.1. Filter the results
Now you want to extract the IP address for all hosts reported as being "Up" and place them in a *.txt* file.

```bash
grep "Up" scans/pingsweep.gnmap | cut -d " " -f2 > hosts/basic.txt
```

## 2. RMI Host Discovery
Sometimes a system administrator will intentionally configure hosts to ignore ICMP requests making them difficult to discover.  
Try this if the Basic Ping Sweep doesn't yield the results you're expecting.
The logic behind it is that systems were stood up for a reason and it's likely the system administrator is too busy to physicaly login 
every time maintenance is required. 
For this reason it is highly likely they configured some sort of remote management interface. These ports are simply the most common ports used for remote administration.
You can tweak this scan to have more or fewer ports keeping in mind the more ports you're checking the longer you'll have to wait on large scopes.

```bash
nmap -Pn -n -p 22,445,80,443,3389 -iL scope.txt -oA scans/rmisweep
```

### 2.1. Filter the results
Same as before but this time grep on the string *open* to see IP addresses of hosts that had one of the RMI ports open.
Some hosts could have multiple different RMI ports open so use *sort -u* to remote any duplicate entries.

```bash
grep "open" scans/rmisweep.gnmap | cut -d " " -f2 | sort -u > hosts/rmi.txt
```

## 3. Subnet hunting
What if the client has a Class A */8* network and with 16.7Mil possible IPs and has no idea where everything is?  No problem!
The logic here is that hypothetically speaking, each */24* subnet is going to have a gateway on the *.1* node.  
So instead of checking for 16 million possible IPs we only need to check for 65 thousand possible gateways.

> **Warning**
> DO NOT run this scan, I'll explain :)

```bash
nmap -sn 192.1-255.1-255.1 --min-rate 10000 --min-hostgroup 1024 -oA scans/subnetsweep
```
* **--min-hostgroup/max-hostgroup [size]:**
  * Parallel host scan group sizes (Secret sauce that tells nmap to check 1,024 hosts at a time instead of 64)
* **--min-rate [number]:**
  * Send packets no slower than <number> per second

[//]:![](./docbrown1.jpg)

### 3.1. Perform regular discovery on subnets
Here is a quick command to turn the *subnetsweep.gnmap* file into a new *ranges.txt* file that you can use for basic or RMI host discovery.

```bash
grep "Up" scans/subnetsweep | cut -d " " -f2 | awk -F "." '{print $1"."$2"."$3".0/24"}' > subnets.txt
```

## 4. Fished with Host Discovery
Now that you've completed your host discovery scans run this command to build your final *targets.txt* file which you'll use for service discovery.

```bash
cat hosts/*.txt | sort -u > targets.txt
```
