---
title: 'Navegacion y sistema de archivos en Windows - cd, dir y rutas'
description: 'Aprende a moverte por el sistema de archivos de Windows con cd, dir, dir /a, rutas absolutas y relativas, y tree.'
pubDate: 'Apr 01 2026 10:30:00'
heroImage: '../../assets/windows-nav-hero.svg'
category: 'windows'
---

En Windows, navegar bien por el sistema de archivos no es un detalle menor. Es la base de casi todo: abrir logs, localizar configuraciones, revisar perfiles de usuario, encontrar scripts, preparar copias de seguridad o investigar que ha cambiado en una maquina.

Si en Linux te apoyas en `pwd`, `ls` y `cd`, en Windows el trio equivalente gira alrededor de **`cd`**, **`dir`** y la comprension de las **rutas**. A eso se suma un comando muy util para visualizar estructura: **`tree`**.

Este post es el paso natural despues de la introduccion a la CLI de Windows. Ya sabes que CMD existe, ahora toca aprender a **moverte con precision**.

---

## El sistema de archivos de Windows en una sola idea

Windows organiza archivos y carpetas en un **arbol jerarquico**, pero con una diferencia importante respecto a Linux: no hay una unica raiz `/`, sino **unidades**.

Ejemplos tipicos:

```cmd
C:\
D:\
E:\
```

`C:\` suele ser la unidad principal del sistema. Dentro de ella aparecen rutas como:

```cmd
C:\Windows
C:\Program Files
C:\Users
C:\ProgramData
```

El separador de ruta es la **barra invertida** `\`, no la barra normal `/`.

```cmd
C:\Users\usuario\Desktop
```

Esta diferencia parece pequena, pero si vienes de Linux o escribes rapido, es uno de los errores mas frecuentes.

---

## cd - saber donde estas y moverte

En CMD, `cd` significa *change directory*. Cambia el directorio actual, pero tambien sirve para **mostrar tu ubicacion** si lo ejecutas sin argumentos.

```cmd
cd
cd Desktop
cd ..
cd \
cd /d D:\
```

### Lo esencial de `cd`

```cmd
cd                  :: muestra el directorio actual
cd Desktop          :: entra en Desktop desde donde estes
cd ..               :: sube un nivel
cd ..\..            :: sube dos niveles
cd \                :: va a la raiz de la unidad actual
cd /d D:\Labs       :: cambia de unidad y directorio a la vez
```

Dos detalles importantes:

**1. `cd` sin argumentos muestra la ruta actual.**  
Es el equivalente practico de `pwd` en Linux.

**2. `cd` no cambia de unidad por si solo.**  
Si estas en `C:\Users\usuario` y escribes:

```cmd
cd D:\Temp
```

puede cambiar la ruta asociada a `D:`, pero seguiras estando en `C:`. Para moverte realmente a otra unidad usa:

```cmd
cd /d D:\Temp
```

o primero:

```cmd
D:
cd \Temp
```

---

## dir - ver que hay dentro

`dir` es el comando de listado de Windows. Muestra archivos y carpetas del directorio actual o de la ruta que le pases.

```cmd
dir
dir C:\Windows
dir C:\Users\usuario\Desktop
```

### Opciones que de verdad importan

```cmd
dir                :: listar contenido del directorio actual
dir /a             :: incluir ocultos y del sistema
dir /ad            :: mostrar solo directorios
dir /b             :: formato basico, solo nombres
dir /s             :: buscar de forma recursiva en subdirectorios
dir /o:n           :: ordenar por nombre
dir /o:-d          :: ordenar por fecha descendente
dir /w             :: formato ancho
dir /p             :: paginar salida
```

Las combinaciones mas utiles:

```cmd
dir /a
dir /b /s *.txt
dir /ad /b
dir /o:-d
```

### Como leer `dir /a`

Cuando usas `dir /a`, CMD muestra tambien elementos ocultos o del sistema que no verias en un listado normal. Es comun encontrarte cosas como:

```cmd
01/04/2026  09:12    <DIR>          .
01/04/2026  09:12    <DIR>          ..
31/03/2026  18:00    <DIR>          Desktop
31/03/2026  18:00    <DIR>          Documents
30/03/2026  22:10    <DIR>          AppData
```

- `.` representa el directorio actual
- `..` representa el directorio padre
- `<DIR>` indica que es una carpeta

En HTML o Markdown tecnico es frecuente ver `&lt;DIR&gt;` para que no se interprete como etiqueta.

---

## Rutas absolutas y rutas relativas

Entender esto te ahorra una enorme cantidad de errores.

### Ruta absoluta

Una ruta absoluta empieza desde la unidad raiz:

```cmd
C:\Users\usuario\Documents\notas.txt
```

No depende de donde estes situado. Si la ruta existe, el comando funcionara desde cualquier directorio.

### Ruta relativa

Una ruta relativa se interpreta **desde el directorio actual**:

```cmd
Documents
..\Desktop
.\scripts
```

Si estas en:

```cmd
C:\Users\usuario
```

entonces:

```cmd
cd Documents
```

te llevara a:

```cmd
C:\Users\usuario\Documents
```

Y si estas en:

```cmd
C:\Users\usuario\Documents\proyecto
```

entonces:

```cmd
cd ..\..
```

te llevara a:

```cmd
C:\Users\usuario
```

### Simbolos que debes dominar

```cmd
.      :: directorio actual
..     :: directorio padre
\      :: raiz de la unidad actual
```

Ejemplos:

```cmd
type .\README.txt
cd ..\Downloads
dir ..\Desktop
```

---

## tree - ver la estructura entera

`tree` dibuja un arbol de directorios. Es uno de los mejores comandos para **entender visualmente** una estructura que no conoces.

```cmd
tree
tree C:\Users\usuario\Desktop
tree /f
tree /a
```

### Opciones utiles

```cmd
tree /f   :: incluye archivos, no solo carpetas
tree /a   :: usa caracteres ASCII simples
```

Ejemplo:

```cmd
tree C:\Labs /f

