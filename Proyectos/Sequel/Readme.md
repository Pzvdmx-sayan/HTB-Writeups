Informe de Auditoría de Seguridad: Máquina "Sequel"

Fecha: 17 de abril de 2026
Auditor: Kali User (Tu nombre/alias)
Objetivo: 10.129.95.232
Severidad: <span style="color:red">CRÍTICA</span>
1. Resumen Ejecutivo

Durante la evaluación de seguridad de la máquina "Sequel", se identificó una vulnerabilidad crítica en el servicio de base de datos MariaDB. Debido a una configuración incorrecta, el sistema permitía el acceso remoto al usuario administrador (root) sin requerir ninguna contraseña. Esto permitió la exfiltración de información sensible (Flag) del sistema de manera inmediata.
2. Fase de Reconocimiento (Enumeración)

Se realizó un escaneo de puertos utilizando la herramienta nmap para identificar servicios expuestos.

Comando ejecutado:
nmap -sV 10.129.95.232

Resultado clave:

    Puerto 3306/TCP: Abierto (Servicio MariaDB).

3. Análisis de Vulnerabilidades

Se detectó una Mala Configuración de Seguridad (Security Misconfiguration) en el servicio MariaDB. El servicio aceptaba conexiones desde direcciones externas y no aplicaba una política de autenticación para la cuenta con mayores privilegios.
4. Explotación (Vectores de Ataque)

Para validar la vulnerabilidad, se utilizó el cliente de línea de comandos de MariaDB intentando una conexión directa como el usuario root.

Comando de acceso:
mysql -h 10.129.95.232 -u root --skip-ssl

Pasos realizados dentro de la base de datos:

    Enumeración de BD: SHOW DATABASES; (Se identifica la base de datos htb).

    Acceso a datos: USE htb;

    Listado de tablas: SHOW TABLES; (Se identifica la tabla config).

    Extracción de información: SELECT * FROM config;

5. Prueba de Impacto (Evidencia)

Se logró extraer la "Flag" del sistema, lo que demuestra que un atacante podría leer, modificar o eliminar cualquier dato almacenado en la infraestructura.

    Flag extraída:

6. Recomendaciones (Remediación)

Para mitigar este riesgo, se recomiendan las siguientes acciones inmediatas:

    Establecer Contraseña de Root: Asignar una contraseña robusta al usuario administrador del DBMS.

        Comando: ALTER USER 'root'@'%' IDENTIFIED BY 'NuevaContraseñaSegura';

    Restringir Acceso Remoto: Modificar el archivo de configuración (my.cnf o 50-server.cnf) para que el servicio solo escuche en 127.0.0.1 si no se requiere acceso externo.

    Habilitar SSL/TLS: Obligar a que todas las conexiones sean cifradas para evitar la interceptación de datos en tránsito.
