# Mirai ![Avatar](https://www.hackthebox.eu/storage/avatars/4ef1ea77e69185063d4200d7d0142baa_thumb.png)

## Initial Scan
```
22/tcp    open  ssh     OpenSSH 6.7p1 Debian 5+deb8u3 (protocol 2.0)
| ssh-hostkey: 
|   1024 aa:ef:5c:e0:8e:86:97:82:47:ff:4a:e5:40:18:90:c5 (DSA)
|   2048 e8:c1:9d:c5:43:ab:fe:61:23:3b:d7:e4:af:9b:74:18 (RSA)
|   256 b6:a0:78:38:d0:c8:10:94:8b:44:b2:ea:a0:17:42:2b (ECDSA)
|_  256 4d:68:40:f7:20:c4:e5:52:80:7a:44:38:b8:a2:a7:52 (ED25519)
80/tcp    open  http    lighttpd 1.4.35
|_http-server-header: lighttpd/1.4.35
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
1196/tcp  open  upnp    Platinum UPnP 1.0.5.13 (UPnP/1.0 DLNADOC/1.50)
32400/tcp open  http    Plex Media Server httpd
| http-auth: 
| HTTP/1.1 401 Unauthorized\x0D
|_  Server returned status 401 but no WWW-Authenticate header.
|_http-cors: HEAD GET POST PUT DELETE OPTIONS
|_http-title: Unauthorized
32469/tcp open  upnp    Platinum UPnP 1.0.5.13 (UPnP/1.0 DLNADOC/1.50)
```

## Enum
### HTTP 80
#### Gobuster
```
/admin (Status: 301)
/swfobject.js (Status: 200)
/versions (Status: 200)
```

En /admin tenmos una consola de administracion de una pi-hole, indicador de que tenemos delante una raspberry

Las creds defaults de una raspberry son pi:raspberry , probamos en el ssh y podemos logearnos sin ningún problema

## Privesc 
#### Sudo -l 
```
User pi may run the following commands on localhost:
    (ALL : ALL) ALL
    (ALL) NOPASSWD: ALL
```
Pero al mirar la root flag encontramos lo siguiente
```
I lost my original root.txt! I think I may have a backup on my USB stick...
```
Veamos los mounts

```
root@raspberrypi:~# ls /media/usbstick/
damnit.txt  lost+found
root@raspberrypi:~# cat /media/usbstick/damnit.txt 
Damnit! Sorry man I accidentally deleted your files off the USB stick.
Do you know if there is any way to get them back?

-James
```
Veamos si hay suerte y podemos recuperarlo con grep

```
grep --binary-files=text --context=100 'root' /dev/sdb 
```

Y ahi podemos ver la flag3d3e483143ff12ec505d026fa13e020b3d3e483143ff12ec505d026fa13e020b

# Analisís de la intrusión
### Default creds
Las contraseñas para la raspberry no han sido cambiadas de las estandar lo que nos permitio acceder a la maquina
