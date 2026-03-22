---
title: 'Gestión de Archivos en Linux — cp, mv, rm y touch'
description: 'Copiar, mover, renombrar, borrar y crear archivos desde la terminal. Opciones críticas, errores comunes y aplicaciones en ciberseguridad.'
pubDate: 'Mar 22 2026 09:00:00'
heroImage: '../../assets/linux-files-hero.svg'
category: 'fundamentos'
---

Navegar por el sistema de archivos es solo el primer paso. El trabajo real empieza cuando necesitas **crear, copiar, mover y eliminar** archivos. Cuatro comandos cubren el 95% de esas operaciones: `cp`, `mv`, `rm` y `touch`.

Son simples en apariencia. Pero cada uno tiene opciones que marcan la diferencia entre hacerlo bien y cometer un error irreversible — porque en Linux, **no hay papelera**.

---

## touch — Crear archivos y actualizar fechas

`touch` tiene dos funciones: crear archivos vacíos si no existen, y actualizar la fecha de acceso y modificación si ya existen.

```bash
touch archivo.txt           # crea archivo.txt vacío
touch a.txt b.txt c.txt     # crea varios archivos a la vez
touch -t 202603210900 log.txt  # establece una fecha específica (YYYYMMDDhhmm)
touch -a archivo.txt        # actualiza solo la fecha de acceso
touch -m archivo.txt        # actualiza solo la fecha de modificación
```

### ¿Para qué se usa en la práctica?

```bash
# Crear archivos de log vacíos para un servicio
touch /var/log/mi-app.log

# Crear múltiples archivos de una vez en un proyecto
touch index.html style.css main.js README.md

# En scripting: verificar que un archivo existe o crearlo
touch /tmp/lock.pid
```

> ⚠️ En ciberseguridad, `touch -t` se usa para **timestomping** — modificar las fechas de un archivo malicioso para que parezca más antiguo y evitar la detección forense. Un archivo con fecha de 2019 en un directorio donde todo lo demás es de 2026 es una señal de alerta.

---

## cp — Copiar archivos y directorios

`cp` copia archivos y directorios. La sintaxis básica es `cp origen destino`.

### Copiar archivos

```bash
cp archivo.txt copia.txt              # copia en el mismo directorio con otro nombre
cp archivo.txt /tmp/                  # copia a /tmp/ manteniendo el nombre
cp archivo.txt /tmp/nuevo_nombre.txt  # copia a /tmp/ con nuevo nombre
cp a.txt b.txt c.txt /tmp/            # copiar varios archivos a un directorio
```

### Copiar directorios — la opción `-r`

Para copiar directorios, **siempre** necesitas `-r` (recursivo):

```bash
cp -r carpeta/ /tmp/carpeta_copia/    # copia el directorio completo
cp -r /etc/nginx /tmp/nginx_backup/  # backup de configuración de nginx
```

Sin `-r`, `cp` falla silenciosamente al intentar copiar un directorio.

### Opciones esenciales

```bash
cp -i archivo.txt destino/    # interactivo: pregunta antes de sobreescribir
cp -n archivo.txt destino/    # no sobreescribe si ya existe (no clobber)
cp -u archivo.txt destino/    # copia solo si el origen es más nuevo
cp -v archivo.txt destino/    # verbose: muestra lo que está copiando
cp -p archivo.txt destino/    # preserva permisos, fechas y propietario
cp -a carpeta/ destino/       # archive: -r + -p + preserva todo (ideal para backups)
```

### La opción `-a` para backups perfectos

```bash
# -a es equivalente a -dR --preserve=all
# Preserva: permisos, fechas, propietario, grupo, symlinks, atributos especiales
cp -a /etc/ /backup/etc_$(date +%Y%m%d)/
```

> 💡 Para backups de configuración, usa siempre `cp -a`. Un backup que no preserva los permisos puede romper servicios al restaurarlo.

---

## mv — Mover y renombrar archivos

`mv` hace dos cosas según el destino: **mover** (si el destino es un directorio diferente) o **renombrar** (si el destino es un nombre en el mismo directorio).

```bash
# Renombrar
mv antiguo.txt nuevo.txt              # renombra en el mismo directorio

# Mover
mv archivo.txt /tmp/                  # mueve a /tmp/ manteniendo el nombre
mv archivo.txt /tmp/nuevo_nombre.txt  # mueve y renombra al mismo tiempo
mv carpeta/ /opt/carpeta_nueva/       # mueve un directorio completo

# Mover varios archivos a un destino
mv a.txt b.txt c.txt /tmp/
```

### Diferencia crítica entre mv y cp

`cp` deja el original intacto. `mv` elimina el original — es un corte y pega, no una copia. Si el destino está en la misma partición de disco, `mv` es instantáneo (solo cambia el puntero del inode). Si está en otra partición, copia y luego borra.

### Opciones importantes

```bash
mv -i origen destino    # interactivo: pregunta antes de sobreescribir
mv -n origen destino    # no sobreescribe si ya existe
mv -v origen destino    # verbose: muestra cada operación
mv -b origen destino    # hace backup del destino si ya existe
```

### Renombrar múltiples archivos

