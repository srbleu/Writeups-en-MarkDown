# Marshal in the Middle
*The security team was alerted to suspicous network activity from a production web server.*
*Can you determine if any data was stolen and what it was?*

Nos dan los logs de bro, un pem y un pcap , usaremos el secret.log que nos dan como premaster log a fin de desencriptar el trafico. Para hacer esto vamos a Preferencias->TLS->Pre-Master-Secret log y metemos el secret.log


Ahora vamos a hechar un ojo a los protocolos que se usan, nos interesan sobre todo HTTP,DNS y TLS.

Vemos un paquete DNS bastante interesante:
```
10.10.20.13	10.10.20.1	DNS	77	Standard query 0x8a9b A mysql-m1.prod.htb	53
10.10.20.1	10.10.20.13	DNS	93	Standard query response 0x8a9b A mysql-m1.prod.htb A 10.10.20.14	60472
```
Estos dos paquetes acotan un poco nuestra busqueda concretamente a 2 ips la del que hace la consulta como ip.src y la del servidor de produccion como ip.dest
```
ip.addr == 10.10.20.14 || ip.addr == 10.10.20.13
```
Se ve que el 10.10.20.14 no sale en la captura asñi que el filtro queda en:
```
ip.addr == 10.10.20.13
```
Miremos el trafico HTTP y vemos 3 accesos a pastebins
```
https://pastebin.com/dsDhyVZU
https://pastebin.com/cUjmHRYK
https://pastebin.com/rLqy9iXC
```
Parece que estan los 3 caidos sigamos viendo desde el pcap entonces, veremos que se han hecho 3 POST request a pastebin

