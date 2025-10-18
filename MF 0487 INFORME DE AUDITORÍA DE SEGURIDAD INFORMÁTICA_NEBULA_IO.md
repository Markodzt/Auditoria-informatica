# MF 0487 INFORME DE AUDITORÍA DE SEGURIDAD INFORMÁTICA - NEBULA.IO

**Autor:** Marco Di Zitti  
**Fecha de Inicio:** 01 de Octubre de 2025  
**Tiempo de Ejecución:** 11 horas approx.  
**Empresa:** Nebula.io  

---

## Introducción

La presente auditoría de seguridad informática en Nebula.io se realizó con el objetivo de identificar, evaluar y reducir los riesgos detectados tras el incidente crítico sufrido por el servidor principal, que fue víctima de un ataque de tipo ransomware.

El proceso de auditoría se llevó a cabo a partir de una copia de seguridad del sistema, situada en un entorno aislado (sandbox) y permitiendo un análisis seguro para identificar los posibles vectores de ataque responsables de la brecha de seguridad inicial.

Durante la evaluación, se identificaron cinco hallazgos principales. Se destaca especialmente la vulnerabilidad crítica SambaCry (CVE-2017-7494), catalogada con un CVSS de 9.8, la cual permite la ejecución remota de código con privilegios de usuario root.

El análisis confirmó el compromiso total del entorno evaluado, así como rastros del ransomware RYUK V0.02a2, evidenciando la necesidad prioritaria de aplicar las recomendaciones de seguridad propuestas en este informe.

**Sistema auditado:** 10.10.xx.xx / Servidor NEBULA (hostname: Nebula-server)  
**Sistema operativo:** Ubuntu 14.04.6 - Kernel 4.4.0-142-generic  

**Puertos y servicios evaluados:**
- 53 (Domain, BIND 9.9.5-3ubuntu0.19)  
- 80 (HTTP, Bluffer)  
- 1986 (SSH, OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.13)  
- 44544 (Samba smdb 3.X - 4.X)

**Herramientas utilizadas:** Nmap, Wireshark, Nessus, John the Ripper  
**Limitaciones:** Auditoría realizada en entorno sandbox aislado a partir de una copia de respaldo del sistema comprometido  

---

## Alcance

- Puertos y servicios evaluados

---

## Resumen Ejecutivo

- **Severidad:** Media  
- **Descripción:** Permite a un atacante eludir controles de integridad durante el inicio de sesión, degradando la seguridad de conexión mediante el ataque conocido como Terrapin.  
- **Impacto:** Permite a un atacante interceptar y manipular una sesión SSH, comprometiendo credenciales y datos sensibles.  
- **Recomendación:** Actualizar OpenSSH a una versión superior que corrija la vulnerabilidad CVE-2023-48795.

---

## Metodología

1. Descubrimiento de hosts y reconocimiento inicial con Nmap.  
2. Escaneo de puertos y detección de servicios en ejecución.  
3. Enumeración de servicios y versiones.  
4. Análisis de vulnerabilidades mediante Nessus y Nmap.  
5. Recolección de evidencias.  
6. Recomendaciones.

---

## Hallazgos

### 1) Servicio SSH vulnerable a ataques man-in-the-middle

- **Severidad:** Media  
- **Descripción:** Permite eludir controles de integridad durante el inicio de sesión.  

### 2) Transferencia de zona DNS no restringida

- **Severidad:** Crítica  
- **Descripción:** Posibilidad de realizar una transferencia de zona completa (AXFR) en el servidor DNS, exponiendo todos los registros internos.  
- **Impacto:** Permite a un atacante mapear la infraestructura interna y orientar ataques dirigidos.  
- **Recomendación:** Restringir el acceso a la transferencia de zona DNS a solo IPs autorizadas.

### 3) Exposición de directorios de administración y servicios

- **Severidad:** Media  
- **Descripción:** Directorios y servicios administrativos accesibles sin autenticación estricta.  
- **Impacto:** Podría facilitar ataques de fuerza bruta o enumeración de recursos.  
- **Recomendación:** Restringir acceso por IP y habilitar autenticación fuerte. Ocultar interfaces no necesarias en producción.

### 4) Servicio SMB vulnerable

- **Severidad:** Crítica  
- **Descripción:** Versiones de Samba vulnerables a CVE-2017-7494 permiten ejecución remota de código.  
- **Impacto:** Un atacante podría obtener acceso remoto al sistema.  
- **Recomendación:** Actualizar Samba a la versión más reciente y restringir acceso a la red interna.

### 5) Credenciales y usuarios expuestos

- **Severidad:** Alta  
- **Descripción:** Identificación de múltiples usuarios con hashes de contraseñas accesibles.  
- **Impacto:** Incrementa la posibilidad de escalamiento de privilegios.  
- **Recomendación:** Cambiar todas las contraseñas, implementar políticas de gestión de accesos y auditar cuentas activas.

---

## Prioridades y Acciones

| Prioridad | Acción                                                                |
|-----------|----------------------------------------------------------------------|
| Alta      | Actualizar OpenSSH y Samba a versiones corregidas                    |
| Alta      | Restringir transferencia de zona DNS solo a administradores          |
| Alta      | Cambiar inmediatamente todas las contraseñas y revisar cuentas activas |
| Media     | Implementar autenticación fuerte en paneles administrativos          |
| Media     | Revisar y mejorar configuración de firewall y segmentación de red    |
| Baja      | Realizar formación periódica sobre ciberseguridad para los usuarios  |

---

## Recomendaciones

Aplicar las acciones priorizadas y mantener auditorías periódicas para asegurar la seguridad continua.

---

## Conclusión

El laboratorio ha permitido identificar debilidades relevantes en la seguridad de Nebula.io y ha ofrecido una hoja de ruta para mitigar los principales riesgos detectados. Demuestra la importancia de auditorías periódicas y la aplicación continua de mejores prácticas.

---

## Anexos

### Comandos utilizados

cat, cd, ls, file, chmod
nmap -sn 10.10.215.196
nmap -sS -p- 10.10.215.196 -vv
nmap -sS -sV -p53,80,1986 10.10.215.196 -vv
ssh -p 1986 bluffer@10.10.215.196
compgen -u
nslookup -type=TXT nebula.io 10.10.155.249
hostname
uname -a
getent passwd