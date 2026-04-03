INFORME DE AUDITORÍA DE SEGURIDAD: Máquina "Dancing"
ID de Objetivo: 10.129.59.95
S.O Detectado:Windows Server (TTL:127)
Severidad: Crítica (Exposición de datos sensibles)

1.Fase de Reconocimiento y Escaneo
Se realizó un escaneo exhaustivo de puertos mediante nmap, detectando una superficie de ataques centrada en servicios de infraestructura de Windows

 .Puertos Abiertos Clave:
    .445/tcp (microsoft-ds):Servicio SMB activo
    .139/tcp (netbios-ssn):Servicio NetBIOS
    .5985/TCP (http):WinRM (Administración remota) habilitado

2.Enumeración de Servicios (Explotación de SMB)
Al investigar el puerto 445, se descubrío que el servidor permitía sesiones nulas (Null Sessions), lo que permite a un atacante listar recursos sin autenticación
 .Comando utilizado: smbclient -L 10.129.59.95 -N 
 .Recursos compartidos detectados: 
  .ADMIN$, C$, IPC$ (Por defecto/Administrativos).
  .Workshares (Recursos personalizados con permissos laxos).

  3.Explotación y Acesso a Datos
  Se logró acceso no autorizado al recurso WorkShares.Tras navegar por la estructura de directorios, se identificaron dos perifles de ususario: AMY.J Y JAMES.P
  .Evidencia 1 (Amy.J/worknotes.txt):Se recuperaron notas técnicas que confirman la configuración de WinRM en el sistema, lo que sugiere un posible vector de movimiento lateral o escalada.
  .Evidencia 2 (James.P/flag.txt):Se localizó y extrajo la bandera de seguridad (Flag) confirmando el compromiso total de la confidencialidad de los archivos del usuario.

  4.Recomendaciones de Mitigación (Para el SysAdmin)
  Para corregir estas vulnerabilidades detectadas en el curso , se recomienda:
  1.Deshabilitar el acceso anónimo:Configurar el registro de Windows para impedir que usuarios no autenticados listen recursos compartidos(RestricNullSessAccess)
  2. Principio de Menor Privilegio:Eliminar el recurso Workshares o restringir el acceso mediante listas de control (ACLs) solo a usuarios especificos con contraseña.
  3. Cifrado de SMB:Asegurar que se utilice SMB 3.1.1 con cifrado activo para proteger los datos en tránsito.
