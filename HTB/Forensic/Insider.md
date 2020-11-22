# Insider
*A potential insider threat has been reported, and we need to find out what they accessed. Can you help?* 

Es un reto de browser forensics , concretamente de firefox, lo primero será acceder a los perfiles, hay 2:
```
2542z9mo.default-release  yodxf5e0.default
```
El segundo esta vacio, el primero es mas suculento
```
addons.json               cookies.sqlite-wal          gmp-gmpopenh264/    places.sqlite-shm                   storage/
addonStartup.json.lz4     crashes/                    gmp-widevinecdm/    places.sqlite-wal                   storage.sqlite
bookmarkbackups/          datareporting/              handlers.json       prefs.js                            times.json
broadcast-listeners.json  extension-preferences.json  key4.db             protections.sqlite                  weave/
cert9.db                  extensions/                 logins.json         search.json.mozlz4                  webappsstore.sqlite
compatibility.ini         extensions.json             minidumps/          security_state/                     webappsstore.sqlite-shm
containers.json           favicons.sqlite             parent.lock         sessionCheckpoints.json             webappsstore.sqlite-wal
content-prefs.sqlite      favicons.sqlite-shm         permissions.sqlite  sessionstore-backups/               xulstore.json
cookies.sqlite            favicons.sqlite-wal         pkcs11.txt          shield-preference-experiments.json
cookies.sqlite-shm        formhistory.sqlite          places.sqlite       SiteSecurityServiceState.txt
```
Tenemos un archivo de key4.db que no se puede crackear con mozilla2john, por suerte [firepwd](https://github.com/lclevy/firepwd) viene al rescate. 

Al lanzarlo vemos lo siguiente:
```
decrypting login/password pairs
   http://acc01:8080:b'admin',b'HTB{ur_8RoW53R_H157Ory}
```
Flag: **HTB{ur_8RoW53R_H157Ory}**
