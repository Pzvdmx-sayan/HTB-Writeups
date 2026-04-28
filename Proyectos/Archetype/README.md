Informe de Auditoría de Seguridad: Archetype (Hack The Box)

Fecha: 28 de abril de 2026

Auditor: Gemini & User

Objetivo: Evaluación de seguridad de la infraestructura "Archetype"

Nivel de Riesgo: CRÍTICO
1. Resumen Ejecutivo

Se ha realizado una prueba de penetración en el host 10.129.101.132. La evaluación reveló múltiples fallos de configuración que permitieron el compromiso total del sistema (Privilegios de SYSTEM). El ataque comenzó con una enumeración de servicios SMB y culminó con el robo de credenciales administrativas en el historial de comandos de PowerShell.
2. Resumen de Hallazgos y Preguntas HTB (Tasks)

Para facilitar el seguimiento de la plataforma, aquí se detallan los puntos clave identificados durante la intrusión:
Task	Descripción	Hallazgo / Respuesta
T1	Puerto de Base de Datos	1433 (Microsoft SQL Server)
T2	Recurso SMB no administrativo	backups
T3	Contraseña en archivo SMB	M3g4c0rp123
T4	Herramienta de conexión SQL	mssqlclient.py (Impacket)
T5	Procedimiento SQL para RCE	xp_cmdshell
T6	Comando PowerShell de descarga	wget (Invoke-WebRequest)
T7	Herramienta de escalada (Genérica)	winPEAS
T8	Archivo con clave de Admin	ConsoleHost_history.txt
3. Cadena de Ataque Detallada (Kill Chain)
A. Reconocimiento y Enumeración

El escaneo inicial mediante nmap reveló servicios críticos expuestos: SMB (445), MSSQL (1433) y WinRM (5985). Al inspeccionar el servicio SMB de forma anónima, se localizó el recurso \backups.
B. Explotación de Credenciales Expuestas

Dentro del recurso compartido, se analizó el archivo prod.dtsConfig, un archivo de configuración que contenía credenciales de un usuario de servicio en texto claro.

    Usuario: sql_svc

    Contraseña: M3g4c0rp123

C. Ejecución Remota de Comandos (RCE)

Utilizando mssqlclient.py, se obtuvo acceso al motor de base de datos. Se procedió a habilitar el procedimiento xp_cmdshell, lo que permitió ejecutar comandos en el sistema operativo Windows subyacente. Mediante una cadena de comandos de PowerShell, se transfirió un binario de nc.exe para establecer una Reverse Shell.
D. Post-Explotación y Escalada de Privilegios

Una vez dentro como sql_svc, se realizó una auditoría de archivos locales. Se descubrió que el administrador había dejado credenciales sensibles en el historial de la consola:
C:\Users\sql_svc\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt

Utilizando wmiexec.py con las credenciales de administrador encontradas (MEGACORP_4dm1n!!), se logró el acceso total como NT AUTHORITY\SYSTEM.
4. Evidencias (Banderas)

    User Flag: 3e7b102e78218e935bf3f4951fec21a3

    Root Flag: b91ccec3305e98240082d4474b848528

5. Recomendaciones (Remediación)

    Seguridad en SMB: Deshabilitar el acceso de invitados (guest access) a recursos compartidos y asegurar que no existan archivos de configuración con credenciales en texto claro.

    Hardening de SQL Server: El procedimiento xp_cmdshell debe permanecer deshabilitado y el servicio de SQL debe ejecutarse con el menor privilegio posible.

    Higiene de PowerShell: Implementar políticas para limpiar el historial de comandos (ConsoleHost_history.txt) o evitar que se guarden comandos que contengan parámetros de autenticación.

    Gestión de Privilegios: Cambiar la contraseña del administrador local y utilizar contraseñas complejas que no sigan patrones predecibles.
