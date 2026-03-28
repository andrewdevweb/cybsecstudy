---
title: 'Ver Contenido de Archivos en Linux — cat, less, head, tail y grep'
description: 'Leer, paginar, inspeccionar y filtrar el contenido de archivos desde la terminal. La herramienta fundamental de análisis en Linux.'
pubDate: 'Mar 28 2026 09:00:00'
heroImage: '../../assets/linux-view-hero.svg'
category: 'fundamentos'
---

Saber navegar por el sistema de archivos y gestionarlos es el primer paso. El siguiente es **leer lo que hay dentro**. En Linux, la mayoría de la configuración del sistema, los logs, los scripts y los datos viven en archivos de texto plano — y estos cinco comandos son los que usarás para inspeccionarlos.

Son distintos entre sí porque sirven para cosas distintas: `cat` para ver rápido, `less` para archivos largos, `head` y `tail` para los extremos, y `grep` para encontrar exactamente lo que buscas.

---

## cat — Mostrar el contenido completo

`cat` (*concatenate*) muestra el contenido completo de uno o varios archivos de una vez. Es el comando más directo — vuelca todo el archivo en la terminal sin paginación.

```bash
cat archivo.txt                    # mostrar un archivo
cat /etc/hostname                  # ver el nombre del sistema
cat /etc/os-release                # información de la distribución
cat archivo1.txt archivo2.txt      # concatenar y mostrar dos archivos
cat -n archivo.txt                 # mostrar con números de línea
cat -A archivo.txt                 # mostrar caracteres especiales (tabs, fin de línea)
cat -s archivo.txt                 # comprimir líneas vacías consecutivas en una
```

### Cuándo usar cat — y cuándo no

`cat` es perfecto para archivos cortos. Para archivos largos (logs, configs extensas), el contenido pasa volando por la pantalla y no puedes leerlo. En ese caso, usa `less`.

```bash
# Perfecto para cat:
cat /etc/hostname          # una línea
cat /etc/hosts             # ~10 líneas
cat ~/.bashrc              # config personal

# Mejor usar less:
cat /var/log/syslog        # miles de líneas — useless
```

### cat para crear archivos rápidos

```bash
# Crear un archivo con contenido directamente
cat > notas.txt << EOF
Esto es una nota rápida
Segunda línea
EOF

# Añadir contenido a un archivo existente
cat >> notas.txt << EOF
Línea añadida al final
EOF
```

> 💡 `cat archivo1 archivo2 > combinado.txt` — concatena dos archivos en uno. De aquí viene el nombre: **cat**enate.

---

## less — Leer archivos largos con comodidad

`less` abre el archivo en un visor paginado que no llena la terminal. Puedes desplazarte hacia arriba y abajo, buscar texto, y salir cuando quieras. Es el estándar para leer archivos largos.

```bash
less /var/log/syslog
less /etc/nginx/nginx.conf
less ~/.bash_history
```

### Controles de less

| Tecla | Acción |
|-------|--------|
| `↑` / `↓` | Mover una línea |
| `Space` / `b` | Página siguiente / anterior |
| `g` | Ir al inicio del archivo |
| `G` | Ir al final del archivo |
| `/texto` | Buscar hacia adelante |
| `?texto` | Buscar hacia atrás |
| `n` | Siguiente coincidencia |
| `N` | Coincidencia anterior |
| `q` | Salir |

```bash
# Abrir less en la última línea (útil para logs)
less +G /var/log/auth.log

# Abrir less buscando directamente "error"
less +/error /var/log/syslog

# Ver un archivo con números de línea
less -N /etc/nginx/nginx.conf
```

> 💡 **less is more** — literalmente. `more` fue el paginador original de Unix, pero tenía limitaciones (no podía ir hacia atrás). `less` se creó como mejora y se llamó así por el chiste: *less is more than more*.

---

## head — Ver el inicio de un archivo

`head` muestra las primeras líneas de un archivo. Por defecto, las 10 primeras.

```bash
head archivo.txt              # primeras 10 líneas
head -n 20 archivo.txt        # primeras 20 líneas
head -n 1 /etc/hostname       # solo la primera línea
head -c 100 archivo.txt       # primeros 100 bytes
```

### Usos prácticos de head

```bash
# Ver la cabecera de un CSV sin cargar todo
head -n 5 datos.csv

# Verificar el shebang de un script
head -n 1 script.sh
# #!/bin/bash

# Ver el frontmatter de un archivo markdown
head -n 10 post.md

# Ver las primeras entradas del log del sistema
head -n 50 /var/log/syslog
```

---

## tail — Ver el final de un archivo

`tail` muestra las últimas líneas. Por defecto, las 10 últimas. Su opción más importante es `-f` — **follow**, que mantiene el archivo abierto y muestra nuevas líneas en tiempo real.

```bash
tail archivo.txt              # últimas 10 líneas
tail -n 20 archivo.txt        # últimas 20 líneas
tail -n +5 archivo.txt        # desde la línea 5 hasta el final
tail -c 200 archivo.txt       # últimos 200 bytes
tail -f /var/log/syslog       # seguir el archivo en tiempo real
tail -f /var/log/nginx/access.log  # monitorizar accesos web en vivo
```

### tail -f — La herramienta de monitorización en tiempo real

`tail -f` es uno de los comandos más usados por administradores de sistemas y profesionales de seguridad. Mantiene el archivo abierto y muestra cada nueva línea que se añade:

