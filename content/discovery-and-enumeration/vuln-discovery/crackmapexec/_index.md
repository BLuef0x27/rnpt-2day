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
