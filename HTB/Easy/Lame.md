# Lame ![Avatar](https://www.hackthebox.eu/storage/avatars/fb2d9f98400e3c802a0d7145e125c4ff_thumb.png)     

## Initial Scan

```nmap
21/tcp   open  ftp         vsftpd 2.3.4
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.14.15
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
|_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
```
## Enumeration
### FTP
El resultado de nmap comunica que el login anonimo esta habilitado en FTP , veamos si hay algo útil
```bash
ftp 10.10.10.3
```
Y ahora nos logeamos como Anonymous
```ftp
Connected to 10.10.10.3.
220 (vsFTPd 2.3.4)
Name (10.10.10.3:pj): Anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> pwd
257 "/"
ftp> ls -la
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxr-xr-x    2 0        65534        4096 Mar 17  2010 .
drwxr-xr-x    2 0        65534        4096 Mar 17  2010 ..
226 Directory send OK.
```
La version de FTP es vulnerable a el CVE-2011-2523

### SMB 
Buscando la versión de Samba presente en la máquina con searchsploit encontramos que es vulnerable a:
* Samba 3.0.20 < 3.0.25rc3 - 'Username' map script' Command Execution
* Samba < 3.0.20 - Remote Heap Overflow
* Samba 3.0.10 < 3.3.5 - Format String / Security Bypass 

## Exploit

Probamos el exploit de la FTP Backdoor sin exito, parece que lo han parcheado
Vamos a probar con el 'Username' map scrit Comand Execution
```URL
https://gist.github.com/joenorton8014/19aaa00e0088738fc429cff2669b9851
```
Lanzamos el exploit y obtenemos shell como root
```shell
id
uid=0(root) gid=0(root)
wc -c /root/root.txt
33 /root/root.txt
```
Siendo root tendremos acceso a la flag del user sin problema
```
wc -c /home/makis/user.txt
33 /home/makis/user.txt
```
