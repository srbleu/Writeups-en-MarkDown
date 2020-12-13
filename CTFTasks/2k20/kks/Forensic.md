# Forensic
## Encrypted Storage 1
![Task Description](https://github.com/srbleu/Writeups-en-MarkDown/blob/master/CTFTasks/2k20/kks/Task1.png)

Nos dan un filesystem que ha sdio encriptado por un ransom, pero podemos ver que es linux:
```bash
file filesystem 
filesystem: Linux rev 1.0 ext4 filesystem data, UUID=500a749b-81c7-471a-9be5-73271dde40f1 (extents) (64bit) (large files) (huge files)
```
Podemos montarlo:
```
sudo mount filesystem /mnt/tmp/
```
Veamos que hay dentro:
```
./f6068daa29dbb05a7ead1e3b5a48bbee/encrypted_0a11.enc
./b11ddc7e1671c0bd22dc89dd757508d4/encrypted_d2dd.enc
./1e6947ac7fb3a9529a9726eb692c8cc5/encrypted_b1a3.enc
./554cfab3938e21d9270bd6b75931f96f/encrypted_a4f4.enc
```
No se puede identificar con file que tipo de archivo son pero podemos ver lo siguiente:
```
pj@crow-foot:/mnt/tmp/f28128b38efbc6134dc40751ee21fd29$ hexdump -C 1e6947ac7fb3a9529a9726eb692c8cc5/encrypted_b1a3.enc  | head -n 2
00000000  73 5f 68 33 50 4b 03 04  0a 00 09 00 00 00 5c a1  |s_h3PK........\.|
00000010  8b 51 08 e2 c1 fa 1c 00  00 00 10 00 00 00 08 00  |.Q..............|
pj@crow-foot:/mnt/tmp/f28128b38efbc6134dc40751ee21fd29$ hexdump -C 554cfab3938e21d9270bd6b75931f96f/encrypted_a4f4.enc  | head -n 2
00000000  31 5f 77 34 00 00 00 18  66 74 79 70 6d 70 34 32  |1_w4....ftypmp42|
00000010  00 00 00 00 69 73 6f 6d  6d 70 34 32 00 00 df 58  |....isommp42...X|
pj@crow-foot:/mnt/tmp/f28128b38efbc6134dc40751ee21fd29$ hexdump -C b11ddc7e1671c0bd22dc89dd757508d4/encrypted_d2dd.enc  | head -n 2
00000000  72 33 21 7d 89 50 4e 47  0d 0a 1a 0a 00 00 00 0d  |r3!}.PNG........|
00000010  49 48 44 52 00 00 00 65  00 00 00 64 08 02 00 00  |IHDR...e...d....|
pj@crow-foot:/mnt/tmp/f28128b38efbc6134dc40751ee21fd29$ hexdump -C f6068daa29dbb05a7ead1e3b5a48bbee/encrypted_0a11.enc  | head -n 2
00000000  6b 6b 73 7b 25 50 44 46  2d 31 2e 35 0d 25 e2 e3  |kks{%PDF-1.5.%..|
00000010  cf d3 0d 0a 38 35 33 20  30 20 6f 62 6a 0d 3c 3c  |....853 0 obj.<<|
```
Bien parece que el ramsom no hace nada muy complicado, si observamos bien lo que hace es antes de los magic numbers añade unos bytes , intentemos ver los archivos, podriamos hacerlo a mano o haciendo file carving, hagamoslo con file carving:
```
foremost /mnt/tmp/f28128b38efbc6134dc40751ee21fd29/*/*
```
Esto nos devuelve 4 archivos:
```
./mov/00000000.mov: Apple QuickTime movie (fast start)
./zip/00000000.zip: Zip archive data, at least v1.0 to extract
./pdf/00000000.pdf: PDF document, version 1.5
./png/00000000.png: PNG image data, 101 x 100, 8-bit/color RGB, non-interlaced
```
El zip tiene contraseña, veamos si esta jhon ayuda
```
zip2john zip/00000000.zip > hash
john hash
cling            (00000000.zip/flag.txt)
```
Extraigamos la flag:
```
unzip zip/00000000.zip 
Archive:  zip/00000000.zip
[zip/00000000.zip] flag.txt password: cling
 extracting: flag.txt      
```
Flag **kks{n0t_s3cur3}**

## Encrypted Storage 2
![Task Description](https://github.com/srbleu/Writeups-en-MarkDown/blob/master/CTFTasks/2k20/kks/Task1.png)

Bien, si nos fijamos bien en que es lo que se ha añadido al principio de cada archivo podemos ver lo siguiente:
```
s_h3
1_w4
r3!}
kks{
```
Si lo ordenamos sabiendo el formato de la flag obtenemos lo siguiente:

Flag: **kks{1_w4s_h3r3!}**

