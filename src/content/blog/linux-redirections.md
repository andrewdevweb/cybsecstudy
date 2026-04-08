---
title: 'Redirecciones en Linux - >, >>, < y 2>'
description: 'Como mover la entrada, la salida y los errores en Linux usando redirecciones de shell de forma clara y segura.'
pubDate: 'Apr 08 2026 18:25:00'
heroImage: '../../assets/linux-redirections-hero.svg'
category: 'linux'
---

Las redirecciones son una de las ideas mas potentes de la terminal de Linux. No anaden "magia" al sistema: simplemente cambian de donde viene la entrada de un programa y hacia donde van su salida normal o sus errores.

Si entiendes bien `STDIN`, `STDOUT` y `STDERR`, las redirecciones dejan de ser simbolos raros y pasan a ser algo muy natural: **mover flujos de datos**.

![Mapa visual de redirecciones](/linux-redirections-map.svg)

---

## La idea central

Por defecto, en una terminal interactiva:

- `STDIN` viene del teclado
- `STDOUT` se muestra en pantalla
- `STDERR` tambien se muestra en pantalla

Con las redirecciones puedes cambiar ese comportamiento:

- leer desde un archivo en vez de desde el teclado
- guardar la salida en un archivo
- separar los errores en otro archivo
- anadir resultados a un log sin sobrescribir lo anterior

Esto es muy importante en administracion, scripting, analisis de logs y automatizacion.

---

## `>` - redirigir salida y sobrescribir

El operador `>` envia `STDOUT` a un archivo.

Si el archivo no existe, se crea. Si ya existe, **se sobrescribe**.

### Ejemplos

```bash
echo "hola" > salida.txt
pwd > ruta.txt
ls /etc > listado.txt
```

En todos estos casos, el resultado normal del comando no se queda en pantalla: se guarda en el archivo indicado.

### Idea clave

`>` trabaja con la salida normal, no con los errores.

Si el comando falla, el mensaje de error seguira saliendo por pantalla salvo que redirijas `STDERR` por separado.

---

## `>>` - anadir salida sin borrar lo anterior

El operador `>>` tambien redirige `STDOUT`, pero en lugar de reemplazar el contenido del archivo, **lo anade al final**.

### Ejemplos

```bash
date >> actividad.log
echo "backup completado" >> actividad.log
whoami >> auditoria.txt
```

Es muy util para construir logs, historicos de ejecucion o registros sencillos.

### Cuanto usar `>` y cuanto `>>`

- usa `>` cuando quieres un archivo nuevo o limpio
- usa `>>` cuando quieres conservar lo que ya habia

Esta diferencia parece pequena, pero evita muchos errores tontos al trabajar con logs.

![Flujo de redireccion de streams](/linux-redirections-flow.svg)

---

## `<` - redirigir entrada

El operador `<` cambia el origen de `STDIN`.

En vez de leer del teclado, el programa lee desde un archivo.

### Ejemplos

```bash
sort < nombres.txt
wc -l < usuarios.txt
cat < mensaje.txt
```

Eso significa que el archivo se comporta como si fuese la entrada que un usuario iria escribiendo.

### Por que importa

`<` hace que muchas herramientas que esperan entrada puedan trabajar de forma no interactiva, algo muy util en scripts y automatizacion.

---

## `2>` - redirigir errores

El operador `2>` redirige `STDERR`, es decir, la salida de error estandar.

### Ejemplos

```bash
ls archivo-que-no-existe 2> errores.log
find /root -name "*.conf" 2> permisos-denegados.log
```

Si el comando genera errores, iran al archivo indicado. La salida normal sigue su camino habitual salvo que tambien la redirijas.

### Relacion con los descriptores

- `0` = `STDIN`
- `1` = `STDOUT`
- `2` = `STDERR`

Por eso `2>` afecta al flujo de error y no al de salida normal.

---

## Comportamientos tipicos que debes visualizar

### Caso 1 - solo salida normal

```bash
echo "ok" > resultado.txt
```

- `STDOUT` va a `resultado.txt`
- `STDERR` seguiria yendo a pantalla si apareciera algun error

### Caso 2 - solo errores

```bash
ls archivo-que-no-existe 2> error.log
```

- `STDOUT` no se redirige
- `STDERR` va a `error.log`

### Caso 3 - entrada desde archivo

```bash
sort < datos.txt
```

- `STDIN` viene de `datos.txt`
- `STDOUT` sigue yendo a pantalla

### Caso 4 - construir un log sin machacarlo

```bash
date >> actividad.log
```

- el archivo conserva su contenido previo
- la nueva linea se agrega al final

---

## Redirecciones en tareas reales

En la practica, las redirecciones aparecen continuamente:

- guardar resultados de enumeracion
- generar listados para revisarlos luego
- capturar errores de permisos durante un `find`
- alimentar un comando con datos preexistentes
- escribir logs simples de ejecucion en scripts

En ciberseguridad y administracion esto es especialmente util porque permite **separar datos utiles de ruido**, algo clave cuando analizas mucha salida de terminal.

---

## Errores comunes

**1. Usar `>` cuando querias conservar el archivo**  
Sobrescribe el contenido anterior. Si lo que quieres es acumular resultados, necesitas `>>`.

**2. Pensar que `>` guarda tambien los errores**  
No. `>` afecta a `STDOUT`. Los errores siguen en `STDERR`.

**3. Ver `2>` como una excepcion arbitraria**  
No es arbitrario: el `2` representa el descriptor de error.

**4. Olvidar que `<` cambia la entrada**  
No imprime el archivo por si solo; hace que el programa lea desde el.

**5. Aprender simbolos sin imaginar el flujo**  
Cuando visualizas origen y destino, todo encaja mejor.

![Chuleta rapida de redirecciones](/linux-redirections-cheatsheet.svg)

---

## Relacion con el siguiente tema

Las redirecciones mueven flujos entre **programas y archivos**.

El siguiente paso natural son los **pipes**, donde la salida de un programa alimenta directamente la entrada de otro:

- redirecciones -> programa <-> archivo
- pipes -> programa <-> programa

Entender bien esta diferencia te ayudara mucho a pensar en la filosofia Unix.

---

## Resumen

- `>` redirige `STDOUT` y sobrescribe
- `>>` redirige `STDOUT` y anade al final
- `<` redirige `STDIN`
- `2>` redirige `STDERR`
- las redirecciones no son magia, sino control del flujo de datos
- dominarlas mejora scripting, automatizacion, administracion y analisis

En la guia interactiva asociada veremos estos operadores de forma visual, con escenarios, comparativas y un quiz de 10 preguntas.
