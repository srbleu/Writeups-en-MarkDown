# Logger
*A client reported that a PC might have been infected, as it's running slow. We've collected all the evidence from the suspect workstation, and found a suspicious trace of USB traffic. Can you identify the compromised data?*

Podemos ver que la captura de tráfico es de un teclado, para ver lo que se ha escrito en la captura usaremos [esta herramienta](https://github.com/WangYihang/UsbKeyboardDataHacker)

```
UsbKeyboardDataHacker.py keystrokes.pcapng
[+] Found : <CAP>htb{<CAP>i_<CAP>c4n_533_<CAP>y<CAP>ou<CAP>r_<CAP>k3y2<CAP>}
```
Aplicando la capitalizacion que vemos tenemos dos candidatos de flag
```
HTB{i_C4N_533_yOUr_K3Y2}
htb{I_c4n_533_YouR_k3y2}
```
Como el formato de la plataforma es con HTB en mayusculas probemos con ella y listo

Flag: **HTB{i_C4N_533_yOUr_K3Y2}**
