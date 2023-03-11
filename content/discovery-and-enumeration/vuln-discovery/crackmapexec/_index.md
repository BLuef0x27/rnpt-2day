+++
title = "CrackMapExec"
weight = 50
+++

## 1. Windows Enumeration
CrackMapExec is a fantastic tool created by Porchetta Industries for enumerating and attacking Active Directory environments.
We can leverage the *hosts/windows.txt* file to learn more about our target environment.

*  **Wiki** [https://wiki.porchetta.industries/](https://wiki.porchetta.industries/)S
*  **GitHub** [https://github.com/Porchetta-Industries/CrackMapExec](https://github.com/Porchetta-Industries/CrackMapExec)

`crackmapexec smb hosts/windows.txt`

```bash
KEPLER-LINSRV14  [*] Windows 6.1 (name:KEPLER-LINSRV14) (domain:) (signing:False) (SMBv1:True)
FEYNMAN-WINSRV1  [*] Windows 10.0 Build 20348 x64 (name:FEYNMAN-WINSRV1) (domain:Sciencerocks.local) (signing:False) (SMBv1:False)
HAWKINGWINSRV19  [*] Windows 10.0 Build 20348 x64 (name:HAWKINGWINSRV19) (domain:Sciencerocks.local) (signing:False) (SMBv1:False)
EINSTEIN-DC01    [*] Windows 10.0 Build 20348 x64 (name:EINSTEIN-DC01) (domain:Sciencerocks.local) (signing:True) (SMBv1:False)
```

This first command tells us quite a bit about the Windows targets in our engagement:

* DNS Name
* Operating System
* Active Directory Domain
* SMB version
* SMB signing

## 2. Testing AD Credentials
We can pass the credentials we cracked with hascat to all of our *windows.txt* targets and see what systems our user has access to.

`crackmapexec smb hosts/windows.txt -u richard.f -p "Security24-7"`

```bash
SMB         192.168.0.104   445    KEPLER-LINSRV14  [+] \richard.f:Security24-7 
SMB         192.168.0.130   445    FEYNMAN-WINSRV1  [+] Sciencerocks.local\richard.f:Security24-7 
SMB         192.168.0.120   445    HAWKINGWINSRV19  [+] Sciencerocks.local\richard.f:Security24-7 
SMB         192.168.0.100   445    EINSTEIN-DC01    [+] Sciencerocks.local\richard.f:Security24-7
```
This output confirms that the AD credentials we cracked are valid.  The *[+]* indicates that CrackMapExec successfuly logged in to each machine.
However, the missing *(Pwn3d!)* badge lets us know we do not have Admin on any of these systems.

## 3. Enumerating SMB Shares
Now that we know we have valid AD credentials one of the next things we should do is take a look at any SMB shares we have access to to see if there is anything we can compromise.

`crackmapexec smb hosts/windows.txt -u richard.f -p "Security24-7" --shares`

```bash
SMB         192.168.0.120   445    HAWKINGWINSRV19  [+] Sciencerocks.local\richard.f:Security24-7 
SMB         192.168.0.120   445    HAWKINGWINSRV19  [+] Enumerated shares
SMB         192.168.0.130   445    FEYNMAN-WINSRV1  [+] Sciencerocks.local\richard.f:Security24-7 
SMB         192.168.0.120   445    HAWKINGWINSRV19  Share           Permissions     Remark
SMB         192.168.0.120   445    HAWKINGWINSRV19  -----           -----------     ------
SMB         192.168.0.120   445    HAWKINGWINSRV19  ADMIN$                          Remote Admin
SMB         192.168.0.120   445    HAWKINGWINSRV19  C$                              Default share
SMB         192.168.0.120   445    HAWKINGWINSRV19  IPC$            READ            Remote IPC
SMB         192.168.0.100   445    EINSTEIN-DC01    [+] Sciencerocks.local\richard.f:Security24-7 
SMB         192.168.0.104   445    KEPLER-LINSRV14  [+] \richard.f:Security24-7 
SMB         192.168.0.104   445    KEPLER-LINSRV14  [+] Enumerated shares
SMB         192.168.0.104   445    KEPLER-LINSRV14  Share           Permissions     Remark
SMB         192.168.0.104   445    KEPLER-LINSRV14  -----           -----------     ------
SMB         192.168.0.104   445    KEPLER-LINSRV14  print$                          Printer Drivers
SMB         192.168.0.104   445    KEPLER-LINSRV14  IPC$                            IPC Service (kepler-linsrv14 server (Samba, Ubuntu))
SMB         192.168.0.130   445    FEYNMAN-WINSRV1  [+] Enumerated shares
SMB         192.168.0.130   445    FEYNMAN-WINSRV1  Share           Permissions     Remark
SMB         192.168.0.130   445    FEYNMAN-WINSRV1  -----           -----------     ------
SMB         192.168.0.130   445    FEYNMAN-WINSRV1  ADMIN$                          Remote Admin
SMB         192.168.0.130   445    FEYNMAN-WINSRV1  C$                              Default share
SMB         192.168.0.130   445    FEYNMAN-WINSRV1  IPC$            READ            Remote IPC
SMB         192.168.0.130   445    FEYNMAN-WINSRV1  Users           READ            
SMB         192.168.0.130   445    FEYNMAN-WINSRV1  xampp           READ,WRITE      
SMB         192.168.0.100   445    EINSTEIN-DC01    [+] Enumerated shares
SMB         192.168.0.100   445    EINSTEIN-DC01    Share           Permissions     Remark
SMB         192.168.0.100   445    EINSTEIN-DC01    -----           -----------     ------
SMB         192.168.0.100   445    EINSTEIN-DC01    ADMIN$                          Remote Admin
SMB         192.168.0.100   445    EINSTEIN-DC01    C$                              Default share
SMB         192.168.0.100   445    EINSTEIN-DC01    IPC$            READ            Remote IPC
SMB         192.168.0.100   445    EINSTEIN-DC01    NETLOGON        READ            Logon server share 
SMB         192.168.0.100   445    EINSTEIN-DC01    SYSVOL          READ            Logon server share
```

Take note of the *xampp* share which our compromised user account has both READ and WRITE access to!

## 4. Enumerating Active Directory Users
We can also use crackmapexec to identify valid user accounts which could be later targeted with password guessing should we decide to go that route.

`crackmapexec smb hosts/windows.txt -u richard.f -p "Security24-7" --users`

```bash
SMB         192.168.0.100   445    EINSTEIN-DC01    [-] Error enumerating domain users using dc ip 192.168.0.100: unsupported hash type MD4
SMB         192.168.0.100   445    EINSTEIN-DC01    [*] Trying with SAMRPC protocol
SMB         192.168.0.120   445    HAWKINGWINSRV19  [+] Sciencerocks.local\richard.f:Security24-7 
SMB         192.168.0.104   445    KEPLER-LINSRV14  [+] \richard.f:Security24-7 
SMB         192.168.0.100   445    EINSTEIN-DC01    [+] Enumerated domain user(s)
SMB         192.168.0.104   445    KEPLER-LINSRV14  [-] Error enumerating domain users using dc ip 192.168.0.104: socket connection error while opening: [Errno 111] Connection refused
SMB         192.168.0.104   445    KEPLER-LINSRV14  [*] Trying with SAMRPC protocol
SMB         192.168.0.100   445    EINSTEIN-DC01    Sciencerocks.local\Administrator                  Built-in account for administering the computer/domain
SMB         192.168.0.100   445    EINSTEIN-DC01    Sciencerocks.local\Guest                          Built-in account for guest access to the computer/domain
SMB         192.168.0.104   445    KEPLER-LINSRV14  [+] Enumerated domain user(s)
SMB         192.168.0.100   445    EINSTEIN-DC01    Sciencerocks.local\krbtgt                         Key Distribution Center Service Account
SMB         192.168.0.100   445    EINSTEIN-DC01    Sciencerocks.local\albert.e                       
SMB         192.168.0.100   445    EINSTEIN-DC01    Sciencerocks.local\isaac.n                        
SMB         192.168.0.100   445    EINSTEIN-DC01    Sciencerocks.local\johannes.k                     
SMB         192.168.0.100   445    EINSTEIN-DC01    Sciencerocks.local\richard.f                      
SMB         192.168.0.104   445    KEPLER-LINSRV14  [+] Enumerated domain user(s)
SMB         192.168.0.100   445    EINSTEIN-DC01    Sciencerocks.local\marie.c                        
SMB         192.168.0.100   445    EINSTEIN-DC01    Sciencerocks.local\server.admin                   
SMB         192.168.0.100   445    EINSTEIN-DC01    Sciencerocks.local\neil.degrasse        
```
