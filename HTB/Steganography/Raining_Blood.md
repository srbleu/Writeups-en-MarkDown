# Raining Blood
*Can you find the hidden message?*

Nos dan un archivo de audio , si aplicamos binwalk veremos que hay un jpg en su interior
```
binwalk RainingBlood.mp3

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
257           0x101           JPEG image data, JFIF standard 1.01

```
Forcemos la extracción
```
binwalk RainingBlood.mp3 --dd=".*"
```
Tenemos una imagen de slayer que parece ser un RabbitHole, tirando de strings encontramos uno que me llama la atencion:
```
f<SFRCe2gxZGQxbmdfZDR0NF9iM3R3MzNuX21wM19mcjRtM3NfaXNfbm90X2Z1bm55ISF9Cg==
```
Podria ser un base64 si quitaramos las dos primeras letras.
```
SFRCe2gxZGQxbmdfZDR0NF9iM3R3MzNuX21wM19mcjRtM3NfaXNfbm90X2Z1bm55ISF9Cg==
```
Efectivamente si decodificamos veremos la flag.

Flag: **HTB{h1dd1ng_d4t4_b3tw33n_mp3_fr4m3s_is_not_funny!!}**
