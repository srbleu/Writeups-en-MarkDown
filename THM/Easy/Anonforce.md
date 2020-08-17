# Anonforce
Máquina sencilla, nada fuera de lo común excepto tal vez el punto de PGP

Interesantes: PGP
## Initial Scan
```
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| drwxr-xr-x    2 0        0            4096 Aug 11  2019 bin
| drwxr-xr-x    3 0        0            4096 Aug 11  2019 boot
| drwxr-xr-x   17 0        0            3700 Aug 11 12:49 dev
| drwxr-xr-x   85 0        0            4096 Aug 13  2019 etc
| drwxr-xr-x    3 0        0            4096 Aug 11  2019 home
| lrwxrwxrwx    1 0        0              33 Aug 11  2019 initrd.img -> boot/initrd.img-4.4.0-157-generic
| lrwxrwxrwx    1 0        0              33 Aug 11  2019 initrd.img.old -> boot/initrd.img-4.4.0-142-generic
| drwxr-xr-x   19 0        0            4096 Aug 11  2019 lib
| drwxr-xr-x    2 0        0            4096 Aug 11  2019 lib64
| drwx------    2 0        0           16384 Aug 11  2019 lost+found
| drwxr-xr-x    4 0        0            4096 Aug 11  2019 media
| drwxr-xr-x    2 0        0            4096 Feb 26  2019 mnt
| drwxrwxrwx    2 1000     1000         4096 Aug 11  2019 notread [NSE: writeable]
| drwxr-xr-x    2 0        0            4096 Aug 11  2019 opt
| dr-xr-xr-x  100 0        0               0 Aug 11 12:49 proc
| drwx------    3 0        0            4096 Aug 11  2019 root
| drwxr-xr-x   18 0        0             540 Aug 11 12:49 run
| drwxr-xr-x    2 0        0           12288 Aug 11  2019 sbin
| drwxr-xr-x    3 0        0            4096 Aug 11  2019 srv
| dr-xr-xr-x   13 0        0               0 Aug 11 12:49 sys
|_Only 20 shown. Use --script-args ftp-anon.maxlist=-1 to see all.
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
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 8a:f9:48:3e:11:a1:aa:fc:b7:86:71:d0:2a:f6:24:e7 (RSA)
|   256 73:5d:de:9a:88:6e:64:7a:e1:87:ec:65:ae:11:93:e3 (ECDSA)
|_  256 56:f9:9f:24:f1:52:fc:16:b7:7b:a3:e2:4f:17:b4:ea (ED25519)
```
## Enum

Tenemos un ftp login anonymous permitido esto nos permite leer archivos en la máquina sin ningún tipo de credenciales

Asi obtenemos la primera flag
```
>get /home/melodias/user.txt
```

## Privesc

Si seguimos buscando en el servidor desde el ftp encontramos el siguiente directorio en /
```
drwxrwxrwx    2 1000     1000         4096 Aug 11  2019 notread
```
Dentro del mismo archivos de gpg
```
-rwxrwxrwx    1 1000     1000          524 Aug 11  2019 backup.pgp
-rwxrwxrwx    1 1000     1000         3762 Aug 11  2019 private.asc
```
Descargamos ambos

Crackeamos el .asc usando gpg2john y tenemos que la pass es xbox360, ya solo nos queda importar la clave y con ella descifrar el .pgp, que es una copia del shadow de la maquina
```
root:$6$07nYFaYf$F4VMaegmz7dKjsTukBLh6cP01iMmL7CiQDt1ycIm6a.bsOIBp0DwXVb9XI2EtULXJzBtaMZMNd2tV4uob5RVM0:18120:0:99999:7:::
melodias:$1$xDhc6S6G$IQHUW5ZtMkBQ5pUMjEQtL1:18120:0:99999:7:::
```
Vamos a crackearlos

La password para el root es hikari, ya solo queda conectarnos y leer la root.txt
```
ssh root@10.10.90.218
```
# Analisis de la intrusión
### FTP login anonymous habilitado
Una mala decisión en cuanto a la configuración en el servidor FTP por parte del administrador (habilitar el login anonymous) , nos permitio listar archivos de manera arbitraria dentro del servidor (aunque con unos privilegios limitados)
### Notread
La presencia de archivos potencialmente peligrosos en zonas donde otros usuarios puedan leer (aunque la llamemos notread) es negligente por parte de los administradores
### Password Police
La politica de contraseñas usada es poco fuerte, tanto para el cifrado gpg como para el propio root, lo que nos permite realizar un ataque por fuerza bruta exitoso con relativamente poco recursos

# Soluciones 
### Limitar el acceso anonimo
Si bien puede ser necesario mantener un servidor FTP con acceso anonimo, siempre es buena idea limitar las zonas en las que el usuario conectado mediante FTP puede acceder para ello siendo vsftpd podemos hacer lo siguiente
```
# Verificamos que este ajuste este asi chroot_local_user=YES
#Cambiamos el /home del user a una zona segura como puede ser /var/www/ftp
usermod --home /var/www/ftp username
```
