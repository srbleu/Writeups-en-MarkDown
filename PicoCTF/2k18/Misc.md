# Misc
## General Warmup 1
*If I told you your grade was 0x41 in hexadecimal, what would it be in ASCII?*
```bash
echo '41' | base16 -d
``` 
La respuesta es A 

Flag: picoCTF{A}

## General Warmup 2
*Can you convert the number 27 (base 10) to binary (base 2)?*

Podriamos usar una tool pero siendo tan bajito 27= 16*1 + 8*1 + 4*0 + 2*1 + 1*1

Flag: picoCTF{11011}

##  General Warmup 3
*What is 0x3D (base 16) in decimal (base 10).?*

Igual que antes un simple cambio de base 3*16 + D*1 = 48 + 13 

Flag: picoCTF{61}

## Resources
*We put together a bunch of resources to help you out on our website! If you go over there, you might even find a flag! https://picoctf.com/resources*

Vamos al link y alli esta la flag 

Flag: picoCTF{xiexie_ni_lai_zheli}

## Grep 1
*Can you find the flag in file? This would be really obnoxious to look through by hand, see if you can find a faster way*
```bash
cat file | grep picoCTF
```
Flag: picoCTF{grep_and_you_will_find_c709fa94}

## Net cat
*Using netcat (nc) will be a necessity throughout your adventure. Can you connect to 2018shell.picoctf.com at port 10854 to get the flag?*
```bash
nc 2018shell.picoctf.com 10854
```
Flag: picoCTF{NEtcat_iS_a_NEcESSiTy_c97963fe}

## Strings
*Can you find the flag in this file without actually running it?*
```bash
strings strings | grep picoCTF
```

## Pipe
*During your adventure, you will likely encounter a situation where you need to process data that you receive over the network rather than through a file. Can you find a way to save the output from this program and search for the flag? Connect with 2018shell.picoctf.com 34532.*
```bash
nc 2018shell.picoctf.com 34532 | grep picoCTF
```
Flag: picoCTF{almost_like_mario_b797f2b3}

## Grep 2
*This one is a little bit harder. Can you find the flag in /problems/grep-2_0_783d3e2c8ea2ebd3799ca6a5d28fc742/files on the shell server? Remember, grep is your friend.*
Para este ya hay que conectarse a la shell
```bash
cat /problems/grep-2_0_783d3e2c8ea2ebd3799ca6a5d28fc742/files/*/* | grep picoCTF
```
Flag:picoCTF{grep_r_and_you_will_find_24c911ab}

## Aca-Shell-A
*It's never a bad idea to brush up on those linux skills or even learn some new ones before you set off on this adventure! Connect with nc 2018shell.picoctf.com 27833.*
```bash
nc 2018shell.picoctf.com 27833
> echo 'Help Me!'
> cd secret
> ls
> rm *
> echo 'Drop it in!'
> cd ..
> cd executables
> ./dontLookHere
> whoami
> cd ..
> cp /tmp/TopSecret passwords
> cat /passwords/TopSecret
```
Flag: picoCTF{CrUsHeD_It_17ab99f5}

## Environ
*Sometimes you have to configure environment variables before executing a program. Can you find the flag we've hidden in an environment variable on the shell server?*
Vamos a la shell de la web y usamos el siguiete comando
```bash
env | grep pico
```
Flag:picoCTF{eNv1r0nM3nT_v4r14Bl3_fL4g_3758492}

## SSH-Keyz
*As nice as it is to use our webshell, sometimes its helpful to connect directly to our machine. To do so, please add your own public key to ~/.ssh/authorized_keys, using the webshell. The flag is in the ssh banner which will be displayed when you login remotely with ssh to with your username.*
Generamos una clave desde el server
```
@pico-2018-shell:~/.ssh$ ssh-keygen
### Rellenamos los datos
@local-shell: ssh -i ~/.ssh/pico user@2018shell4.picoctf.com
```
Y la flag estaria en el banner
Flag: picoCTF{who_n33ds_p4ssw0rds_38dj21}
## What base it is?
*To be successful on your mission, you must be able read data represented in different ways, such as hexadecimal or binary. Can you get the flag from this program to prove you are ready? Connect with nc 2018shell.picoctf.com 64706.*
Si bien puede que este planteado para hacer un script, el timer que te dan es suficiente para hacer el decoding a mano bin,hex,oct
Flag: picoCTF{delusions_about_finding_values_5b21aa05}
## You can't see me
*'...reading transmission... Y.O.U. .C.A.N.'.T. .S.E.E. .M.E. ...transmission ended...' Maybe something lies in /problems/you-can-t-see-me_2_cfb71908d8368e3062423b45959784aa.*
Dentro de la carpeta vemos esto
```
drwxr-xr-x   2 root       root        4096 Mar 25  2019 .
-rw-rw-r--   1 hacksports hacksports    57 Mar 25  2019 .  
drwxr-x--x 556 root       root       53248 Mar 25  2019 ..
```
Ese archivo llamado . parece ser la clave
```
grep -r "pico" .
```
Flag: picoCTF{j0hn_c3na_paparapaaaaaaa_paparapaaaaaa_093d6aff}
## Absolutely Relative
## In out error
## Learn gdb
## Roulette
## Store
## Script Me
