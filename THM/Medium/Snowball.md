# Snowball
## Initial Scan
```
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.11.4.77
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 3f:91:28:84:7b:f4:8a:87:4c:d0:36:f0:c1:e1:f1:1b (RSA)
|   256 d5:04:ab:a7:76:6d:3a:cd:64:19:cd:85:9a:de:81:cb (ECDSA)
|_  256 26:2b:4f:6d:4a:da:6e:b1:6c:f4:28:ae:38:f2:b9:8a (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
```
## Enum
### FTP
Tiene login Anonymous habilitado lo que nos permite acceder a un archivo llamado notes con la siguiente info
```
Hey Cult, 
Please change your password as it was a part of an internal data breach. Your
password : cu73_15_my_c0v3r! is not secure anymore

-Snowball
ref no : cmIfQoba1bn84BwEh_p7N9E5TDW1HVM7ikCbZd372UQ
```
### HTTP
#### Gobusting
```
/.htpasswd (Status: 403)
/.htaccess (Status: 403)
/server-status (Status: 403)
/snowball (Status: 301)
```
#### Snowball
##### Gobusting
```
/.htpasswd (Status: 403)
/.htaccess (Status: 403)
/yes (Status: 301)
```
#### Yes
##### Gobusting
```
/.htaccess (Status: 403)
/.htpasswd (Status: 403)
/please (Status: 301)
```
#### Please
Podemos ver un archivo con un hexdump llamado please.txt
## Privesc
