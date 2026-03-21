---
title: 'Navegación Básica en Linux — ls, cd y pwd'
description: 'Aprende a moverte por el sistema de archivos de Linux con ls, cd y pwd. Opciones, flags, atajos y ejemplos prácticos paso a paso.'
pubDate: 'Mar 21 2026 14:00:00'
heroImage: '../../assets/linux-nav-hero.svg'
category: 'fundamentos'
---

Ya sabes que el sistema de archivos de Linux es un árbol jerárquico que parte de `/`. Ahora toca aprender a moverte por ese árbol. Tres comandos son los que usarás el 90% del tiempo: **`pwd`**, **`ls`** y **`cd`**.

Son simples. Son universales. Y dominarlos bien marca la diferencia entre alguien que teclea a ciegas y alguien que navega con precisión.

---

## pwd — ¿Dónde estoy?

`pwd` significa *Print Working Directory* — imprime el directorio de trabajo actual. Es lo primero que deberías ejecutar al abrir una terminal si no sabes dónde estás.

```bash
pwd
# /home/ubuntu

cd /etc
pwd
# /etc
```

No tiene opciones relevantes. Es un comando de una sola función: decirte dónde estás. Úsalo siempre que tengas dudas.

---

## ls — ¿Qué hay aquí?

`ls` (*list*) muestra el contenido del directorio actual — o del directorio que le indiques.

### Uso básico

```bash
ls                    # contenido del directorio actual
ls /etc               # contenido de /etc sin moverse ahí
ls /home/ubuntu/Docs  # contenido de una ruta absoluta
```

### Las opciones más importantes

```bash
ls -l        # formato largo — permisos, propietario, tamaño, fecha
ls -a        # muestra archivos ocultos (los que empiezan con .)
ls -la       # largo + ocultos (la combinación más usada)
ls -lh       # tamaños legibles (K, M, G en vez de bytes)
ls -lt       # ordenado por fecha de modificación (más reciente primero)
ls -lR       # recursivo — lista subdirectorios también
ls -1        # un archivo por línea (útil para scripts)
```

### Entendiendo la salida de `ls -la`

```bash
ls -la /home/ubuntu
total 64
drwxr-xr-x 12 ubuntu ubuntu 4096 Mar 21 10:00 .
drwxr-xr-x  3 root   root   4096 Mar 20 09:00 ..
-rw-------  1 ubuntu ubuntu  439 Mar 21 06:47 .bash_history
-rw-r--r--  1 ubuntu ubuntu  220 Mar 20 09:00 .bash_logout
-rw-r--r--  1 ubuntu ubuntu 3526 Mar 20 09:00 .bashrc
drwxr-xr-x  2 ubuntu ubuntu 4096 Mar 21 08:00 Desktop
drwxr-xr-x  3 ubuntu ubuntu 4096 Mar 21 10:00 Documents
```

Cada columna tiene un significado:

| Columna | Ejemplo | Significado |
|---------|---------|-------------|
| Permisos | `drwxr-xr-x` | tipo + permisos de propietario/grupo/otros |
| Hard links | `12` | número de enlaces al inode |
| Propietario | `ubuntu` | usuario dueño del archivo |
| Grupo | `ubuntu` | grupo dueño del archivo |
| Tamaño | `4096` | tamaño en bytes (o K/M/G con `-h`) |
| Fecha | `Mar 21 10:00` | última modificación |
| Nombre | `Desktop` | nombre del archivo o directorio |

La primera columna merece especial atención:
- `d` — directorio
- `-` — archivo regular
- `l` — enlace simbólico (symlink)
- `b` — dispositivo de bloque
- `c` — dispositivo de carácter

> 💡 `.` es el directorio actual y `..` es el directorio padre. Siempre aparecen con `ls -la`.

---

## cd — Moverse por el árbol

`cd` (*change directory*) cambia tu directorio de trabajo. Es el comando de navegación fundamental.

### Uso básico

```bash
cd /etc               # ir a /etc (ruta absoluta)
cd Documents          # entrar en Documents desde el directorio actual (relativa)
cd ..                 # subir un nivel (al directorio padre)
cd ../..              # subir dos niveles
cd ~                  # ir al home del usuario actual
cd -                  # volver al directorio anterior (muy útil)
cd /                  # ir a la raíz del sistema
```

### El poder de `cd -`

```bash
pwd
# /home/ubuntu/Documents/proyectos/web

cd /etc/nginx

pwd
# /etc/nginx

cd -
# /home/ubuntu/Documents/proyectos/web  ← vuelve al anterior con un solo comando
```

`cd -` es el equivalente al botón "atrás" del navegador. Imprescindible cuando saltas entre directorios lejanos.

### Rutas con espacios

Los espacios en nombres de archivos dan problemas en la terminal. Tres formas de manejarlos:

```bash
cd "Mi Carpeta"          # comillas dobles
cd 'Mi Carpeta'          # comillas simples
cd Mi\ Carpeta           # escapar el espacio con \
```

