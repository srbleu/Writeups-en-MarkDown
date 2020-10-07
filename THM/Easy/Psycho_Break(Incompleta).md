# Psycho Break
## Initial Scan
``
21/tcp open  ftp     ProFTPD 1.3.5a
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 44:2f:fb:3b:f3:95:c3:c6:df:31:d6:e0:9e:99:92:42 (RSA)
|   256 92:24:36:91:7a:db:62:d2:b9:bb:43:eb:58:9b:50:14 (ECDSA)
|_  256 34:04:df:13:54:21:8d:37:7f:f8:0a:65:93:47:75:d0 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Welcome To Becon Mental Hospital
```

## Enum
### HTTP
En la landin page vemos un comentario
```
	<!-- Sebastian sees a path through the darkness which leads to a room => /sadistRoom -->
```

Si vamos a esa localizacion y vemos un js, en el:
```
Key to locker Room => 532219a04ab7a02b56faafbec1a4c1ea 
```
Tambien se ve esto:
```

$(".btn-danger").click(function(e) {
	const key = prompt("Enter Key To The Locker Room ");
	if (key == "532219a04ab7a02b56faafbec1a4c1ea"){
		window.open("../lockerRoom/","_self")
	}
 ```
 Accedemos a la URL y vemos un texto encodeado
 ```
 Tizmg_nv_zxxvhh_gl_gsv_nzk_kovzhv
 ```
 Si aplicamos atbash obtenemos esto
 ```
 Grant_me_access_to_the_map_please
 ```
 Encontramos dos endpoints 
 ```
 abandonedRoom/ <- Aqui va la pass del keeper
 SafeHeaven/ <- fotos 
 ```
 En el safe heaven hay varias fotos y un comentario
 ```
 <!-- I think I'm having a terrible nightmare. Search through me and find it ... -->
 ```
 Uffffffffff esteganografia random, ya seguiré
