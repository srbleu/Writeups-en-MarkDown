# LaCasaDePapel ![Avatar](https://www.hackthebox.eu/storage/avatars/509c1d6ddf04cf3d3f8054a564f2e93a_thumb.png)

### Initial Scan
```nmap
21/tcp   open  ftp      vsftpd 2.3.4
22/tcp   open  ssh      OpenSSH 7.9 (protocol 2.0)
| ssh-hostkey: 
|   2048 03:e1:c2:c9:79:1c:a6:6b:51:34:8d:7a:c3:c7:c8:50 (RSA)
|   256 41:e4:95:a3:39:0b:25:f9:da:de:be:6a:dc:59:48:6d (ECDSA)
|_  256 30:0b:c6:66:2b:8f:5e:4f:26:28:75:0e:f5:b1:71:e4 (ED25519)
80/tcp   open  http     Node.js (Express middleware)
|_http-title: La Casa De Papel
443/tcp  open  ssl/http Node.js Express framework
|_http-title: La Casa De Papel
| ssl-cert: Subject: commonName=lacasadepapel.htb/organizationName=La Casa De Papel
| Not valid before: 2019-01-27T08:35:30
|_Not valid after:  2029-01-24T08:35:30
|_ssl-date: TLS randomness does not represent time
| tls-alpn: 
|_  http/1.1
| tls-nextprotoneg: 
|   http/1.1
|_  http/1.0
6200/tcp open  lm-x?
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, GenericLines, NULL, RPCCheck: 
|     [34mPsy Shell v0.9.9 (PHP 7.2.10 
|     cli) by Justin Hileman
|     [39m
|   GetRequest: 
|     [34mPsy Shell v0.9.9 (PHP 7.2.10 
|     cli) by Justin Hileman
|     [39m
|     [30;43mPHP Warning: Use of undefined constant GET - assumed 'GET' (this will throw an Error in a future version of PHP) in phar://eval()'d code on line 1
|     [39;49m
|   HTTPOptions, RTSPRequest: 
|     [34mPsy Shell v0.9.9 (PHP 7.2.10 
|     cli) by Justin Hileman
|     [39m
|     [30;43mPHP Warning: Use of undefined constant OPTIONS - assumed 'OPTIONS' (this will throw an Error in a future version of PHP) in phar://eval()'d code on line 1
|     [39;49m
|   Help: 
|     [34mPsy Shell v0.9.9 (PHP 7.2.10 
|     cli) by Justin Hileman
|     [39m
|     [30;43mPHP Warning: Use of undefined constant HELP - assumed 'HELP' (this will throw an Error in a future version of PHP) in phar://eval()'d code on line 1
|_    [39;49m

```
### Enumeration

#### FTP
La version del FTP es la que es vulnerable a el CVE 2011-0762 aunque hemos comprobado que se ha parcheado

#### HTTP

### Exploit