> 💡 En Linux, la convención es usar guiones (`mi-carpeta`) o guiones bajos (`mi_carpeta`) en lugar de espacios. Los espacios en nombres de archivos son una fuente constante de errores en scripts.

---

## Combinando los tres comandos

La navegación fluida es cuestión de combinar `pwd`, `ls` y `cd` con confianza:

```bash
# Secuencia típica de exploración
pwd                     # ¿dónde estoy?
# /home/ubuntu

ls                      # ¿qué hay aquí?
# Desktop  Documents  Downloads  projects

cd projects             # entro en projects
ls -la                  # ¿qué hay dentro, incluyendo ocultos?
# total 32
# drwxr-xr-x 5 ubuntu ubuntu 4096 Mar 21 12:00 .
# drwxr-xr-x 24 ubuntu ubuntu 4096 Mar 21 10:00 ..
# drwxr-xr-x 3 ubuntu ubuntu 4096 Mar 19 15:00 web-app
# drwxr-xr-x 2 ubuntu ubuntu 4096 Mar 20 09:00 scripts
# -rw-r--r-- 1 ubuntu ubuntu  128 Mar 21 08:00 .env

cd web-app
pwd
# /home/ubuntu/projects/web-app
```

---

## find — Localizar archivos sin saber dónde están

Cuando no sabes dónde está un archivo, `find` lo busca por ti:

```bash
find ~ -name "mission_brief.txt"          # buscar por nombre en el home
find /etc -name "*.conf"                  # todos los .conf en /etc
find / -name "passwd" 2>/dev/null         # buscar en todo el sistema, sin errores
find ~ -type f -name "*.sh"               # solo archivos (no directorios) .sh
find /tmp -type f -newer /etc/passwd      # archivos más recientes que /etc/passwd
find ~ -size +10M                         # archivos de más de 10MB
find ~ -mtime -1                          # modificados en las últimas 24 horas
```

El `2>/dev/null` al final redirige los mensajes de error (permiso denegado) a la papelera. Muy útil cuando buscas en directorios del sistema a los que no tienes acceso.

> ⚠️ `find /` busca en todo el sistema y puede tardar varios minutos. Especifica siempre el directorio de inicio más concreto posible.

---

## Atajos de teclado para navegar más rápido

La terminal tiene atajos que aceleran la navegación enormemente:

| Atajo | Función |
|-------|---------|
| `Tab` | Autocompletado — completa el nombre del archivo o directorio |
| `Tab Tab` | Muestra todas las opciones si hay más de una |
| `↑` / `↓` | Navegar por el historial de comandos |
| `Ctrl+R` | Buscar en el historial (escribe parte del comando) |
| `Ctrl+L` | Limpiar la pantalla (equivale a `clear`) |
| `Ctrl+A` | Ir al inicio de la línea |
| `Ctrl+E` | Ir al final de la línea |
| `Ctrl+W` | Borrar la palabra anterior |

El **autocompletado con Tab** es uno de los hábitos más importantes que debes desarrollar. Escribe las primeras letras del nombre y pulsa Tab — la shell completa el resto. Además de ahorrar tiempo, elimina errores de escritura.

```bash
cd Doc[Tab]           → cd Documents/
ls /etc/ng[Tab]       → ls /etc/nginx/
cat /var/log/au[Tab]  → cat /var/log/auth.log
```

---

## Wildcards — Comodines en nombres de archivos

La shell expande comodines antes de pasar los argumentos al comando:

```bash
ls *.txt              # todos los archivos que terminan en .txt
ls doc*               # todos los que empiezan por "doc"
ls *.{txt,md}         # .txt y .md
ls foto?.jpg          # foto + un carácter cualquiera + .jpg
ls [abc]*.sh          # empiezan por a, b o c y terminan en .sh
```

```bash
# Ejemplo práctico
ls /var/log/*.log     # todos los archivos .log en /var/log
# auth.log  kern.log  syslog  dpkg.log
```

---

## Relevancia en ciberseguridad

| Comando | Uso en seguridad |
|---------|-----------------|
| `ls -la /tmp` | Detectar ejecutables o scripts sospechosos en /tmp |
| `ls -lt /etc` | Ver qué archivos de configuración cambiaron más recientemente |
| `find / -perm -4000` | Encontrar binarios SUID (escalada de privilegios) |
| `find / -writable -type f 2>/dev/null` | Archivos escribibles por el usuario actual |
| `find /home -name ".ssh" -type d` | Localizar directorios SSH de todos los usuarios |
| `ls -la /root` | Ver archivos en el home de root (requiere privilegios) |

```bash
# Buscar binarios con bit SUID — clave en privilege escalation
find / -perm -u=s -type f 2>/dev/null
# /usr/bin/sudo
# /usr/bin/passwd
# /usr/bin/mount
# ...cualquier binario inusual aquí es sospechoso
```
