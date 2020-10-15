# e-jornadas 2020 CTF

## OSINT
### A Mirror would be useful
*What is the number on the back of this shirt?*
![](https://www.tv7dias.pt/wp-content/uploads/2019/01/A1-1767910_resized.jpg)
Vemos que es una camiseta de futbol y vemos que sale el escudo del benfica, si buscamos por imagen encontramos rapidamente que hay muchas fotos de esta camiseta por ser la que llevaba Miklos Feher con el 29

Flag: **flag{29}**

## Crypto
### Rotate away
*Rotate every damn day of the year*
```
rxms{xqfedafmfqftueftuzs}
```
Si apicamos una rot 14 obtenemos la flag facilmente
Flag: **flag{letsrotatethisthing}**
### Time to Coding
*Take a look at the python code file, and find the secret message in the file data.enc.*
```python
import base64


def encrypt(data): 
    encrypted=""
    key=0x2F
    pre_enc=""
    for x in data:
        tmp = ord(x) 
        tmp = tmp ^ key
        pre_enc+=chr(tmp)

    pre_enc=base64.b64encode(pre_enc)
    encrypted_data="" 
    for x in range(0, len(pre_enc)):   
        tmp = pre_enc[ len(pre_enc) - 1 -x ]    
        encrypted_data+= chr(ord(tmp) + 5 )
           
    return encrypted_data
```
Podemos definir una funcion decrypt que haga justo lo contrario:
```python
def decrypt(encrypted_data):
	pre_enc=""
	for x in range(0, len(encrypted_data)):   
		pre_enc+=chr(ord(encrypted_data[x]) - 5 )
	pre_enc=pre_enc[::-1]
	print(pre_enc)
	pre_enc=base64.b64decode(pre_enc)
	key=0x2F
	data=""
	print(pre_enc)
	pre_enc=str(pre_enc)
	for x in range (0,len(pre_enc)):
		tmp = ord(str(pre_enc[x]))
		tmp = tmp ^ key
		data += chr(tmp)**
		
	print(data)
```
Esto casi nos da la flag:
```
SUNOSFR7Rx58cEZccGEfW3BcTklKUg==
b'ICNHT{G\x1e|pF\\pa\x1f[p\\NIJR'
flag{ThsWJS_iss_NsWIt_ssafe}
```
Hay pequeños fallos por los \x, solución: Cyvberchef
Usamos el b64 ya restaurado y le apicamos b64 -d y xor con la clave para obtener la flag
Flag: **flag{Th1S_is_N0t_safe}**

## Stega
### Someone's voice
*Listen closely...*
Bien nos metemos en el audacity y le damos la vuelta al audio con la opcion de revertir, tras esto usamos el cambio de velocidad y oimos 
```
The flag is all lowercase hello sound
```
Ahora la flag es cuestion de poner el formato: **flag{alllowercasehellosound}**
