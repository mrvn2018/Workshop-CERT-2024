<center>

# **THREAT HUNTING PLAYBOOK**
> Guia Para Hunters:

|Elaborado por|
|-|
|Marvin Amador|
|Threat Hunter|
 </center>
 
##	Query
```
(process.name:schtasks.exe and process.command_line:**Workshop**)
```
```
(process.name:reg.exe and process.parent.pid:5928)
```
```
(process.name:reg.exe and process.command_line:**Workshop**)
```
```
(process.name:reg.exe and process.command_line:**Kernel**) or (process.parent.name:reg.exe and process.parent.command_line:**Kernel**)
```
```
(process.name:reg.exe and process.pid:6032) or (process.parent.name:cmd.exe and process.parent.pid:5928)
```
```
(process.name:net.exe and process.parent.pid:5928)
```
```
(process.name:vssadmin.exe and process.parent.pid:5928)
```
```
(process.name:wevtutil.exe and process.parent.pid:5928)
```
```
(process.name:nltest.exe and process.parent.pid:5928)
```
```
(process.name:ARP.EXE and process.parent.pid:5928)
```
```
(process.name:WMIC.exe and process.command_line:**AntiVirusProduct**)
```

## Configuración de los filtros:

| process.parent.name | process.name | registry.key | registry.data.strings | process.command_line |
|---------------------|--------------|--------------|------------------------|-----------------------|
| -              | -       | -       | -                 | -                |

