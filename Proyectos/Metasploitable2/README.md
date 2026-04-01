# 🛡️ Informe de Laboratorio: Pentesting vsftpd 2.3.4

**Fecha:** 31 de Marzo, 2026  
**Objetivo:** Metasploitable 2 (192.168.56.103)  
**Resultado:** COMPROMISO TOTAL (Acceso Root)

---

## 🏗️ Configuración de la Infraestructura
Para este laboratorio se utilizó un entorno aislado en **Oracle VM VirtualBox**:
* [cite_start]**Red:** Adaptador solo-anfitrión (Host-Only).
* [cite_start]**Máquina Atacante:** Kali Linux (IP: 192.168.56.102).
* [cite_start]**Máquina Víctima:** Metasploitable 2 (IP: 192.168.56.103).

## 🔍 Fase de Reconocimiento
Se realizó un escaneo de servicios con **Nmap v7.9x** utilizando el comando:
[cite_start]`nmap -sV 192.168.56.103`.

[cite_start]**Hallazgo Crítico:** Se detectó el puerto **21/TCP** abierto con el servicio **vsftpd 2.3.4**. [cite_start]Esta versión contiene una puerta trasera (backdoor) conocida que permite la ejecución remota de comandos.

## ⚡ Fase de Explotación
Utilicé **Metasploit Framework** para comprometer el sistema:
1. [cite_start]**Módulo:** `exploit/unix/ftp/vsftpd_234_backdoor`.
2. [cite_start]**Carga útil (Payload):** Se activó el "trigger" mediante una carita sonriente `:)` en el nombre de usuario.
3. [cite_start]**Resultado:** Se estableció una sesión de **Meterpreter** con éxito.

## 🚩 Post-Explotación y Control
* [cite_start]**Escalada de Privilegios:** Automática, obteniendo acceso directo como usuario **root**.
* [cite_start]**Prueba de Concepto:** Se realizó la exfiltración del archivo `/etc/shadow`, visualizando los hashes de las contraseñas de los usuarios del sistema.

---

## 🛠️ Herramientas Clave
* [cite_start]**Nmap:** Descubrimiento de red.
* [cite_start]**Metasploit:** Framework de explotación.
* [cite_start]**Meterpreter:** Control avanzado post-ataque.