```bash
# Monitorizar logs de autenticación en tiempo real
tail -f /var/log/auth.log

# Monitorizar múltiples archivos a la vez
tail -f /var/log/syslog /var/log/auth.log

# Con grep para filtrar solo los errores
tail -f /var/log/nginx/error.log | grep "ERROR"
```

> ⚠️ En ciberseguridad, `tail -f /var/log/auth.log` permite ver intentos de login SSH en tiempo real. Un atacante haciendo fuerza bruta aparece inmediatamente como decenas de líneas "Failed password for root" por segundo.

---

## grep — Buscar texto dentro de archivos

`grep` (*Global Regular Expression Print*) busca líneas que contienen un patrón de texto. Es posiblemente el comando más potente de esta lista — y uno de los más usados en todo Linux.

```bash
grep "error" archivo.log             # líneas que contienen "error"
grep -i "error" archivo.log          # ignorar mayúsculas/minúsculas
grep -n "error" archivo.log          # mostrar número de línea
grep -v "error" archivo.log          # líneas que NO contienen "error"
grep -r "password" /etc/             # buscar recursivamente en un directorio
grep -l "password" /etc/*.conf       # solo mostrar nombres de archivos
grep -c "error" archivo.log          # contar cuántas líneas coinciden
grep -A 3 "error" archivo.log        # 3 líneas después de cada coincidencia
grep -B 3 "error" archivo.log        # 3 líneas antes de cada coincidencia
grep -C 3 "error" archivo.log        # 3 líneas de contexto antes y después
```

### Expresiones regulares básicas con grep

```bash
grep "^root" /etc/passwd           # líneas que empiezan por "root"
grep "bash$" /etc/passwd           # líneas que terminan en "bash"
grep "192\.168\." /var/log/auth.log # IPs de red local
grep "[0-9]\{3\}" archivo.txt      # secuencias de 3 dígitos
grep -E "error|warning|critical" archivo.log  # múltiples patrones con -E
```

### grep -r — Buscar en directorios completos

```bash
# Buscar configuraciones con contraseñas en texto plano
grep -r "password" /etc/ 2>/dev/null

# Buscar todos los archivos que importan una librería
grep -r "import requests" /var/www/ --include="*.py"

# Buscar comentarios TODO en código
grep -rn "TODO\|FIXME" /home/ubuntu/projects/
```

---

## Combinando los comandos — El poder de las pipes

Estos comandos se vuelven exponencialmente más potentes al combinarse con pipes `|`:

```bash
# Ver los últimos 100 errores de nginx
tail -n 100 /var/log/nginx/error.log | grep "ERROR"

# Contar cuántos intentos fallidos de SSH hay en el log
grep "Failed password" /var/log/auth.log | wc -l

# Ver qué IPs están haciendo fuerza bruta
grep "Failed password" /var/log/auth.log | grep -oE "[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+" | sort | uniq -c | sort -rn | head -10

# Buscar usuarios del sistema que usan bash
grep "bash$" /etc/passwd | cut -d: -f1

# Ver los procesos más consumidores de CPU (sin ps aux, usando /proc)
cat /proc/*/status 2>/dev/null | grep -A1 "Name:" | head -40
```

### Buscar y paginar a la vez

```bash
# Buscar en un archivo largo y paginar los resultados
grep "error" /var/log/syslog | less

# Ver el archivo de configuración de SSH de forma limpia
grep -v "^#" /etc/ssh/sshd_config | grep -v "^$" | less
```

---

## strings — Leer texto en archivos binarios

`strings` extrae cadenas de texto legibles de archivos binarios — ejecutables, imágenes de disco, archivos de memoria:

```bash
strings /bin/ls | head -20          # strings en el binario ls
strings /usr/bin/passwd             # strings en el binario passwd
strings archivo.bin | grep -i "password"  # buscar contraseñas en binario
strings /proc/PID/mem 2>/dev/null   # strings en la memoria de un proceso
```

---

## Relevancia en ciberseguridad

| Comando | Técnica / Uso en seguridad |
|---------|--------------------------|
| `cat /etc/passwd` | Enumeración de usuarios del sistema |
| `cat /etc/shadow` | Obtención de hashes de contraseñas |
| `grep -r "password" /var/www/` | Buscar credenciales hardcodeadas en código web |
| `grep "Failed password" /var/log/auth.log` | Detectar ataques de fuerza bruta SSH |
| `tail -f /var/log/auth.log` | Monitorización en tiempo real de intentos de acceso |
| `grep -r "PRIVATE KEY" /home/` | Buscar claves privadas SSH expuestas |
| `grep -v "^#" /etc/sudoers` | Ver reglas sudo sin comentarios |
| `strings malware.bin` | Análisis básico estático de malware |
| `grep -oE "([0-9]{1,3}\.){3}[0-9]{1,3}" access.log` | Extraer todas las IPs de un log |

```bash
# Escenario real: análisis básico de un servidor comprometido

# 1. Ver los últimos logins exitosos
grep "Accepted password\|Accepted publickey" /var/log/auth.log | tail -20

# 2. Ver qué IPs están intentando fuerza bruta ahora mismo  
tail -f /var/log/auth.log | grep "Failed password"

# 3. Buscar webshells en el servidor web
grep -r "system\|exec\|passthru\|shell_exec" /var/www/ --include="*.php" -l

# 4. Ver si alguien modificó archivos críticos recientemente
ls -lt /etc/ | head -10
```
