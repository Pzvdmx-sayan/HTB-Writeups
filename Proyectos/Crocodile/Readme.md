Informe de Auditoría de Seguridad: Proyecto Crocodile

ID de la Máquina: HTB-Crocodile | Severidad: <span style="color:red">CRÍTICA</span>

Preparado por: Gemini (Security Analyst)

Fecha: 17 de abril de 2026
1. Resumen Ejecutivo

Durante la evaluación de seguridad del activo 10.129.84.47, se identificó una cadena de vulnerabilidades crítica que permitió el acceso no autorizado al panel de administración. La falta de controles en el servicio de transferencia de archivos (FTP) facilitó la obtención de credenciales, las cuales fueron utilizadas para comprometer la aplicación web.
2. Enumeración y Reconocimiento

Se realizó un escaneo de puertos con Nmap para identificar la superficie de ataque disponible.

    Comando: nmap -sV -sC -p21,80 10.129.84.47

    Resultados:

        Puerto 21 (FTP): Versión vsftpd 3.0.3. Se detectó Anonymous FTP login allowed (Código 230).

        Puerto 80 (HTTP): Servidor Apache 2.4.41.

3. Análisis de Vulnerabilidades
V-01: Acceso Anónimo a Servicio Crítico (FTP)

El servicio FTP estaba configurado para permitir el acceso sin autenticación. Al ingresar como usuario anonymous, se localizaron dos archivos con información sensible:

    allowed.userlist: Contenía una lista de nombres de usuarios del sistema.

    allowed.userlist.passwd: Contenía las contraseñas asociadas en texto plano.

V-02: Exposición de Interfaz Administrativa Oculta (Fuzzing)

La aplicación web no mostraba enlaces a paneles de gestión. Mediante un ataque de fuerza bruta de directorios con Gobuster, se descubrió una interfaz de autenticación oculta.

    Comando: gobuster dir -u http://10.129.84.47 -w /usr/share/wordlists/dirb/common.txt -x php

    Hallazgo: login.php (Status 200).

4. Explotación (Prueba de Concepto)

    Se accedió a la URL http://10.129.84.47/login.php.

    Se utilizaron las credenciales extraídas del FTP:

        Usuario: admin

        Contraseña: rKXM59ESxesUFHAd

    El acceso fue exitoso, redirigiendo al analista a dashboard.php, donde se obtuvo la bandera del sistema.

Prueba de éxito (Flag):
5. Recomendaciones de Remediación
Hallazgo	Recomendación Técnica	Prioridad
FTP Anónimo	Deshabilitar el acceso anónimo en el archivo de configuración vsftpd.conf (anonymous_enable=NO).	Crítica
Credenciales en Texto Plano	Implementar políticas de almacenamiento seguro (hashing) y nunca guardar claves en servidores de archivos públicos.	Alta
Seguridad Web	Implementar protección contra fuerza bruta (Rate Limiting) y ocultar versiones de software en los banners de Apache.	Media
