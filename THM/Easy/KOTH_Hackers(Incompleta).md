# Hackers
Por ahora paso de seguir con esta máquina es todo bruteforcing 
## Initial Scan
```
21/tcp   open  ftp     vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 ftp      ftp           400 Apr 29 03:57 note
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.9.95.54
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 ff:ea:b0:58:35:79:df:b3:c1:57:01:43:09:be:2a:d5 (RSA)
|   256 3b:ff:4a:88:4f:dc:03:31:b6:9b:dd:ea:69:85:b0:af (ECDSA)
|_  256 fa:fd:4c:0a:03:b6:f7:1c:ee:f8:33:43:dc:b4:75:41 (ED25519)
80/tcp   open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Ellingson Mineral Company
9999/tcp open  abyss?
| fingerprint-strings: 
|   FourOhFourRequest, GetRequest, HTTPOptions: 
|     HTTP/1.0 200 OK
|     Date: Tue, 18 Aug 2020 16:33:21 GMT
|     Content-Length: 1
|     Content-Type: text/plain; charset=utf-8
|   GenericLines, Help, Kerberos, LDAPSearchReq, LPDString, RTSPRequest, SIPOptions, SSLSessionReq, TLSSessionReq, TerminalServerCookie: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|_    Request

```
## Enum
### FTP
El login anonimo esta habilitado veamos hasta donde tenemos acceso
#### Note
```
Note:
Any users with passwords in this list:
love
sex
god
secret
will be subject to an immediate disciplinary hearing.
Any users with other weak passwords will be complained at, loudly.
These users are:
rcampbell:Robert M. Campbell:Weak password
gcrawford:Gerard B. Crawford:Exposing crypto keys, weak password
```
#### Bruteforce 
```
hydra -L users.txt -P /home/pj/rockyou.txt ftp://10.10.161.14
[21][ftp] host: 10.10.161.14   login: rcampbell   password: milagros
```

### HTTP
#### Gobusting
```
/backdoor (Status: 301)
/contact (Status: 301)
/img (Status: 301)
/news (Status: 301)
/robots.txt (Status: 200)
/staff (Status: 301)
```

#### robots.txt
Tenemos el siguiente mensaje
```
Skiddies keep out.
Any unauthorised access will be forwarded straight to Richard McGill FBI and you WILL be arrested.
- plague
```
plague podria ser un usuario


# Flags
## Flag 1
En el ftp hay un archivo llamado .flag con la primera de las flags

## Flag 2
En el css de la pagina esta la segunda flag si lo leemos podemos verla 

## Flag 3
