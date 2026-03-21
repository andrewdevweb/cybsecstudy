---
title: 'El Sistema de Archivos de Linux — Jerarquía, Rutas e Inodes'
description: 'La estructura jerárquica de directorios en Linux, rutas absolutas y relativas, el estándar FHS y una introducción al concepto de inode.'
pubDate: 'Mar 21 2026 13:00:00'
heroImage: '../../assets/linux-fs-hero.svg'
category: 'fundamentos'
---

En Windows estás acostumbrado a `C:\Users\usuario\Desktop`. En Linux todo funciona de forma diferente — y una vez que lo entiendes, resulta mucho más elegante. Todo el sistema de archivos parte de un único punto: `/`.

No hay `C:`, ni `D:`, ni letras de unidad. Hay **una sola raíz**, y todo cuelga de ella.

---

## La raíz de todo — `/`

El directorio raíz `/` es el punto de partida de todo el sistema de archivos. A diferencia de Windows, que tiene múltiples árboles (uno por cada letra de unidad), Linux tiene **un único árbol jerárquico**. Los discos adicionales, los USB y las particiones se "montan" como subdirectorios dentro de ese árbol.

```
/
├── bin/        ← ejecutables esenciales del sistema
├── boot/       ← archivos de arranque, kernel
├── dev/        ← dispositivos (todo es un archivo)
├── etc/        ← configuración del sistema
├── home/       ← directorios personales de usuarios
│   └── ubuntu/ ← el home del usuario "ubuntu"
├── lib/        ← bibliotecas del sistema
├── media/      ← puntos de montaje para USB, DVD...
├── mnt/        ← montajes temporales
├── opt/        ← software opcional de terceros
├── proc/       ← procesos del sistema (sistema de archivos virtual)
├── root/       ← home del usuario root
├── run/        ← datos de runtime del sistema
├── sbin/       ← ejecutables del sistema (para root)
├── srv/        ← datos de servicios (web, FTP...)
├── sys/        ← información del kernel (virtual)
├── tmp/        ← archivos temporales (se borra al reiniciar)
├── usr/        ← programas y datos de usuario
│   ├── bin/    ← la mayoría de ejecutables del usuario
│   └── share/  ← documentación y recursos compartidos
└── var/        ← datos variables (logs, bases de datos, correo)
    └── log/    ← logs del sistema
```

Este estándar se llama **FHS** (Filesystem Hierarchy Standard) y lo siguen la mayoría de distribuciones Linux.

---

## Los directorios más importantes

### `/etc` — El corazón de la configuración
Contiene los archivos de configuración de todo el sistema. Modificar archivos aquí cambia el comportamiento del sistema y los servicios.

```bash
/etc/passwd         # lista de usuarios del sistema
/etc/shadow         # contraseñas (hasheadas)
/etc/hosts          # resolución de nombres local
/etc/hostname       # nombre del ordenador
/etc/fstab          # sistemas de archivos a montar al arranque
/etc/ssh/sshd_config # configuración del servidor SSH
/etc/crontab        # tareas programadas del sistema
```

> ⚠️ En ciberseguridad, `/etc/passwd` y `/etc/shadow` son objetivos frecuentes — contienen información de usuarios y hashes de contraseñas.

### `/home` — Los directorios personales
Cada usuario tiene su propio directorio en `/home/nombre_usuario`. Aquí viven sus archivos, configuración personal (`.bashrc`, `.ssh/`) y datos.

```bash
/home/ubuntu/         # home del usuario ubuntu
/home/ubuntu/.ssh/    # claves SSH del usuario
/home/ubuntu/.bashrc  # configuración de bash
```

El usuario **root** es especial — su home es `/root`, no `/home/root`.

### `/var` — Datos variables
Contiene datos que cambian durante la operación del sistema. Los logs son especialmente importantes:

```bash
/var/log/syslog       # log general del sistema
/var/log/auth.log     # intentos de autenticación
/var/log/apache2/     # logs del servidor web Apache
/var/log/nginx/       # logs de Nginx
/var/www/html/        # raíz del servidor web por defecto
```

> 💡 En análisis forense y Blue Team, `/var/log/auth.log` es el primer lugar donde buscar intentos de acceso no autorizados.

### `/proc` — El sistema de archivos virtual
`/proc` no existe en el disco — es generado por el kernel en tiempo real. Contiene información sobre los procesos en ejecución y el estado del sistema.

```bash
cat /proc/cpuinfo     # información del procesador
cat /proc/meminfo     # uso de memoria
cat /proc/version     # versión del kernel
ls /proc/1234/        # información del proceso con PID 1234
cat /proc/1234/cmdline # comando que lanzó ese proceso
```

### `/dev` — Todo es un archivo
Linux trata los dispositivos como archivos. Los discos, terminales, generadores de números aleatorios — todo tiene una entrada en `/dev`.

```bash
/dev/sda              # primer disco SATA/SCSI
/dev/sda1             # primera partición del disco
/dev/null             # agujero negro — borra todo lo que le mandes
/dev/zero             # genera ceros infinitos
/dev/random           # generador de números aleatorios
/dev/tty              # terminal actual
```

```bash
# Ejemplo clásico: redirigir errores a /dev/null
comando 2>/dev/null   # los errores desaparecen

# Crear un archivo de 1MB lleno de ceros
dd if=/dev/zero of=archivo.bin bs=1M count=1
```

