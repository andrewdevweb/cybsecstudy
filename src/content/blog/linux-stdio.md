---
title: 'Entrada y salida estandar en Linux - STDIN, STDOUT y STDERR'
description: 'Como fluye la informacion en la terminal de Linux: entrada estandar, salida estandar, errores y descriptores de archivo.'
pubDate: 'Apr 08 2026 10:20:00'
heroImage: '../../assets/linux-stdio-hero.svg'
category: 'linux'
---

La terminal parece muy simple desde fuera: escribes un comando, pulsas Enter y aparece algo en pantalla. Pero por debajo ocurre un modelo muy elegante que explica como los programas reciben datos y como los devuelven.

Ese modelo se basa en tres canales estandar:

- **STDIN** - entrada estandar
- **STDOUT** - salida estandar
- **STDERR** - salida de error estandar

Entenderlos es clave para comprender de verdad la filosofia Unix y para manejar bien redirecciones, pipes, scripting y automatizacion.

![Mapa de STDIN STDOUT STDERR](/linux-stdio-map.svg)

---

## La idea central

Cuando un programa se ejecuta en Linux, normalmente trabaja con tres flujos de datos ya preparados por el sistema:

- uno para **recibir entrada**
- uno para **emitir salida normal**
- uno para **emitir errores**

Eso significa que el programa no necesita “inventar” un mecanismo nuevo cada vez para leer o escribir. Usa esos canales estandar.

Esta decision de diseño es una de las razones por las que la terminal de Linux es tan composable.

---

## STDIN - entrada estandar

`STDIN` es la entrada estandar del programa.

Normalmente, en una terminal interactiva, `STDIN` viene del teclado.

### Ejemplos

- cuando escribes texto en `cat`
- cuando una herramienta te pide confirmacion
- cuando un script lee datos introducidos por el usuario

Ejemplo simple:

```bash
cat
hola
hola
```

`cat` esta leyendo de `STDIN` y reproduciendo lo que recibe.

---

## STDOUT - salida estandar

`STDOUT` es la salida normal del programa.

En una terminal interactiva, normalmente va a la pantalla.

### Ejemplos

- la lista que muestra `ls`
- el texto que imprime `echo`
- el resultado de `pwd`
- la salida correcta de un script

Ejemplo:

```bash
pwd
/home/juan
```

Ese resultado ha salido por `STDOUT`.

---

## STDERR - salida de error estandar

`STDERR` es un canal separado para errores y mensajes de diagnostico.

Aunque tambien suele verse en pantalla, no es lo mismo que `STDOUT`.

### Ejemplo

```bash
ls archivo-que-no-existe
ls: cannot access 'archivo-que-no-existe': No such file or directory
```

Ese mensaje no sale por `STDOUT`, sino por `STDERR`.

Esta separacion es muy importante porque permite tratar la salida normal y los errores de forma distinta.

![Los tres flujos](/linux-stdio-streams.svg)

---

## Los descriptores de archivo 0, 1 y 2

En Linux, estos tres canales se identifican con numeros llamados **descriptores de archivo**:

- `0` -> `STDIN`
- `1` -> `STDOUT`
- `2` -> `STDERR`

Esos numeros se vuelven muy importantes cuando empieces a usar redirecciones:

- `<` trabaja con `STDIN`
- `>` trabaja con `STDOUT`
- `2>` trabaja con `STDERR`

No hace falta memorizarlo como algo aislado. Tiene sentido si lo conectas con el flujo real de datos.

---

## Por que separar STDOUT y STDERR

Podria parecer que bastaria con una sola salida, pero separarlas tiene muchas ventajas:

### 1. Automatizacion mas limpia

Puedes guardar resultados utiles sin mezclar errores.

### 2. Depuracion mejor

Los errores se pueden observar, redirigir o filtrar aparte.

### 3. Scripts mas robustos

Un programa puede producir datos validos por `STDOUT` y avisos por `STDERR` sin mezclar ambos.

### 4. Filosofia Unix

Las herramientas se vuelven mas reutilizables cuando su salida normal esta separada de su ruido diagnostico.

---

## Ejemplos conceptuales

### Comando exitoso

```bash
echo "hola"
```

- `STDIN`: no necesita
- `STDOUT`: imprime `hola`
- `STDERR`: no usa

### Comando con error

```bash
ls /ruta/inexistente
```

- `STDIN`: no necesita
- `STDOUT`: nada util
- `STDERR`: mensaje de error

### Comando interactivo

```bash
cat
```

- `STDIN`: recibe lo que escribes
- `STDOUT`: devuelve lo recibido
- `STDERR`: solo si algo falla

---

## La terminal como intermediaria

La terminal actua como el entorno visual donde esos flujos se conectan al usuario.

Normalmente:

- el teclado alimenta `STDIN`
- la pantalla muestra `STDOUT`
- la pantalla tambien muestra `STDERR`

Pero eso no significa que sean el mismo canal. Solo coincide el destino visual por defecto.

Este detalle es esencial para comprender lo que pasara despues con:

- redirecciones
- pipes
- logs
- scripts

![Descriptores 0 1 2](/linux-stdio-fd.svg)

---

## Relevancia en scripting y administracion

En administracion de sistemas, entender `STDIN`, `STDOUT` y `STDERR` permite:

- construir scripts mas limpios
- separar errores de resultados
- automatizar tareas con menos ambiguedad
- depurar mejor
- conectar programas entre si

Sin esta idea, muchas redirecciones parecen “magia”. Con esta idea, pasan a ser simplemente flujo de datos.

---

## Errores comunes al estudiar este tema

**1. Pensar que todo lo que ves en pantalla es lo mismo**  
`STDOUT` y `STDERR` pueden verse igual, pero son canales distintos.

**2. Reducir `STDIN` al teclado**  
El teclado es solo la fuente por defecto en modo interactivo.

**3. Memorizar 0, 1 y 2 sin entenderlos**  
Los numeros tienen sentido porque representan canales reales.

**4. Saltar a redirecciones sin entender primero los flujos**  
Eso hace que luego la sintaxis parezca arbitraria.

---

## Resumen

- Linux usa tres flujos estandar: `STDIN`, `STDOUT` y `STDERR`
- `STDIN` es la entrada del programa
- `STDOUT` es la salida normal
- `STDERR` es la salida de error
- se representan con los descriptores `0`, `1` y `2`
- la separacion entre salida normal y errores es fundamental en Unix
- este concepto es la base de redirecciones, pipes y scripting

En la guia interactiva asociada veremos estos tres flujos de forma visual, con ejemplos y un quiz de 10 preguntas.
