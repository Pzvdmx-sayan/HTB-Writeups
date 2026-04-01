🐱 Hack The Box: Meow (Starting Point - Tier 0)

Este repositorio contiene la documentación técnica y el procedimiento de intrusión seguido para comprometer la máquina Meow. El objetivo es demostrar habilidades en redes, enumeración de servicios y explotación de malas configuraciones.
🏗️ 1. Infraestructura y Conectividad

Para interactuar con el objetivo, se estableció un túnel privado mediante OpenVPN. Se gestionó el proceso en segundo plano para optimizar la eficiencia en la terminal.

Comando de conexión:
Bash

sudo openvpn mi_configuracion.ovpn > vpn.log 2>&1 &

    Herramienta: OpenVPN (Crea la interfaz virtual tun0).

    Diagnóstico: Se verificó la asignación de IP con ip addr show tun0.

🔍 2. Reconocimiento y Enumeración
2.1 Verificación de Host (Capa 3 - Red)

Se utilizó el protocolo ICMP para confirmar que el objetivo está activo y alcanzable.

    Comando: ping -c 4 10.129.1.17

    Concepto: Envío de Echo Requests para medir latencia y conectividad.

2.2 Escaneo de Servicios (Capa 4 - Transporte)

Se empleó Nmap (Network Mapper) para identificar los puertos abiertos y las versiones de los servicios que corren en ellos.

    Comando: nmap -sV 10.129.1.17

    Resultado Crítico: * Puerto 23/tcp: Abierto.

        Servicio: Telnet (Ubuntu Linux).

⚡ 3. Fase de Explotación

Vulnerabilidad: Falta de controles de acceso en el servicio administrativo Telnet.
Protocolo: Telnet (Puerto 23), un protocolo inseguro que transmite datos en texto plano.

Proceso de Acceso:

    Se inició la conexión: telnet 10.129.1.17.

    Se introdujo el usuario de máxima autoridad: root.

    El sistema permitió el acceso sin solicitar contraseña (Blank Password).

Bash

Meow login: root
Welcome to Ubuntu 20.04.2 LTS
root@Meow:~# 

🚩 4. Post-Explotación (Prueba de Compromiso)

Una vez obtenido el acceso como root, se procedió a la extracción de la bandera del sistema para validar el control total sobre la máquina.
Bash

root@Meow:~# whoami
root
root@Meow:~# cat flag.txt
b40abdfe23665f766f9c61ecba8a4c19

🛠️ 5. Resumen de Herramientas y Conocimientos
Herramienta	Aplicación	Concepto Técnico
Terminal	Interfaz de comandos	Gestión de sistemas operativos.
OpenVPN	VPN	Acceso a redes privadas virtuales.
Ping	ICMP	Diagnóstico de conectividad.
Nmap	Escaneo de puertos	Enumeración y detección de servicios.
Telnet	Explotación	Protocolo de acceso remoto (inseguro).

🛡️ 6. Recomendaciones de Seguridad (Mitigación)

Como analista de seguridad, se proponen las siguientes mejoras:

    Sustitución de Telnet por SSH: SSH cifra las credenciales y la sesión, evitando el robo de datos.

    Hardening de Cuentas: Configurar contraseñas robustas para el usuario root.

    Control de Acceso: Limitar el acceso a servicios administrativos solo a IPs autorizadas mediante un Firewall.