`mv` solo mueve/renombra uno a uno. Para renombrar en masa, se usa con bucles o la herramienta `rename`:

```bash
# Renombrar todos los .txt a .md con un bucle bash
for f in *.txt; do mv "$f" "${f%.txt}.md"; done

# Con rename (si está instalado)
rename 's/\.txt$/.md/' *.txt
```

---

## rm — Eliminar archivos y directorios

`rm` es el comando más peligroso de la terminal. **No hay papelera. No hay deshacer. Los archivos se eliminan permanentemente** (hasta que el espacio en disco sea reutilizado).

```bash
rm archivo.txt                # elimina un archivo
rm a.txt b.txt c.txt          # elimina varios archivos
rm *.log                      # elimina todos los .log del directorio actual
```

### Eliminar directorios

```bash
rm -r carpeta/                # elimina el directorio y todo su contenido
rm -rf carpeta/               # forzado: sin preguntar, sin errores si no existe
```

### Opciones

```bash
rm -i archivo.txt     # interactivo: pregunta antes de cada eliminación
rm -f archivo.txt     # forzado: no muestra errores si no existe el archivo
rm -v archivo.txt     # verbose: confirma cada eliminación
rm -r carpeta/        # recursivo: necesario para directorios
```

### El comando más peligroso de Linux

```bash
# NUNCA ejecutes esto — borra todo el sistema de archivos
rm -rf /

# En versiones modernas de rm, hay que añadir --no-preserve-root para que funcione
# pero sus variantes siguen siendo peligrosas:
rm -rf /*          # borra todo dentro de /
rm -rf ~/          # borra tu home completo
rm -rf .           # borra el directorio actual y todo su contenido
```

> ⚠️ Antes de cualquier `rm` con wildcards, **primero ejecuta `ls`** con los mismos argumentos para verificar qué vas a borrar: `ls *.log` → si la lista es correcta → `rm *.log`.

### Alternativa segura: mover a /tmp antes de borrar

```bash
# En vez de rm directo, mueve primero a /tmp
mv archivo_sospechoso.txt /tmp/
# Si confirmas que ya no lo necesitas:
rm /tmp/archivo_sospechoso.txt
```

---

## Operaciones combinadas y flujos de trabajo reales

### Backup antes de modificar

```bash
# Patrón fundamental: siempre backup antes de editar un archivo crítico
cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak
nano /etc/nginx/nginx.conf
# Si algo va mal:
mv /etc/nginx/nginx.conf.bak /etc/nginx/nginx.conf
```

### Limpiar archivos temporales

```bash
# Ver qué hay antes de borrar
ls -lh /tmp/*.tmp
# Total: 847M de archivos temporales

# Borrar con confirmación
rm -i /tmp/*.tmp

# O directamente si estás seguro
rm -f /tmp/*.tmp
```

### Organizar archivos por extensión

```bash
mkdir -p docs images scripts

mv *.pdf *.docx *.txt docs/
mv *.png *.jpg *.svg images/
mv *.sh *.py scripts/
```

### Crear estructura de proyecto

```bash
mkdir -p proyecto/{src,docs,tests,config}
touch proyecto/src/main.py
touch proyecto/README.md
touch proyecto/config/.env
touch proyecto/docs/index.md
```

---

## mkdir y rmdir — Crear y eliminar directorios

Aunque no son los protagonistas de este post, van de la mano con `cp`, `mv` y `rm`:

```bash
mkdir directorio              # crear un directorio
mkdir -p a/b/c/d              # crear árbol completo de directorios
mkdir -p proyecto/{src,tests} # crear varios subdirectorios a la vez

rmdir directorio              # eliminar directorio solo si está VACÍO
rm -r directorio/             # eliminar directorio y todo su contenido
```

`-p` en `mkdir` es imprescindible: crea todos los directorios intermedios necesarios sin error si ya existen.

---

## Relevancia en ciberseguridad

| Comando | Técnica / Uso |
|---------|--------------|
| `touch -t` | **Timestomping** — modificar fechas para evadir análisis forense |
| `cp -a` | Backup de configuración preservando permisos antes de modificar |
| `rm -rf` | Limpieza de evidencias por parte del atacante |
| `mv` a `/tmp` | Atacantes mueven payloads a /tmp donde todos tienen escritura |
| `cp /etc/passwd /tmp/` | Exfiltración de archivos sensibles a un directorio accesible |
| `rm ~/.bash_history` | Borrar historial de comandos para cubrir huellas |
| `touch ~/.bash_history` | Recrear el historial vacío tras borrarlo |

```bash
# Técnica común post-explotación: exfiltrar /etc/shadow
cp /etc/shadow /tmp/.hidden_file    # notar el punto inicial — archivo oculto
# El atacante luego descarga /tmp/.hidden_file

# Cubrir huellas básicas
rm ~/.bash_history
touch ~/.bash_history
history -c
```

> ⚠️ En análisis forense, los archivos "borrados" con `rm` pueden recuperarse mientras el espacio no haya sido sobreescrito. Herramientas como `testdisk`, `extundelete` o `Autopsy` pueden recuperar inodes con contenido en disco.
