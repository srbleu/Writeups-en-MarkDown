# Art

####  Can you find the flag? 

### Extracción

Extraemos el zip que nos dan del challenge y veamos que hay dentro

```bash
$ unzip -P hackthebox art.zip 
Archive:  inferno.zip
    inflating: inferno.txt      
```

### Decoding

Miramos que hay dentro de inferno.txt
```
RCdgXyReIjdtNVgzMlZ4ZnZ1PzFOTXBMbWwkakdGZ2dVZFNiYn08eyldeHFwdW5tM3Fwb2htZmUrTGJnZl9eXSNhYFleV1Z6PTxYV1ZPTnJMUUpJTkdrRWlJSEcpP2MmQkE6Pz49PDVZenk3NjU0MzIrTy8uJyYlJEgoIWclJCN6QH59dnU7c3JxdnVuNFVxamlubWxlK2NLYWZfZF0jW2BfWHxcW1pZWFdWVVRTUlFQMk5NRktKQ0JmRkU+JjxgQDkhPTw1WTl5NzY1NC0sUDAvby0sJUkpaWh+fSR7QSFhfXZ7dDpbWnZvNXNyVFNvbm1mLGppS2dgX2RjXCJgQlhXVnpaPDtXVlVUTXFRUDJOR0ZFaUlIR0Y/PmJCJEA5XT08OzQzODFVdnUtMiswLygnSysqKSgnfmZ8Qi8=
```
Parece base64 , decodifiquemoslo
```
D'`_$^"7m5X32Vxfvu?1NMpLml$jGFggUdSbb}<{)]xqpunm3qpohmfe+Lbgf_^]#a`Y^WVz=<XWVONrLQJINGkEiIHG)?c&BA:?>=<5Yzy765432+O/.'&%$H(!g%$#z@~}vu;srqvun4Uqjinmle+cKaf_d]#[`_X|\[ZYXWVUTSRQP2NMFKJCBfFE>&<`@9!=<5Y9y7654-,P0/o-,%I)ih~}${A!a}v{t:[Zvo5srTSonmf,jiKg`_dc\"`BXWVzZ<;WVUTMqQP2NGFEiIHGF?>bB$@9]=<;4381Uvu-2+0/('K+*)('~f|B/
```
Esto podria ser algún cryptolang o algun cipher , ciertos caracteres me recuerdan a algún reto de Malbolge probemos con un interprete online
```
http://malbolge.doleczek.pl/
```
Y sacamos la flag
```
HTB{!1t_1s_just_M4lb0lg3_l4ngu4g3!}
```
