+++
title = "Discovering Assets with Nmap"
menuTitle = "Nmap (Assets)"
weight = 1
+++

## Basic Ping Sweep
Usually all you need to uncover lots of juicy assets (potential attack targets)

```bash
nmap -sn -iL ranges.txt -oA pingsweep
```
 * `-sn: Ping Scan - disable port scan`
   * Tell nmap not to do a port scan
 * `-iL <inputfilename>: Input from list of hosts/networks`
   * File containing IP ranges from your scope
 * `-oA <basename>: Output in the three major formats at once`
   * Output three files .nmap, .gnmap and .xml

