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
No tenemos creds pero admin:pasword funcionan perfectamente en el endpoint de administración, gracias a ello sabemos que la versión es la 3.7
