# Madness

## Initial Scan
```
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 ac:f9:85:10:52:65:6e:17:f5:1c:34:e7:d8:64:67:b1 (RSA)
|   256 dd:8e:5a:ec:b1:95:cd:dc:4d:01:b3:fe:5f:4e:12:c1 (ECDSA)
|_  256 e9:ed:e3:eb:58:77:3b:00:5e:3a:f5:24:d8:58:34:8e (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
```
## Enum
### HTTP
En la landing page tenemos lo siguiente
```
        <img src="thm.jpg" class="floating_element"/>
<!-- They will never find me--
```
La imagen tiene un formato incorrecto, intentamos ver que pasa, mirando vemos que pese a ser formato jpg, la imagen tiene un header png, cambiamos el header y tenemos una imagen donde se nos muestra un hidden directory
```
/th1s_1s_h1dd3n
```
Vamos al directorio y nos encontramos el siguiente comentario
```
<!-- It's between 0-99 but I don't think anyone will look here-->
```
Probablemente en el backend haya un script en php esperando un secret como argumento
* 1.Generamos un diccionario
```
crunch 1 2 0123456789 > dic.txt
```
