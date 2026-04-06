---
title: 'Propiedad de archivos en Linux - chown, chgrp y multiusuario'
description: 'Como funciona la propiedad de archivos en Linux: usuario, grupo, chown, chgrp, grupos suplementarios y seguridad en entornos multiusuario.'
pubDate: 'Apr 06 2026 11:30:00'
heroImage: '../../assets/linux-ownership-hero.svg'
category: 'linux'
---

En Linux, los permisos no existen en el vacio. Siempre se aplican sobre algo previo: **quien es el propietario del archivo y a que grupo pertenece**.

Eso convierte la propiedad en una pieza central del modelo multiusuario.

Si los permisos deciden *que puede hacerse*, la propiedad decide *quien entra en cada regla*.

Por eso entender `chown`, `chgrp` y la logica de usuarios y grupos no es un detalle administrativo: es parte del mecanismo de seguridad del sistema.

![Mapa de propiedad de archivos](/linux-ownership-map.svg)

---

## La idea central

Cada archivo y directorio en Linux tiene:

- un **propietario**
- un **grupo**
- un conjunto de permisos aplicados a:
  - user
  - group
  - others

La propiedad responde a dos preguntas:

1. Que usuario es el dueño directo del recurso.
2. Que grupo tiene relacion especial con ese recurso.

Sin eso, el modelo de permisos no sabria a quien aplicar cada bloque.

---

## Propietario y grupo

Cuando haces `ls -l`, Linux te muestra ambos campos:

```bash
ls -l informe.txt
-rw-r----- 1 alice analistas 842 Apr  6 10:00 informe.txt
```

Aqui:

- `alice` es el **propietario**
- `analistas` es el **grupo**

Eso significa:

- los permisos del bloque `user` se aplican a `alice`
- los permisos del bloque `group` se aplican a los usuarios miembros de `analistas`
- el bloque `others` se aplica a cualquiera que no entre en los dos casos anteriores

---

## Multiusuario: por que importa

Linux esta diseñado como sistema multiusuario desde la base.

Eso implica que varias personas, procesos y servicios conviven en el mismo sistema con diferentes niveles de acceso.

Sin propiedad y grupos:

- cualquier usuario podria tocar archivos ajenos
- no habria separacion real entre perfiles
- compartir recursos seria caotico
- la seguridad del sistema seria mucho mas debil

La propiedad es una forma de ordenar el acceso sin tener que definir reglas archivo por archivo para cada persona.

![Usuarios y grupos](/linux-multiuser-groups.svg)

---

## Usuario propietario

El propietario suele ser:

- el usuario que creo el archivo
- o el usuario al que luego se reasigno con `chown`

Ese usuario es el que recibe los permisos del bloque `u` o `user`.

### Ejemplo

```bash
-rw------- 1 juan juan 1280 notas.txt
```

Aqui solo `juan` puede leer y escribir `notas.txt`. El grupo y others no tienen acceso.

---

## Grupo propietario

El grupo asociado al archivo permite compartir acceso de forma ordenada.

En vez de dar permisos individualmente a cada usuario, puedes:

- crear o usar un grupo
- asignar archivos a ese grupo
- configurar permisos sobre el bloque `group`

### Ejemplo

```bash
-rw-rw---- 1 root developers 4096 app.log
```

Aqui:

- `root` es el propietario
- el grupo `developers` puede leer y escribir
- el resto no tiene acceso

Este modelo es muy util para trabajo colaborativo o servicios compartidos.

---

## chown - cambiar propietario

`chown` significa **change owner**.

Sirve para cambiar el propietario de un archivo y, si quieres, tambien el grupo.

### Sintaxis comun

```bash
chown usuario archivo
chown usuario:grupo archivo
chown :grupo archivo
chown -R usuario:grupo directorio/
```

### Ejemplos

```bash
chown maria informe.txt
chown root:developers app.log
chown :www-data /var/www/html/index.php
chown -R www-data:www-data /var/www/html/
```

Puntos importantes:

- normalmente solo `root` puede cambiar el propietario a otro usuario
- un usuario puede, en ciertos contextos, cambiar el grupo si pertenece a ese grupo y el sistema lo permite
- `-R` aplica el cambio de forma recursiva

---

## chgrp - cambiar grupo

`chgrp` significa **change group**.

Es una forma mas especifica de cambiar solo el grupo del archivo:

```bash
chgrp developers app.log
chgrp -R www-data /var/www/html/
```

Funcionalmente, muchas veces equivale a:

