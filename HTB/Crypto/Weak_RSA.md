# Weak RSA
*Can you decrypt the message and get the flag?*

Nos dan 2 archivos:
```
flag.enc: data
key.pub:  ASCII text
```
Siendo un reto de RSA lo primero que haremos sera tirar de RsaCtfTool
```
python /home/pj/Tools/RsaCtfTool/RsaCtfTool.py --publickey key.pub --uncipherfile flag.enc
STR : b'\x00\x02!\xcf\xb2\x98\x83\xb0o@\x9ag\x9aX\xa4\xe9{Dn(\xb2D\xbb\xcd\x06\x87\xd1x\xa8\xab\x87"\xbf\x86\xda\x06\xa6.\x04,\x89-)!\xb36W\x1e\x9f\xf7\xac\x9d\x89\xba\x90Q+\xacL\xfb\x8d~J9\x01\xbb\xcc\xf5\xdf\xac\x01\xb2{\xdd\xd3_\x1c\xa5SD\xa7YC\xdf\x9a\x18\xea\xdb4L\xf7\xcfU\xfa\x0b\xaap\x05\xbf\xe3/A\x00HTB{s1mpl3_Wi3n3rs_4tt4ck}'
```
Y ya tenemos nuestra flag
Flag: **HTB{s1mpl3_Wi3n3rs_4tt4ck}**
