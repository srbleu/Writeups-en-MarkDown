# Joy
## Sanity Check
```
We offer a message for you on our NOT very secret channel! Send it back!
```

Nos unimos a su canal de Telegram y buscamos la cadena unictf

Flag: unictf{Un1c0rnCTF_th1s_1s_v3ry_n000t_d3f4ult_CTF}


## Best Blogger
```
I think everyone has already guessed what is the essence of this mega very difficult task!

As easy as shelling pears, you say! Take a photo or video of your team, upload it to any social network (absolutely any!) With the hashtag #unicornctf2020 and send a link to your publication by mail naumovax@disasm.me. Naturally, it's no secret to anyone that your account must be open so that we can check the completion of this task.

In the letter to the mail, indicate the "name of the team" + "link to the post"
```
Solo hacia falta subir una foto de nuestro team a una RRSS con el hashtag #unicornctf2020 y enviarlo a naumovax@disasm.me y esperar

Flag: unictf{th3_tru3_myst3ry_0f_th3 w0r1d_is_th3_visib13}


# Misc
## Without many words 
```
You don't need extra words for love, do you agree?
```
### Imagen
Tirando de strings vemos la siguiente
```
lvl1.rar0
```
Veamos si podemos sacar ese rar con binwalk
```
binwalk love.jfif  -e
```
El rar extraido esta password protected, vamos a sacar el hash
```
rar2john 4BB6E.rar > hash.txt
```
Tras un buen rato con JTR + Rockyou nos da la pass
```
ilovetoeat       (4BB6E.rar)
```
### LVL 1
Descomprimimos y tenemos dos archivos, otro rar y un txt, en el txt pone lo siguiente
```
CCT CTT TTA CTC TGA TAG ACG AGA
```
Es el encoded en DNA cipher para la palabra GENETICS, probemosla como password y accedamos al lvl2

### LVL 2

Obtenemos otra vez un archivo de texto y otro rar, el el archivo de texto pone lo siguiente
```
\/// /\/ \\/\ \/\ /\/ /\ /\/\
```
Parece Tom Tom Code y decodifica a:
```
NOTSOEZ
```
Usemoslo como pass

### LVL 3

Ahora una imagen y otro rar, la imagen parece algun tipo de cifrado mediante simbolos/runas
```
https://gravityfalls.fandom.com/wiki/List_of_cryptograms/Episodes
```
Decodifiquemos pues
```
GRAVITYFALLS
```

### Flag.jpg

En los exif hay un enlace a Pastebin
```
https://pastebin.com/9TXHurPR
```

Flag:unictf{th1ngs_4lw4ys_s33m_m0r3_d1ff1cult_th4n_th3y_4ctu4lly_4r3}

# OSINT
## CTF
```
Did you know CTF has a suffocating effect, and it was also once researched for rocket fuel and has an interesting code name for that. By who developed this rocket fuel?
```
Encontramos que hay un compuesto químico llamado CTF, usado como combustible para cohetes pero descartado, si miramos su artículo en wikipedia veremos que la SS fue el desarrollador del mismo

Flag: unictf{SS}

# Web

## Flag getter
```
This service issues a flag. You just need to ask correctly.
```
Hacemos algo de recon y vemos que tenemos un servicio SQL que coge parametros POST en la main page , pero tras un rato interctuando con el no obtengo nada

Tiramos dirb por si acaso y encontramos que /backup redirige a index.php

```
/.git/HEAD (Status: 403)
/backup (Status: 200)
/index.php (Status: 200)
/robots.txt (Status: 403)
```
Vamos a ver si el BFAC saca algo útil
```
https://github.com/mazen160/bfac
```
Finds:
```
[$] Discovered: -> {http://tasks2020.disasm.me:10100/.git/HEAD} (Response-Code: 403 | Content-Length: 146)
[$] Discovered: -> {http://tasks2020.disasm.me:10100/.git/index} (Response-Code: 403 | Content-Length: 146)
[$] Discovered: -> {http://tasks2020.disasm.me:10100/tasks2020.txt} (Response-Code: 403 | Content-Length: 146)
[$] Discovered: -> {http://tasks2020.disasm.me:10100/.} (Response-Code: 200 | Content-Length: 1311)
[$] Discovered: -> {http://tasks2020.disasm.me:10100/.gitignore} (Response-Code: 403 | Content-Length: 146)
[$] Discovered: -> {http://tasks2020.disasm.me:10100/.git/config} (Response-Code: 403 | Content-Length: 146)
[$] Discovered: -> {http://tasks2020.disasm.me:10100/.git-credentials} (Response-Code: 403 | Content-Length: 146)
[$] Discovered: -> {http://tasks2020.disasm.me:10100/%00} (Response-Code: 400 | Content-Length: 150)
[$] Discovered: -> {http://tasks2020.disasm.me:10100/.txt} (Response-Code: 403 | Content-Length: 146)

[i] Findings:
http://tasks2020.disasm.me:10100/.git/HEAD (403) | (Content-Length: 146)
http://tasks2020.disasm.me:10100/.git/index (403) | (Content-Length: 146)
http://tasks2020.disasm.me:10100/tasks2020.txt (403) | (Content-Length: 146)
http://tasks2020.disasm.me:10100/. (200) | (Content-Length: 1311)
http://tasks2020.disasm.me:10100/.gitignore (403) | (Content-Length: 146)
http://tasks2020.disasm.me:10100/.git/config (403) | (Content-Length: 146)
http://tasks2020.disasm.me:10100/.git-credentials (403) | (Content-Length: 146)
http://tasks2020.disasm.me:10100/%00 (400) | (Content-Length: 150)
http://tasks2020.disasm.me:10100/.txt (403) | (Content-Length: 146)
```
