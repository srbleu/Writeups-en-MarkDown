# Event Horizon
*Our CEO's computer was compromised in a phishing attack. The attackers took care to clear the PowerShell logs, so we don't know what they executed. Can you help us?*

Si bien el log principal de powershell y el de amdmin se han limpiado el Operational sigue teniendo contenido, para parsear los logs desde Linux:
```
python /usr/local/bin/evtx_dump.py  Microsoft-Windows-PowerShell%4Operational.evtx  > ../Powershell.xml
```
Vemos un script powershell y en el comentada aparece la flag

Flag: **HTB{8Lu3_734m_F0r3v3R}**
