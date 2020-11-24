# S3cr3t_R3c1p3

*This damn Meth-Cook encrypted his Recipe. But we found his RSA-Key. For some reason, it is not working. Can you help us?*

**!Alerta de guessing!**

Bien nos dan dos archivos
```
how_to_make_meth.txt: data
rsa_private.key:      PEM RSA private key
```
Intentando lo obvio ya que tenemos una key y un archivo cifrado
```
openssl rsautl -decrypt -inkey rsa_private.key -in how_to_make_meth.txt 
unable to load Private Key
```
Houston tenemos un problema, la clave no funciona. Aquí viene la guesseada, primero subimos nuestra clave a cyberchef y tras esto aplicamos esta receta
```
PEM_to_Hex()
From_Hex('Auto')
Raw_Inflate(0,0,'Adaptive',false,false)
```
Con esto tendremos una segunda clave descarguemosla y llamemosla second.key, tras esto desencriptemos
``` 
openssl rsautl -decrypt -inkey secondkey.rsa -in how_to_make_meth.txt
```
1. Chemicals
2. Mix 'em
3. HTB{5e59025ae4e2173cc372b7a890dddc75}
4. Profit
```
