---
title: 'Permisos en Linux — rwx, chmod y el modelo de seguridad'
description: 'Cómo funciona el sistema de permisos de Linux: lectura, escritura y ejecución para usuarios, grupos y otros. chmod en modo simbólico y numérico.'
pubDate: 'Mar 28 2026 10:00:00'
heroImage: '../../assets/linux-perms-hero.svg'
category: 'linux'
---

Cada archivo en Linux tiene un propietario, un grupo y un conjunto de permisos. Esta no es una característica opcional — es la columna vertebral de la seguridad del sistema operativo. Sin ella, cualquier proceso podría leer cualquier archivo, cualquier usuario podría borrar cualquier cosa, y el concepto de "sistema multiusuario seguro" no existiría.

Entender los permisos de Linux es **obligatorio** para cualquier profesional de sistemas o seguridad. Es la diferencia entre saber ejecutar comandos y entender realmente cómo funciona Linux.

---

## El modelo de tres capas

Linux usa un modelo de permisos basado en **tres entidades** y **tres tipos de acceso**:

### Las tres entidades

| Símbolo | Nombre | Quién es |
|---------|--------|---------|
| `u` | **user** | El propietario del archivo |
| `g` | **group** | Los usuarios del grupo asignado al archivo |
| `o` | **others** | Todos los demás usuarios del sistema |

### Los tres tipos de acceso

| Símbolo | Nombre | En archivos | En directorios |
|---------|--------|-------------|----------------|
| `r` | **read** | Leer el contenido | Listar el contenido con ls |
| `w` | **write** | Modificar el contenido | Crear, renombrar y borrar archivos dentro |
| `x` | **execute** | Ejecutar como programa | Entrar con cd y acceder a archivos dentro |

---

## Leyendo los permisos — `ls -la`

```bash
ls -la /etc/passwd
-rw-r--r-- 1 root root 2847 Mar 21 09:00 /etc/passwd
```

El primer campo tiene 10 caracteres. Vamos carácter a carácter:

```
- rw- r-- r--
│ │   │   │
│ │   │   └── others: puede leer (r), no escribir (-), no ejecutar (-)
│ │   └────── group:  puede leer (r), no escribir (-), no ejecutar (-)
│ └────────── user:   puede leer (r), puede escribir (w), no ejecutar (-)
└──────────── tipo:   - archivo regular, d directorio, l symlink
```

Ejemplos reales del sistema:

```bash
ls -la /etc/shadow
---------- 1 root shadow 1423 Mar 21 09:00 /etc/shadow
# nadie puede leer shadow directamente — solo root via sudo

ls -la /usr/bin/passwd
-rwsr-xr-x 1 root root 68208 Mar 21 09:00 /usr/bin/passwd
# la 's' en rwsr es el bit SUID — se ejecuta como root

ls -la /tmp
drwxrwxrwt 12 root root 4096 Mar 28 10:00 /tmp
# 't' es el sticky bit — cualquiera puede escribir pero solo borrar lo suyo
```

---

## chmod — Cambiar permisos

`chmod` (*change mode*) modifica los permisos de un archivo. Tiene dos modos: **simbólico** y **octal**.

### Modo simbólico — intuitivo y legible

```bash
chmod u+x script.sh          # añadir ejecución al propietario
chmod g-w archivo.txt        # quitar escritura al grupo
chmod o-r secreto.txt        # quitar lectura a otros
chmod a+r publico.txt        # añadir lectura a todos (a = all)
chmod u+x,g+r script.sh      # múltiples cambios a la vez
chmod u=rwx,g=rx,o= dir/     # establecer permisos exactos
```

### Modo octal — preciso y rápido

Cada tipo de permiso tiene un valor numérico:

| Permiso | Valor |
|---------|-------|
| `r` | 4 |
| `w` | 2 |
| `x` | 1 |
| ninguno | 0 |

Se suman para cada entidad y se combinan en un número de 3 dígitos:

```
chmod 755 script.sh

7 = 4+2+1 = rwx  → propietario puede todo
5 = 4+0+1 = r-x  → grupo puede leer y ejecutar
5 = 4+0+1 = r-x  → otros pueden leer y ejecutar
```

Los valores más comunes:

| Octal | Simbólico | Uso típico |
|-------|-----------|-----------|
| `755` | `rwxr-xr-x` | Scripts y directorios públicos |
| `644` | `rw-r--r--` | Archivos de texto y configuración |
| `600` | `rw-------` | Claves privadas, archivos personales |
| `700` | `rwx------` | Directorios y scripts privados |
| `777` | `rwxrwxrwx` | ⚠️ Peligroso — todos tienen acceso total |
| `000` | `----------` | Sin acceso para nadie |

```bash
chmod 644 /etc/hosts           # config legible por todos, editable por root
chmod 600 ~/.ssh/id_rsa        # clave SSH — solo el propietario puede leer
chmod 755 /usr/local/bin/mi-script  # script ejecutable por todos
chmod -R 755 /var/www/html/    # -R aplica recursivamente al directorio
```