Folder PATH listing
C:\LABS
+---evidencias
|       hosts.txt
+---scripts
|       enum.bat
\---logs
        access.log
        system.log
```

`tree /a` es muy practico cuando vas a copiar la salida a notas, tickets o informes.

---

## Archivos ocultos, del sistema y rutas clave

Una parte importante de aprender `dir /a` es entender que Windows esconde muchos elementos relevantes por defecto.

Rutas especialmente importantes:

```cmd
C:\Users
C:\Users\usuario\AppData
C:\ProgramData
C:\Windows\Temp
C:\Temp
```

Comandos utiles:

```cmd
dir /a
dir /ah
dir /as
dir C:\Users /ad
dir C:\Users\usuario\AppData /a
```

Donde:

- `/ah` muestra ocultos
- `/as` muestra elementos de sistema
- `/ad` muestra solo directorios

### Por que importa

Muchos artefactos interesantes no estan a simple vista:

- caches
- configuraciones de aplicaciones
- perfiles de navegador
- logs
- scripts auxiliares
- archivos temporales

Si te limitas a `dir` sin `/a`, te pierdes una parte del mapa.

---

## Secuencia practica de navegacion

Esta es una secuencia realista al abrir una shell en Windows y querer orientarte:

```cmd
cd
dir
dir /a
cd Desktop
dir /b
cd ..\Documents
dir /o:-d
tree /f
dir /s /b *.txt
```

Que estas haciendo en realidad:

1. averiguar donde estas
2. listar contenido visible
3. listar tambien ocultos
4. moverte a una carpeta conocida
5. simplificar salida con formato basico
6. moverte con ruta relativa
7. ordenar por fecha para ver cambios recientes
8. visualizar estructura
9. buscar archivos concretos de forma recursiva

---

## Variables de entorno que ayudan a navegar

En Windows hay rutas largas que conviene no escribir completas cada vez. Para eso estan las variables de entorno:

```cmd
echo %USERPROFILE%
echo %HOMEPATH%
echo %TEMP%
echo %APPDATA%
echo %ProgramFiles%
```

Ejemplos practicos:

```cmd
cd %USERPROFILE%
dir %TEMP%
dir %APPDATA% /a
```

No sustituyen el conocimiento de rutas, pero aceleran mucho el trabajo diario.

---

## Relevancia en ciberseguridad

La navegacion y el sistema de archivos aparecen en casi cualquier fase de trabajo ofensivo o defensivo.

| Situacion | Comando util |
|-----------|--------------|
| Ver perfiles de usuario | `dir C:\Users /ad` |
| Buscar archivos interesantes | `dir /s /b *pass* *cred* *.config` |
| Revisar AppData | `dir %APPDATA% /a` |
| Entender estructura de una carpeta | `tree /f` |
| Ver que cambio recientemente | `dir /o:-d` |
| Enumerar ocultos | `dir /a` |

Ejemplos tipicos:

```cmd
dir C:\Users /ad
dir C:\Users\Public /a
dir %APPDATA% /s /b *.log
dir C:\ /s /b unattend.xml
tree C:\Users\usuario\Desktop /f
```

En analisis defensivo o forense, estas tecnicas sirven para:

- localizar persistencia burda en carpetas de usuario
- revisar logs o artefactos dejados por malware
- encontrar scripts `.bat`, `.cmd`, `.ps1`
- detectar ficheros ocultos en ubicaciones poco normales

---

## Errores tipicos al empezar

**1. Confundir `\` con `/`**  
Windows usa barra invertida en las rutas tradicionales de CMD.

**2. Olvidar `cd /d` al cambiar de unidad**  
Muy comun cuando pasas de `C:` a `D:`.

**3. Usar solo `dir` y no `dir /a`**  
Eso oculta parte del panorama.

**4. No distinguir entre ruta absoluta y relativa**  
Muchos comandos fallan solo porque estas en otro directorio del que creias.

**5. No aprovechar `dir /b /s`**  
Es una forma rapida de buscar rutas completas sin meterte aun en PowerShell.

---

## Resumen

- Windows organiza su sistema de archivos por **unidades** como `C:\` y `D:\`
- `cd` sirve para **moverte** y tambien para **ver tu directorio actual**
- `dir` lista contenido y gana mucha potencia con `/a`, `/b`, `/s` y `/o`
- Las **rutas absolutas** no dependen de donde estes; las **relativas** si
- `.` representa el directorio actual y `..` el directorio padre
- `tree` permite visualizar una estructura completa, especialmente con `/f`
- `dir /a` es imprescindible para no perder archivos ocultos o del sistema
- Estas tecnicas son basicas tanto para administracion como para ciberseguridad

En el siguiente post seguiremos con **gestion de archivos en Windows**: `copy`, `move`, `del`, `mkdir`, `rmdir`, `ren` y `type`.