Un /etc/passwd
```
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/bin/false
systemd-network:x:101:103:systemd Network Management,,,:/run/systemd/netif:/bin/false
systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd/resolve:/bin/false
systemd-bus-proxy:x:103:105:systemd Bus Proxy,,,:/run/systemd:/bin/false
syslog:x:104:108::/home/syslog:/bin/false
_apt:x:105:65534::/nonexistent:/bin/false
messagebus:x:106:110::/var/run/dbus:/bin/false
lxd:x:107:65534::/var/lib/lxd/:/bin/false
uuidd:x:108:112::/run/uuidd:/bin/false
dnsmasq:x:109:65534:dnsmasq,,,:/var/lib/misc:/bin/false
sshd:x:110:65534::/var/run/sshd:/usr/sbin/nologin
madmin:x:1000:1000:madmin,,,:/home/madmin:/bin/bash
```
Un /etc/shadow
```
root:$6$fNidshVk$LVuSjtql5GTGQQM7zxsrbCI4MfXEHzjT6j3v51jHj4W/RfUUWW1F97oljXUXhMThyf2up/B/wRhkkG.EnMbnL/:17404:0:99999:7:::
daemon:*:17403:0:99999:7:::
bin:*:17403:0:99999:7:::
sys:*:17403:0:99999:7:::
sync:*:17403:0:99999:7:::
games:*:17403:0:99999:7:::
man:*:17403:0:99999:7:::
lp:*:17403:0:99999:7:::
mail:*:17403:0:99999:7:::
news:*:17403:0:99999:7:::
uucp:*:17403:0:99999:7:::
proxy:*:17403:0:99999:7:::
www-data:*:17403:0:99999:7:::
backup:*:17403:0:99999:7:::
list:*:17403:0:99999:7:::
irc:*:17403:0:99999:7:::
gnats:*:17403:0:99999:7:::
nobody:*:17403:0:99999:7:::
systemd-timesync:*:17403:0:99999:7:::
systemd-network:*:17403:0:99999:7:::
systemd-resolve:*:17403:0:99999:7:::
systemd-bus-proxy:*:17403:0:99999:7:::
syslog:*:17403:0:99999:7:::
_apt:*:17403:0:99999:7:::
messagebus:*:17403:0:99999:7:::
lxd:*:17403:0:99999:7:::
uuidd:*:17403:0:99999:7:::
dnsmasq:*:17403:0:99999:7:::
sshd:*:17403:0:99999:7:::
madmin:$6$ushKC9bI$sWvvuq4IcD.Rb/Mt.lIfKReFZcVy2niErwNsPPO3yoQso5I0tMn6bABU9Tzg71Ov3/bMoGERi3.RMr7u1XOgF1:17404:0:99999:7:::
```
Una lista de tarjetas de credito
```
IssuingNetwork,CardNumber
American Express,345806846723249
American Express,345390632937883
American Express,348537668979836
American Express,377053228050054
American Express,376583316960401
American Express,370728090418771
American Express,343089157698829
American Express,376783960099486
American Express,370925614011310
American Express,345655064666899
American Express,349526416252983
American Express,374137601650417
American Express,379459312870752
American Express,342002781858009
American Express,371548417517132
American Express,374215542017076
American Express,378061197392448
American Express,373623819098896
American Express,378714893017707
American Express,372204783826157
American Express,374543879869094
American Express,347256757140959
American Express,344113218649966
American Express,370438099582029
American Express,379074114959362
American Express,376214359658856
American Express,374392766168545
American Express,378120275674968
American Express,377653788608231
American Express,372228747692280
American Express,379712206774419
American Express,371170584736336
American Express,341821906223364
American Express,346031816502270
American Express,342388871615015
American Express,342297596567577
American Express,371116813447823
American Express,347275681898029
American Express,340159467728765
American Express,341291086603530
American Express,370154070448409
American Express,370945625943350
American Express,346669026997960
American Express,345555131951417
American Express,370679406346331
American Express,374039336944042
American Express,376358640457337
American Express,340489816646982
American Express,376988515894717
American Express,343938540360150
American Express,378753104699614
American Express,371067527561203
American Express,372071568257688
American Express,371298336467262
American Express,377742701779979
American Express,370606011245970
American Express,373298847804357
American Express,379386423260799
American Express,375746261865385
American Express,349236877420497
American Express,377277758621600
American Express,340515950981783
American Express,370380522063476
American Express,347840973813653
American Express,343732320743671
American Express,344856839404977
American Express,378553260917242
American Express,375171892130283
American Express,347077947108072
American Express,374065404228380
American Express,349572089871063
American Express,340873970916598
American Express,376501051822032
American Express,378635675028929
American Express,372262705444244
American Express,377327829524687
American Express,347637516705986
HTB{Th15_15_4_F3nD3r_Rh0d35_M0m3NT!!}
American Express,343588840524078
American Express,375354542667439
American Express,342786650506356
American Express,376519541622051
American Express,372925141702629
American Express,379064920049805
American Express,372062866981368
American Express,340119293062854
American Express,377622183441043
American Express,345596477927538
American Express,346120357010486
American Express,348743175358502
American Express,348265083838326
American Express,374165182475318
American Express,343413470215022
American Express,377835450116425
American Express,343319576934756
American Express,349097271174704
American Express,343047062516676
American Express,347837794215977
American Express,377905537353414
American Express,343336645380480
American Express,349722904687745
American Express,379439377204840
American Express,371098010402175
American Express,341711119881514
American Express,379155339156478
American Express,346499893173952
American Express,379822410604029
American Express,378401969278765
American Express,376187523024900
American Express,371560862816232
American Express,374688712671981
American Express,346567385784039
American Express,349677499992027
American Express,378863874883072
American Express,370538962014776
American Express,378695319664554
American Express,377700801047184
American Express,341490109937152
American Express,375116305929698
American Express,376293550656281
American Express,376184934284512
American Express,370692215483682
American Express,341484791940103
American Express,341379048961417
American Express,374162791962349
American Express,370307175675108
American Express,349013931675378
American Express,347949547383489
American Express,342996616020703
American Express,349356556270045
American Express,346684782309441
American Express,379871086475610
American Express,345199109118715
American Express,340216820858984
American Express,349888469898835
American Express,346365151301637
American Express,378265272063804
American Express,349959481746698
American Express,348263119237951
American Express,373484474433864
American Express,377663560742140
American Express,370212530971871
American Express,376863650124206
American Express,373727306606895
American Express,344917353013352
American Express,341618870946529
American Express,349386219430900
American Express,378491822173402
American Express,377749742143933
American Express,343787312359902
American Express,342834615655924
American Express,373220167257297
American Express,342409875100604
American Express,374623961952105
American Express,342343248508898
American Express,370014624976364
American Express,370634519297087
American Express,378686083206003
American Express,345301479069094
American Express,341653062519404
American Express,344466041049600
American Express,340831124516177
American Express,371352670040878
American Express,376506773521960
American Express,373412041523057
American Express,371761796232138
American Express,372758692991600
American Express,375081018662582
American Express,379755239006424
American Express,379846246153760
American Express,371606808641125
American Express,348577292127113
American Express,374145045728368
American Express,347077718247133
American Express,340453378523583
American Express,344546944525481
American Express,377030750473656
American Express,371832335323686
American Express,348749016952684
American Express,373007620504443
American Express,376917237325536
American Express,345005318377549
American Express,346253765527997
American Express,349673285691824
American Express,379556545602579
American Express,344722223105333
American Express,349796314273585
American Express,344803589396672
American Express,372038817288624
American Express,343935624567445
American Express,342699774398016
American Express,377323872814232
American Express,377540985647023
American Express,347894643938739
American Express,379439395658019
American Express,376650833504747
American Express,343979751964728
American Express,377648365324335
American Express,377309456167584
American Express,347081651786503
American Express,347727257462630
American Express,344419972274507
American Express,370147246447792
American Express,375039789680878
American Express,346840335828432
American Express,346557187869278
American Express,340511052332744
American Express,378147342634378
American Express,347894225651353
American Express,377048863390823
American Express,378984721869708
American Express,376893345466290
American Express,340993435902015
American Express,345021743290579
American Express,372216617148053
American Express,345721594543956
American Express,370701456996490
American Express,378075075335832
American Express,347193033978698
American Express,378449242282864
American Express,347225645203899
American Express,374757600945979
American Express,342834096127575
American Express,347662201357208
American Express,343574247594591
American Express,370566322534167
American Express,372334356047228
American Express,344289113810802
American Express,348157167680627
American Express,378139627479017
American Express,341433918625899
American Express,375054585319852
American Express,375232023349653
American Express,371277165625117
American Express,375201533060415
American Express,370486975047062
American Express,345341443311941
American Express,370079377557471
American Express,348277553108999
American Express,348942096401546
American Express,340139468957183
American Express,375501135914772
American Express,348469770686390
American Express,372121835443015
American Express,342572197088771
American Express,349239713481020
American Express,379395776757541
American Express,377424909245976
American Express,346716240079230
American Express,342897267037956
American Express,372256457425906
American Express,347780418120357
American Express,345626501272781
American Express,348313982208785
American Express,377268832627986
American Express,348464633254247
American Express,345862988784051
American Express,372478188557829
American Express,346931472667467
American Express,376210287092911
American Express,349669214408298
American Express,349589127207480
American Express,373465045108259
American Express,345993581429677
American Express,379055189988279
American Express,342079255260219
American Express,372148370619824
American Express,341669065343451
American Express,343887050822002
American Express,375806364224144
American Express,341482592864373
American Express,343996994073075
American Express,348241530301408
American Express,340508494078869
American Express,341222588779064
American Express,341732458268378
American Express,342215785733206
American Express,375206484753587
American Express,348757630021869
American Express,375207977915915
American Express,378757965166902
American Express,371416072169864
American Express,371472284470210
American Express,344313444200085
American Express,371633951327088
American Express,373356508545394
American Express,346745901450190
American Express,375181398034764
American Express,348193944767049
American Express,379595687856023
American Express,347620188567868
American Express,371310095007468
American Express,378682000836261
American Express,374595741589813
American Express,370884641976377
American Express,371607107266291
American Express,377652257867146
American Express,376637079066374
American Express,345584266046595
American Express,340067183054384
American Express,341277850000334
American Express,341015814446098
American Express,342533390773452
American Express,376192394344189
American Express,343383111886624
American Express,374675072100834
American Express,348256685835653
American Express,372575544986608
American Express,376401373745583
American Express,342379126565899
American Express,346049017594391
American Express,373038486671992
American Express,343457158735405
American Express,340704529133239
American Express,377579823735301
American Express,374967588156800
American Express,372686112667320
American Express,341984840780285
American Express,373958109083988
American Express,371282587212338
American Express,371274146115201
American Express,375883096203118
American Express,347301054097666
American Express,343300721511053
American Express,340255289396029
American Express,375990362325374
American Express,343370733453560
American Express,342287408842862
American Express,348804061474645
American Express,371637510509326
American Express,378703796776774
American Express,349472306877482
American Express,344780438642987
American Express,378027734086116
American Express,346466146518556
American Express,343220729798991
American Express,378229907226962
American Express,374667417516923
American Express,343467230099563
American Express,349511258864699
American Express,349511141464095
American Express,370006599144259
American Express,377617826115186
American Express,379169502147297
American Express,373496044312105
American Express,377027560130781
American Express,374894851366082
American Express,349229238497916
American Express,372634070441056
American Express,375582667693786
American Express,375027644539694
American Express,346571317871203
American Express,373404070015592
American Express,342932357360933
American Express,378788416228850
American Express,346523832315760
American Express,346555775031558
American Express,377689619342913
American Express,372545677253019
American Express,378959598323203
American Express,348334757958008
American Express,348928076558314
American Express,343815776077452
American Express,371651434313657
American Express,373385669824926
American Express,377587129014446
American Express,349961591775088
American Express,377262527714142
American Express,374353905627247
American Express,344127718183706
American Express,370394640760004
American Express,342501026140156
American Express,371990240035645
American Express,344045629467283
American Express,372417880208372
American Express,376848472499589
American Express,348836111506778
American Express,341253206773262
American Express,379813062281463
American Express,340389988094393
American Express,348861999184142
American Express,378272683395502
American Express,378913030244634
American Express,371745098486450
American Express,347365349266279
American Express,341650305850257
American Express,345512937435434
American Express,345034766920956
American Express,376462350476477
American Express,370601311448960
American Express,378323147198691
American Express,372269640352689
American Express,349501478749697
American Express,346605935208784
American Express,347016994528519
American Express,370662025937227
American Express,377697725813677
American Express,349751884430099
American Express,373559452294766
American Express,347293629507818
American Express,346944733058632
American Express,346540195773088
American Express,377646253436773
American Express,378137477976348
American Express,371979298957057
American Express,347244087119885
American Express,370828135962682
American Express,344635944815370
American Express,371256715300140
American Express,379100809243737
American Express,340470483320994
American Express,376426496187447
American Express,374314125645476
American Express,348029280065251
American Express,375398240964424
American Express,379666458903185
American Express,345219721518272
American Express,349262935875001
American Express,372319583768886
```
En esta ultima esta la flag

Flag: **HTB{Th15_15_4_F3nD3r_Rh0d35_M0m3NT!!}**
