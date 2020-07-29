# Art

####  Can you find the flag? 

### Extracción

Extraemos el zip que nos dan del challenge y veamos que hay dentro

```bash
$ unzip -P hackthebox art.zip 
Archive:  art.zip
    inflating: art.png      
```

### Decoding

Si observamos la imagen vemos pixeless de colores y pixeles negros muy marcados, lo cual me lleva a pensar que es Piet, vamos a usar un interprete online de Piet para resolverlo

```
https://www.bertnase.de/npiet/npiet-execute.php
```

Y ya nos da directamente la flag
```flag
HTB{p137_m0ndr14n}
```
