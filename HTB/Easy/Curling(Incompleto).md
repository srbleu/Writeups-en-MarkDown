# Curling ![Avatar](https://www.hackthebox.eu/storage/avatars/50cbb1d2e9b638140641af95a7582ef6_thumb.png)

## Initial Scan
```
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 8a:d1:69:b4:90:20:3e:a7:b6:54:01:eb:68:30:3a:ca (RSA)
|   256 9f:0b:c2:b2:0b:ad:8f:a1:4e:0b:f6:33:79:ef:fb:43 (ECDSA)
|_  256 c1:2a:35:44:30:0c:5b:56:6a:3f:a5:cc:64:66:d9:a9 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-generator: Joomla! - Open Source Content Management
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Home
```
## Enum
### HTTP
#### Joomscan
Joomscan reporta que la version es
```
[+] Detecting Joomla Version
[++] Joomla 3.8.8
```
Buscando vulns de la version encontramos un RCE y un LFI
```
RCE: CVE-2018-17856
LFI: CVE-2019-10945
```
