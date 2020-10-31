# Bandit
## Level 0

*The goal of this level is for you to log into the game using SSH. The host to which you need to connect is bandit.labs.overthewire.org, on port 2220. The username is bandit0 and the password is bandit0. Once logged in, go to the Level 1 page to find out how to beat Level 1.*

Facil de resolver:
```
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

## Level 1
*The password for the next level is stored in a file called readme located in the home directory. Use this password to log into bandit1 using SSH. Whenever you find a password for a level, use SSH (on port 2220) to log into that level and continue the game.*
```
>cat readme
boJ9jbbUNNfktd78OOpsqOltutMc3MY1
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario
```
ssh bandit1@bandit.labs.overthewire.org -p 2220
```
## Level 2
*The password for the next level is stored in a file called - located in the home directory*
```
>cat < -
CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario
```
ssh bandit2@bandit.labs.overthewire.org -p 2220
```
## Level 3
*The password for the next level is stored in a file called spaces in this filename located in the home directory*
```
>cat spaces\ in\ this\ filename
UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario
```
ssh bandit3@bandit.labs.overthewire.org -p 2220
```
## Level 4
*The password for the next level is stored in a hidden file in the inhere directory.*
```
>cd inhere/
>ls -A
.hidden
>cat .hidden
pIwrPrtPN36QITSp3EQaw936yaFoFgAB
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario
```
ssh bandit4@bandit.labs.overthewire.org -p 2220
```
## Level 5
*The password for the next level is stored in the only human-readable file in the inhere directory. Tip: if your terminal is messed up, try the “reset” command.*
```
>file ./inhere/*
./inhere/-file00: data
./inhere/-file01: data
./inhere/-file02: data
./inhere/-file03: data
./inhere/-file04: data
./inhere/-file05: data
./inhere/-file06: data
./inhere/-file07: ASCII text
./inhere/-file08: data
./inhere/-file09: data
>cat ./inhere/-file07
koReBOKuIDDepwhWk7jZC0RTdopnAYKh
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario
```
ssh bandit5@bandit.labs.overthewire.org -p 2220
```
## Level 6
*The password for the next level is stored in a file somewhere under the inhere directory and has all of the following properties:*
*human-readable*
*1033 bytes in size*
*not executable*
Parece el escenario perfecto para usar el comando find:
```
>find . -type f -readable ! -executable -size 1033c -exec cat {} \;
DXjZPULLxYr17uwoI01bNLQbtFemEgo7
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario
```
ssh bandit6@bandit.labs.overthewire.org -p 2220
```
## Level 7
*The password for the next level is stored somewhere on the server and has all of the following properties:*
*owned by user bandit7*
*owned by group bandit6*
*33 bytes in size*
Parece el escenario perfecto para usar el comando find:
```
>find / -user bandit7 -group bandit6 -size 33c -exec cat {} \; 2> /dev/null
HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario
```
ssh bandit7@bandit.labs.overthewire.org -p 2220
```
## Level 8
*The password for the next level is stored in the file data.txt next to the word millionth*
```
> cat data.txt | grep millionth
millionth	cvX2JJa4CFALtqS87jk27qwqGhBM9plV
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario
```
ssh bandit8@bandit.labs.overthewire.org -p 2220
```
## Level 9
*The password for the next level is stored in the file data.txt and is the only line of text that occurs only once*
```
> cat data.txt | sort | uniq -u
UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario
```
ssh bandit9@bandit.labs.overthewire.org -p 2220
```
## Level 10
*The password for the next level is stored in the file data.txt in one of the few human-readable strings, preceded by several ‘=’ characters.*
```
>strings data.txt | grep -e "===*"
========== the
========== password
========== is
========== truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario
```
ssh bandit10@bandit.labs.overthewire.org -p 2220
```
## Level 11
*The password for the next level is stored in the file data.txt, which contains base64 encoded data*
```
>cat data.txt | base64 -d
The password is IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario
```
ssh bandit11@bandit.labs.overthewire.org -p 2220
```
## Level 12
*The password for the next level is stored in the file data.txt, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions*
```
>cat data.txt | tr "A-Za-z" "N-ZA-Mn-za-m"
The password is 5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario
```
ssh bandit12@bandit.labs.overthewire.org -p 2220
```
## Level 13
*The password for the next level is stored in the file data.txt, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work using mkdir. For example: mkdir /tmp/myname123. Then copy the datafile using cp, and rename it using mv (read the manpages!)*
```
>cp data.txt /tmp/srbleu/
>xxd -r data.txt data
>file data
data: gzip compressed data, was "data2.bin", last modified: Thu May  7 18:14:30 2020, max compression, from Unix
>mv data data.gz
>gzip -d data.gz
> file data
data: bzip2 compressed data, block size = 900k
> mv data data.bz2
> bzip2 -d data.bz2
> file data
data: gzip compressed data, was "data4.bin", last modified: Thu May  7 18:14:30 2020, max compression, from Unix
>mv data data.gz
>gzip -d data.gz
>file data
data: POSIX tar archive (GNU)
>mv data data.tar
>tar xvf data.tar
>file data5.bin
data5.bin: POSIX tar archive (GNU)
>mv data5.bin data5.tar
>tar xvf data5.tar
>file data6.bin
data6.bin: bzip2 compressed data, block size = 900k
>mv data6.bin data6.tar
>tar xvf data6.tar
>file data8.bin
data8.bin: gzip compressed data, was "data9.bin", last modified: Thu May  7 18:14:30 2020, max compression, from Unix
>mv data8.bin data8.gz
>file data8
data8: ASCII text
>cat data8
The password is 8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario
```
ssh bandit13@bandit.labs.overthewire.org -p 2220
```
## Level 14
*The password for the next level is stored in /etc/bandit_pass/bandit14 and can only be read by user bandit14. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. Note: localhost is a hostname that refers to the machine you are working on*
Desde bandit 13 hacemos lo siguiente:
```
>ssh -i sshkey.private bandit14@localhost
>cat /etc/bandit_pass/bandit14
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario
```
ssh bandit14@bandit.labs.overthewire.org -p 2220
```
## Level 15
*The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.*
```
>cat /etc/bandit_pass/bandit14 | nc 127.0.0.1 30000
Correct!
BfMYroe26WYalil77FoDi9qh59eK5xNr
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario
```
ssh bandit15@bandit.labs.overthewire.org -p 2220
```
## Level 16
*The password for the next level can be retrieved by submitting the password of the current level to port 30001 on localhost using SSL encryption.*
```
>cat /etc/bandit_pass/bandit15 | openssl s_client -connect localhost:30001 -quiet
depth=0 CN = localhost
verify error:num=18:self signed certificate
verify return:1
depth=0 CN = localhost
verify return:1
Correct!
cluFn7wTiGryunymYOu4RcffSxQluehd
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario
```
ssh bandit16@bandit.labs.overthewire.org -p 2220
```
## Level 17
```
>nmap localhost -A -p 31000-32000
Starting Nmap 7.40 ( https://nmap.org ) at 2020-10-12 18:55 CEST
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00030s latency).
Not shown: 996 closed ports
PORT      STATE SERVICE
31046/tcp open  unknown
31518/tcp open  unknown
31691/tcp open  unknown
31790/tcp open  unknown
31960/tcp open  unknown
>cat /etc/bandit_pass/bandit16 | openssl s_client -connect localhost:31790 -quiet
depth=0 CN = localhost
verify error:num=18:self signed certificate
verify return:1
depth=0 CN = localhost
verify return:1
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario usando la clave RSA obtenida
```
ssh -i key.rsa bandit17@bandit.labs.overthewire.org -p 2220
```
## Level 18
*There are 2 files in the homedirectory: passwords.old and passwords.new. The password for the next level is in passwords.new and is the only line that has been changed between passwords.old and passwords.new*
```
>diff passwords.new passwords.old
kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd
w0Yfolrc5bwjS4qw5mq1nnQi6mF03bii
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario usando la clave RSA obtenida
```
ssh bandit18@bandit.labs.overthewire.org -p 2220
```
## Level 19
*The password for the next level is stored in a file readme in the homedirectory. Unfortunately, someone has modified .bashrc to log you out when you log in with SSH.*
Desde nuestro sistema:
```
>ssh bandit18@bandit.labs.overthewire.org -p 2220 ls -la
drwxr-xr-x  2 root     root     4096 May  7 20:14 .
drwxr-xr-x 41 root     root     4096 May  7 20:14 ..
-rw-r--r--  1 root     root      220 May 15  2017 .bash_logout
-rw-r-----  1 bandit19 bandit18 3549 May  7 20:14 .bashrc
-rw-r--r--  1 root     root      675 May 15  2017 .profile
-rw-r-----  1 bandit19 bandit18   33 May  7 20:14 readme
>ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme
IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario usando la clave obtenida
```
ssh bandit19@bandit.labs.overthewire.org -p 2220
```
## Level 20
*To gain access to the next level, you should use the setuid binary in the homedirectory. Execute it without arguments to find out how to use it. The password for this level can be found in the usual place (/etc/bandit_pass), after you have used the setuid binary.*
Veremos que tenemos un SUID bin de bandit 20
```
>ls -l
-rwsr-x--- 1 bandit20 bandit19 7296 May  7 20:14 bandit20-do 
>./bandit20-do 
Run a command as another user.
  Example: ./bandit20-do id
>./bandit20-do whoami
bandit20
>./bandit20-do bash -p
bash-4.4$ id
uid=11019(bandit19) gid=11019(bandit19) euid=11020(bandit20) groups=11019(bandit19)
bash-4.4$ cat /etc/bandit_pass/bandit20
GbKksEFF4yrVs6il55v6gwY5aVje5f0j
bash-4.4$ 
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario usando la clave obtenida
```
ssh bandit20@bandit.labs.overthewire.org -p 2220
```
## Level 21
*There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).*
```
>cat /etc/bandit_pass/bandit20 | nc -lp 6000 &
>./suconnect 6000
Read: GbKksEFF4yrVs6il55v6gwY5aVje5f0j
Password matches, sending next password
gE269g2h3mw3pwgrj0Ha9Uoqen1c9DGr
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario usando la clave obtenida
```
ssh bandit21@bandit.labs.overthewire.org -p 2220
```
## Level 22
*A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.*
```
>cat /etc/cron.d/cronjob_bandit22
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
>cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
>cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario usando la clave obtenida
```
ssh bandit22@bandit.labs.overthewire.org -p 2220
```
## Level 23
*A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.*
```
>cat /etc/cron.d/cronjob_bandit23
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
>cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
>myname=bandit23
>mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)
>echo $mytarget
8ca319486bfbbc3663ea0fbe81326349
>cat /tmp/8ca319486bfbbc3663ea0fbe81326349
jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario usando la clave obtenida
```
ssh bandit23@bandit.labs.overthewire.org -p 2220
```
## Level 24
*A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.*
```
>cat /etc/cron.d/cronjob_bandit24
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
>cat /usr/bin/cronjob_bandit24.sh
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname
echo "Executing and deleting all scripts in /var/spool/$myname:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
done
> cd /var/spool/bandit24/
> nano uwu.sh 
> cat uwu.sh 
#!/bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/srbleu/pass
> touch /tmp/srbleu/pass
> chmod 777 /tmp/srbleu/pass
> cat /tmp/srbleu/pass
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario usando la clave obtenida
```
ssh bandit24@bandit.labs.overthewire.org -p 2220
```
## Level 25
*A daemon is listening on port 30002 and will give you the password for bandit25 if given the password for bandit24 and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 combinations, called brute-forcing.*
Hagamos un script:
```bash
#!/bin/bash for i in {0000..9999}; do
    echo "UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ" $i | 
    nc localhost 30002 | egrep -v "Exiting|Wrong|I am"
done
```
Ejecutar esto nos da la siguiente contraseña:
```
uNG9O58gUE7snukf3bvZ0rxhtnjzSGzG
``` 
Salimos a nuestro sistema y nos logeamos con el siguiente usuario usando la clave
```
ssh bandit25@bandit.labs.overthewire.org -p 2220
```
## Level 26
*Logging in to bandit26 from bandit25 should be fairly easy… The shell for user bandit26 is not /bin/bash, but something else. Find out what it is, how it works and how to break out of it.*
(Este nivel es bastante tricky)
Veamos la shell en el /etc/passwd
```
>cat /etc/passwd | grep bandit26
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
```
Tenemos la clave RSA del bandit26 pero al logearnos nos hace log out por la shell usada, veamos la shell que se usa:
```
#!/bin/sh

export TERM=linux

more ~/text.txt
exit 0
```
Tenemos que forzar el uso de more para poder logearnos, usmos v para spawnear vim, y luego !sh para poder tener shell acces, desde aquí es cuestion de leer la pass
```
5czgV9L3Xx8JPOyRbXh6lQbmIOWvPT6Z
```
Salimos a nuestro sistema y nos logeamos con el siguiente usuario usando la clave
```
ssh bandit26@bandit.labs.overthewire.org -p 2220
```
## Level 27
*Good job getting a shell! Now hurry and grab the password for bandit27!*
Podemos ejcutar comandos de la siguiente manera:
```
3ba3118a22e93127a4ed485be72ef5ea
```
## Level 28
*There is a git repository at ssh://bandit27-git@localhost/home/bandit27-git/repo. The password for the user bandit27-git is the same as for the user bandit27.*
Creamos una carpeta en /tmp y nos vamos a la misma 
```
git clone ssh://bandit27-git@localhost/home/bandit27-git/repo
cat repo/README
The password to the next level is: 0ef186ac70e04ea33b4c1853d2526fa2
```
Al siguiente
## Level 29
*There is a git repository at ssh://bandit28-git@localhost/home/bandit28-git/repo. The password for the user bandit28-git is the same as for the user bandit28.*
Creamos una carpeta en /tmp y nos vamos a la misma 
```
git clone ssh://bandit28-git@localhost/home/bandit27-git/repo
cat repo/README
```
Nos faltan datos:
```
# Bandit Notes
Some notes for level29 of bandit.

## credentials

- username: bandit29
- password: xxxxxxxxxx
```
Tirando de git log vemos este cambio
```
-- password: bbc96594b4e001778eee9975372716b2
+- password: xxxxxxxxxx
```
## Level 30
*There is a git repository at ssh://bandit29-git@localhost/home/bandit29-git/repo. The password for the user bandit29-git is the same as for the user bandit29.*
Creamos una carpeta en /tmp y nos vamos a la misma 
```
git clone ssh://bandit29-git@localhost/home/bandit27-git/repo
cat repo/README
```
Nos faltan datos:
```
# Bandit Notes
Some notes for bandit30 of bandit.

## credentials

- username: bandit30
- password: <no passwords in production!>
```
Podemos ver que existen otras branchs
```
git branch -r
  origin/HEAD -> origin/master
  origin/dev
  origin/master
  origin/sploits-dev
```
Cambiamos de brach
```
git checkout dev
```
Miramos ahora los logs
```
-- password: <no passwords in production!>
+- password: 5b90576bedb2cc04c86a9e924ce42faf
```
## Level 31
*There is a git repository at ssh://bandit30-git@localhost/home/bandit30-git/repo. The password for the user bandit30-git is the same as for the user bandit30.*
Creamos una carpeta en /tmp y nos vamos a la misma 
```
git clone ssh://bandit30-git@localhost/home/bandit27-git/repo
cat repo/README
```
Trolled:
```
just an epmty file... muahaha
```
Buscamos una tag 
```
git tag
 secret
git show secret
47e603bb428404d265f59c42920d81e5
```
## Level 32
*There is a git repository at ssh://bandit31-git@localhost/home/bandit31-git/repo. The password for the user bandit31-git is the same as for the user bandit31.*
Clonamos el repo y taly vemos el readme
```
This time your task is to push a file to the remote repository.

Details:
    File name: key.txt
    Content: 'May I come in?'
    Branch: master
```
Bien las intruscciones son claras
```
bandit31@bandit:/tmp/patetico/repo$ echo 'May I come in?' > key.txt
bandit31@bandit:/tmp/patetico/repo$ git add -f key.txt 
bandit31@bandit:/tmp/patetico/repo$ git commit -m "uwu"
```
Al hacer el push da error por lo siguiente:
```
remote: ### Attempting to validate files... ####
remote: 
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
remote: 
remote: Well done! Here is the password for the next level:
remote: 56a9bf19c63d650ce78e6ec0354ee45e
remote: 
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
remote: 
```
A siguiente
## Level 33
Ahora tenemos una restricted shell que pone en mayuscula lo que ponemos
Si hacemos uso de la variable $0 podemos escapar 
```
>>>$0
/bin/bash
bandit33@bandit:~$ cat /etc/bandit_pass/bandit33
c9c3199ddf4121b10cf581a98d51caee
```
Vayamos a por el ultimo

## Level 34
GG!
