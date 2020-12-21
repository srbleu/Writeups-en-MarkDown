## It's Complicated My Pal 

![Task](https://github.com/srbleu/Writeups-en-MarkDown/blob/master/CTFTasks/2k20/Bsides_Algiers/ComplicatedTask.png)

Bien mirando la captura lo unico raro que vi fueron los paquetes ICMP que contenian payloads, hice un script ad-hoc para resolverlo, bueno por acelerar hice 2
```bash
#!/bin/bash
tshark -r $1 -Tek -e data.data -Y 'icmp' | grep data_data > icmptrafic.json
python parser.py | base16 -d > rawpayload.ext
foremost rawpayload.ext 
```
Bien vayamos por partes 
1) Extraigamos el campo del payload de icmp
```bash
tshark -r $1 -Tek -e data.data -Y 'icmp' | grep data_data > icmptrafic.json
```
2) Pasamos el payload a un parser
```bash
python parser.py
```
2.1) Creamos un parser
```python 
import json

f = open("icmptrafic.json")
payload = ''
for line in f:
	y = json.loads(line)
	splited = str(y['layers']).split("u'")
	payload += splited[2].replace("']}","")

print(payload)
```
3) Pasamos el payload de hex a raw
```bash
| base16 -d > rawpayload.ext
``` 
4) Analizamos el payload con foremost por si hay adjuntos
```
foremost rawpayload.ext
```
Esto nos da una zip protegido con contraseña, habra que crackearla
```
zip2john 00000000.zip > hash
john hash --wordliist rockyou.txt
craccer
```
De aqui sale una imagen con una flag:

![Flag](https://github.com/srbleu/Writeups-en-MarkDown/blob/master/CTFTasks/2k20/Bsides_Algiers/flag.jpg)