### `/tmp` — Archivos temporales
Se borra completamente al reiniciar el sistema. Cualquier usuario puede escribir aquí.

> ⚠️ `/tmp` es un vector de ataque frecuente — atacantes depositan scripts y ejecutables maliciosos aquí porque todos tienen permiso de escritura.

---

## Rutas absolutas vs relativas

Una **ruta** es la dirección de un archivo o directorio en el sistema. Hay dos tipos:

### Ruta absoluta
Empieza siempre desde la raíz `/`. Es la dirección completa, inequívoca, independientemente de dónde estés.

```bash
/home/ubuntu/Documents/proyecto.txt
/etc/nginx/nginx.conf
/var/log/auth.log
```

### Ruta relativa
Parte desde el **directorio actual** (donde estás en este momento). No empieza con `/`.

```bash
# Si estás en /home/ubuntu:
Documents/proyecto.txt    # equivale a /home/ubuntu/Documents/proyecto.txt
../otro_usuario/          # sube un nivel y entra en otro_usuario
./script.sh               # ./ significa "aquí mismo"
```

### Atajos de navegación

| Símbolo | Significa |
|---------|-----------|
| `.` | El directorio actual |
| `..` | El directorio padre (un nivel arriba) |
| `~` | El home del usuario actual (`/home/usuario`) |
| `-` | El directorio anterior (útil con `cd -`) |

```bash
cd ~          # ir al home
cd ..         # subir un nivel
cd ../..      # subir dos niveles
cd -          # volver al directorio anterior
cd /          # ir a la raíz

# Rutas absolutas vs relativas — mismo resultado
cat /home/ubuntu/notas.txt
cd /home/ubuntu && cat notas.txt
cat ~/notas.txt
```

---

## El concepto de inode — Introducción teórica

Cada archivo en Linux tiene dos partes:
1. **El nombre** (que ves con `ls`)
2. **El inode** (donde realmente vive la información)

Un **inode** es una estructura de datos que almacena los **metadatos** de un archivo:

- Tipo de archivo (regular, directorio, enlace...)
- Permisos (`rwxr-xr-x`)
- Propietario y grupo
- Tamaño en bytes
- Fechas (creación, modificación, acceso)
- Número de enlaces hard
- Punteros a los bloques de datos en disco

Lo que **no** almacena el inode: el nombre del archivo. El nombre es solo una etiqueta en el directorio que apunta al número de inode.

```bash
ls -i archivo.txt    # muestra el número de inode
# 262146 archivo.txt

stat archivo.txt     # muestra todos los metadatos del inode
```

### ¿Por qué importa el inode?

1. **Hard links** — dos nombres distintos apuntando al mismo inode (al mismo contenido). Borrar uno no borra el archivo si hay otro enlace.

2. **Soft links (symlinks)** — un archivo que apunta a la ruta de otro. Si borras el original, el symlink queda roto.

```bash
# Hard link — ambos apuntan al mismo inode
ln archivo.txt otro_nombre.txt
ls -i
# 262146 archivo.txt
# 262146 otro_nombre.txt  ← mismo inode

# Soft link — apunta a la ruta
ln -s /etc/nginx/nginx.conf nginx.conf
ls -la nginx.conf
# lrwxrwxrwx ... nginx.conf -> /etc/nginx/nginx.conf
```

3. **Forense** — el análisis de inodes puede revelar archivos borrados (el contenido persiste hasta que el espacio se reutiliza), fechas de acceso y manipulación.

---

## Permisos a nivel de sistema de archivos

Cada archivo y directorio tiene permisos asociados al inode. Los veremos en detalle en el post 7, pero es útil entenderlos en contexto:

```bash
ls -la /etc/shadow
# ---------- 1 root shadow 1234 Mar 21 10:00 /etc/shadow
# ↑ sin permisos para nadie excepto root via sudo

ls -la /tmp
# drwxrwxrwt 12 root root 4096 Mar 21 10:00 /tmp
# ↑ todos pueden leer, escribir y ejecutar (t = sticky bit)
```

---

## Montaje de sistemas de archivos

En Linux, los discos y particiones no aparecen automáticamente como `D:` — se **montan** en un punto del árbol de directorios.

```bash
mount                           # ver todos los sistemas montados
df -h                           # uso de disco por sistema montado
mount /dev/sdb1 /mnt/disco      # montar un disco en /mnt/disco
umount /mnt/disco               # desmontar

cat /etc/fstab                  # sistemas montados automáticamente al arranque
```

---

## Relevancia en ciberseguridad

| Directorio | Relevancia |
|-----------|-----------|
| `/etc/passwd`, `/etc/shadow` | Enumeración de usuarios, cracking de hashes |
| `/etc/crontab`, `/var/spool/cron` | Persistencia — tareas programadas maliciosas |
| `/tmp`, `/dev/shm` | Depósito de payloads — escritura libre para todos |
| `/home/usuario/.ssh/` | Claves SSH privadas — acceso persistente |
| `/var/log/auth.log` | Evidencia de intrusión — Blue Team |
| `/proc/` | Información de procesos — enumeración de sistema |
| `/root/` | Home del superusuario — archivos críticos |
| `/usr/bin/`, `/sbin/` | Objetivo de sustitución maliciosa de binarios |
