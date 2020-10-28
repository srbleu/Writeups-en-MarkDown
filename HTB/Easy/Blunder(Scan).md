# Blunder ![Avatar](https://www.hackthebox.eu/storage/avatars/6437ea67350beceeb5c313f386bd1abe_thumb.png)

## Initial Scan
```
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-generator: Blunder
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Blunder | A blunder of interesting facts
```
## Enum
### HTTP
```
/.htaccess (Status: 403)
/.htpasswd (Status: 403)
/0 (Status: 200)
/LICENSE (Status: 200)
/about (Status: 200)
/admin (Status: 301)
/cgi-bin/ (Status: 301)
/robots.txt (Status: 200)
/server-status (Status: 403)
/usb (Status: 200)
```