> ⚠️ SSH rechaza claves privadas con permisos demasiado abiertos. Si `~/.ssh/id_rsa` tiene permisos 644 o superiores, SSH te mostrará `WARNING: UNPROTECTED PRIVATE KEY FILE!` y no conectará.

---

## chown y chgrp — Cambiar propietario y grupo

```bash
chown usuario archivo.txt              # cambiar propietario
chown usuario:grupo archivo.txt        # cambiar propietario y grupo
chown :grupo archivo.txt               # cambiar solo el grupo
chown -R usuario:grupo /var/www/html/  # recursivo

chgrp grupo archivo.txt                # cambiar solo el grupo (alternativa)
```

```bash
# Escenario real: arreglar un servidor web
ls -la /var/www/html/index.html
-rw-r--r-- 1 root root 1234 Mar 21 09:00 /var/www/html/index.html
# El servidor nginx corre como www-data y no puede escribir aquí

chown www-data:www-data /var/www/html/index.html
# o dar permisos de escritura al grupo:
chmod 664 /var/www/html/index.html
```

---

## El bit SUID, SGID y Sticky bit

Más allá de rwx, existen tres bits especiales:

### SUID (Set User ID) — `s` en posición del propietario
El ejecutable se corre con los permisos del **propietario**, no del usuario que lo ejecuta.

```bash
ls -la /usr/bin/passwd
-rwsr-xr-x 1 root root 68208 Mar passwd
#    ↑ s = SUID activo
```

`passwd` es SUID root porque necesita escribir en `/etc/shadow` — un archivo solo accesible por root. Sin SUID, ningún usuario podría cambiar su propia contraseña.

```bash
# Ver todos los binarios SUID del sistema
find / -perm -u=s -type f 2>/dev/null

# Establecer SUID en un binario propio
chmod u+s mi-programa
chmod 4755 mi-programa   # 4 = SUID
```

> ⚠️ **SUID = objetivo principal en privilege escalation**. Si un binario SUID tiene una vulnerabilidad, un atacante puede explotarla para ejecutar código como root. `find / -perm -u=s` es uno de los primeros comandos que ejecuta un atacante tras obtener acceso.

### SGID (Set Group ID) — `s` en posición del grupo
En ejecutables: corre con los permisos del grupo del archivo.
En directorios: los archivos creados dentro heredan el grupo del directorio.

```bash
chmod g+s directorio/     # activar SGID en un directorio
chmod 2755 directorio/    # 2 = SGID
```

### Sticky Bit — `t` en posición de otros
Solo en directorios. Aunque todos puedan escribir en el directorio, cada usuario solo puede borrar sus propios archivos.

```bash
ls -la /tmp
drwxrwxrwt  # la 't' al final es el sticky bit

chmod +t /directorio-compartido/
chmod 1777 /directorio-compartido/  # 1 = sticky bit
```

---

## umask — El filtro de permisos por defecto

Cuando creas un archivo nuevo, Linux no le da todos los permisos posibles. El `umask` define qué permisos se **eliminan** por defecto:

```bash
umask           # ver el umask actual
# 0022

# Permisos máximos: 666 para archivos, 777 para directorios
# Con umask 022:
# Archivos:     666 - 022 = 644 (rw-r--r--)
# Directorios:  777 - 022 = 755 (rwxr-xr-x)

umask 027       # cambiar umask: grupo sin escritura, otros sin nada
# Archivos:     666 - 027 = 640
# Directorios:  777 - 027 = 750
```

---

## Relevancia en ciberseguridad

| Escenario | Permiso | Riesgo |
|-----------|---------|--------|
| `/etc/shadow` con permisos 644 | `rw-r--r--` | Cualquiera puede leer hashes de contraseñas |
| Script con SUID mal configurado | `-rwsr-xr-x` | Escalada de privilegios |
| `/tmp` sin sticky bit | `drwxrwxrwx` | Cualquiera borra los archivos de otros |
| Clave SSH con permisos 644 | `rw-r--r--` | Cualquier usuario puede copiar tu clave privada |
| Web shell subida con 777 | `rwxrwxrwx` | Ejecutable por el servidor web |
| `.env` con credenciales con 644 | `rw-r--r--` | Cualquier usuario del sistema lee las credenciales |

```bash
# Checklist de seguridad básica de permisos
find /home -name "*.ssh" -type d | xargs ls -la    # verificar permisos de .ssh
find / -perm -u=s -type f 2>/dev/null               # binarios SUID
find / -perm -o=w -type f 2>/dev/null               # archivos escribibles por otros
find /var/www -type f -name "*.php" -perm -o+x      # PHP ejecutable por otros
stat /etc/shadow                                     # verificar permisos de shadow
```