```bash
chown :developers app.log
```

Pero `chgrp` deja mas claro que solo estas tocando el grupo.

---

## Grupos primarios y suplementarios

Cada usuario en Linux suele tener:

- un **grupo primario**
- cero o mas **grupos suplementarios**

Puedes verlo con:

```bash
id
groups
id juan
```

### Ejemplo

```bash
uid=1001(juan) gid=1001(juan) groups=1001(juan),27(sudo),1002(developers)
```

Eso significa que `juan` pertenece a:

- su grupo primario `juan`
- los grupos suplementarios `sudo` y `developers`

Si un archivo pertenece al grupo `developers`, `juan` podra usar los permisos del bloque `group`.

---

## Nuevos archivos y grupo por defecto

Cuando un usuario crea un archivo, normalmente:

- el propietario sera ese usuario
- el grupo sera su grupo primario

Ejemplo:

```bash
touch prueba.txt
ls -l prueba.txt
-rw-r--r-- 1 juan juan 0 Apr 6 11:00 prueba.txt
```

Pero esto puede cambiar en directorios con SGID, donde los archivos heredan el grupo del directorio.

Eso es muy util en carpetas colaborativas.

---

## SGID en directorios y trabajo compartido

Si activas el bit SGID en un directorio:

```bash
chmod g+s proyecto/
```

los archivos nuevos creados dentro heredaran el grupo del directorio.

### Ejemplo practico

```bash
mkdir proyecto
chown root:developers proyecto
chmod 2775 proyecto
```

Resultado:

- el directorio pertenece al grupo `developers`
- todo archivo nuevo dentro hereda ese grupo
- se facilita el trabajo colaborativo sin tener que corregir grupo a mano cada vez

---

## Casos reales

### Servidor web

```bash
chown -R www-data:www-data /var/www/html/
```

Sirve para que el proceso del servidor web tenga la propiedad adecuada sobre ciertos archivos o directorios.

### Directorio compartido entre analistas

```bash
chown root:analistas /srv/analisis
chmod 2770 /srv/analisis
```

Aqui:

- `root` conserva la propiedad
- el grupo `analistas` comparte acceso
- SGID asegura herencia del grupo

### Error comun

```bash
chown -R usuario:usuario /var/www/
```

Puede parecer comodo, pero si no entiendes que proceso necesita realmente acceso, puedes dar propiedad excesiva o romper el modelo esperado del servicio.

![Flujo de cambio de propiedad](/linux-ownership-flow.svg)

---

## Relevancia en ciberseguridad

La propiedad de archivos importa directamente en seguridad porque afecta a:

- que procesos pueden leer secretos
- que usuarios pueden modificar configuraciones
- como se comparte informacion sensible
- que cuentas pueden tocar datos criticos
- si un servicio comprometido puede alterar mas cosas de las necesarias

### Riesgos frecuentes

- propietario incorrecto en claves privadas
- grupos demasiado amplios
- directorios colaborativos mal configurados
- servicios corriendo con cuentas demasiado privilegiadas
- cambios recursivos mal pensados con `chown -R`

Muchos fallos de seguridad en Linux no se deben a que el comando este mal escrito, sino a que el modelo de propiedad esta mal diseñado.

---

## Errores comunes

**1. Pensar que permisos y propiedad son lo mismo**  
Los permisos dependen de la propiedad, pero no son la misma cosa.

**2. Usar `chown -R` sin revisar**  
Puedes cambiar propietarios masivamente y romper acceso legitimo o crear sobreprivilegio.

**3. Ignorar los grupos suplementarios**  
Un usuario puede heredar acceso por grupo aunque no sea el propietario.

**4. No usar grupos para compartir correctamente**  
Acabas resolviendo a base de permisos laxos.

**5. Ejecutar servicios con propietarios inadecuados**  
Eso amplifica el impacto si el servicio cae.

---

## Resumen

- cada archivo en Linux tiene propietario y grupo
- el bloque `user` aplica al propietario, `group` al grupo y `others` al resto
- `chown` cambia propietario y opcionalmente grupo
- `chgrp` cambia solo el grupo
- el modelo multiusuario depende fuertemente de esta estructura
- SGID en directorios ayuda mucho en entornos colaborativos
- una mala propiedad de archivos puede traducirse en problemas reales de seguridad

En la guia interactiva asociada veremos la propiedad de archivos de forma visual, con ejemplos multiusuario, herencia de grupos y un quiz de 10 preguntas.
