# Beep ![Avatar](https://www.hackthebox.eu/storage/avatars/995f465295b99869fce21ecadea4604c_thumb.png)

### Initial Scan

```
22/tcp    open  ssh        OpenSSH 4.3 (protocol 2.0)
| ssh-hostkey: 
|   1024 ad:ee:5a:bb:69:37:fb:27:af:b8:30:72:a0:f9:6f:53 (DSA)
|_  2048 bc:c6:73:59:13:a1:8a:4b:55:07:50:f6:65:1d:6d:0d (RSA)
25/tcp    open  smtp       Postfix smtpd
|_smtp-commands: beep.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, ENHANCEDSTATUSCODES, 8BITMIME, DSN, 
80/tcp    open  http       Apache httpd 2.2.3
|_http-server-header: Apache/2.2.3 (CentOS)
|_http-title: Did not follow redirect to https://10.10.10.7/
|_https-redirect: ERROR: Script execution failed (use -d to debug)
110/tcp   open  pop3       Cyrus pop3d 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4
|_pop3-capabilities: APOP RESP-CODES LOGIN-DELAY(0) PIPELINING EXPIRE(NEVER) STLS IMPLEMENTATION(Cyrus POP3 server v2) AUTH-RESP-CODE USER UIDL TOP
111/tcp   open  rpcbind    2 (RPC #100000)
143/tcp   open  imap       Cyrus imapd 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4
|_imap-capabilities: ATOMIC THREAD=ORDEREDSUBJECT ACL CHILDREN OK IMAP4 URLAUTHA0001 X-NETSCAPE LIST-SUBSCRIBED ID ANNOTATEMORE UIDPLUS Completed IMAP4rev1 CONDSTORE SORT=MODSEQ STARTTLS QUOTA NO CATENATE UNSELECT RENAME LITERAL+ NAMESPACE LISTEXT THREAD=REFERENCES IDLE RIGHTS=kxte SORT BINARY MAILBOX-REFERRALS MULTIAPPEND
443/tcp   open  ssl/https?
|_ssl-date: 2020-07-29T08:26:37+00:00; +10s from scanner time.
878/tcp   open  status     1 (RPC #100024)
993/tcp   open  ssl/imap   Cyrus imapd
|_imap-capabilities: CAPABILITY
995/tcp   open  pop3       Cyrus pop3d
3306/tcp  open  mysql      MySQL (unauthorized)
4190/tcp  open  sieve      Cyrus timsieved 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4 (included w/cyrus imap)
4445/tcp  open  upnotifyp?
4559/tcp  open  hylafax    HylaFAX 4.3.10
5038/tcp  open  asterisk   Asterisk Call Manager 1.1
10000/tcp open  http       MiniServ 1.570 (Webmin httpd)
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1)
```

### Enumeration

#### HTTP
```bash
gobuster -u https://10.10.10.7/ -w /usr/share/dirb/wordlists/big.txt -t 100 -k
/.htaccess (Status: 403)
/.htpasswd (Status: 403)
/admin (Status: 301)
/cgi-bin/ (Status: 403)
/configs (Status: 301)
/favicon.ico (Status: 200)
/help (Status: 301)
/images (Status: 301)
/lang (Status: 301)
/libs (Status: 301)
/mail (Status: 301)
/modules (Status: 301)
/panel (Status: 301)
/recordings (Status: 301)
/robots.txt (Status: 200)
/static (Status: 301)
/themes (Status: 301)
/vtigercrm (Status: 301)
/var (Status: 301)
```
Accediendo a la web vemos que la tecnología usada es Elastix

