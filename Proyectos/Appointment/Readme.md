Informe de Auditoría de Seguridad: Appointment (Hack The Box)

Fecha: 16 de Abril, 2026

Severidad: Crítica

Vulnerabilidad: SQL Injection (Authentication Bypass)
1. Resumen Ejecutivo

Durante la evaluación de la máquina "Appointment", se identificó una vulnerabilidad de inyección SQL de severidad crítica en el formulario de inicio de sesión principal. Esta falla permite a un atacante no autenticado evadir los mecanismos de control de acceso y obtener privilegios de administrador, comprometiendo la confidencialidad de la información almacenada (Flag).
2. Fase de Reconocimiento (Reconnaissance)
2.1 Escaneo de Puertos

Se realizó un escaneo mediante Nmap para identificar servicios activos:

    Puerto 80/TCP: Abierto (Servicio HTTP).

    Servidor Web: Apache httpd 2.4.38 (Debian).

2.2 Enumeración de Directorios

Utilizando Gobuster, se descubrieron los siguientes recursos:

    /index.php (Página de inicio/login).

    /css/, /js/, /images/ (Directorios de recursos con Directory Listing habilitado).

3. Análisis de Vulnerabilidades
ID: CWE-89 - SQL Injection

El formulario de login no sanitiza correctamente los caracteres especiales en el campo de nombre de usuario. Al utilizar una base de datos SQLite, el sistema es vulnerable a comentarios de línea y lógica booleana.

Vector de ataque:
El campo username permite la inserción de payloads que alteran la consulta SQL original.
4. Explotación (Exploitation)
Paso 1: Prueba de concepto (PoC)

Se introdujo el siguiente payload en el campo de usuario para forzar una respuesta verdadera (TRUE) y anular la verificación de contraseña:

    Payload: ' OR 1=1 --

Paso 2: Ejecución

La consulta interna del servidor se transformó de la siguiente manera:
SELECT * FROM users WHERE username = '' OR 1=1 --' AND password = '...'

Debido a que 1=1 es una condición siempre verdadera y -- comenta el resto de la instrucción, el servidor otorgó acceso al primer registro de la tabla (Admin).
Paso 3: Resultados

Tras el bypass exitoso, se obtuvo acceso a la zona restringida visualizando el siguiente mensaje:

    Congratulations! Your flag is: [TU_HASH_AQUÍ]

5. Recomendaciones de Mitigación

Para solventar esta vulnerabilidad, se recomienda aplicar las siguientes medidas:

    Uso de Sentencias Preparadas (Prepared Statements): Implementar consultas parametrizadas para asegurar que el input del usuario nunca sea interpretado como código ejecutable por el motor de la base de datos.

    Sanitización de Inputs: Validar y filtrar caracteres especiales (', --, #, ;) en el lado del servidor.

    Principio de Menor Privilegio: Asegurar que la cuenta de conexión a la base de datos tenga permisos limitados y no pueda acceder a tablas del sistema innecesarias.

Firma:
Analista de Seguridad Independiente
