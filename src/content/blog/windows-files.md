---
title: 'Gestion de archivos en Windows - copy, move, del, mkdir, rmdir, ren y type'
description: 'Aprende a copiar, mover, borrar, crear, renombrar y leer archivos desde CMD con seguridad y criterio tecnico.'
pubDate: 'Apr 01 2026 12:00:00'
heroImage: '../../assets/windows-files-hero.svg'
category: 'windows'
---

Navegar por el sistema de archivos es solo el principio. El trabajo real empieza cuando necesitas **crear carpetas, copiar evidencias, mover archivos, renombrar scripts, borrar residuos o leer el contenido de un fichero** sin salir de la terminal.

En Windows CMD, los comandos base para eso son:

- `copy`
- `move`
- `del`
- `mkdir`
- `rmdir`
- `ren`
- `type`

Parecen simples, pero juntos cubren una gran parte del trabajo diario en administracion, soporte, analisis y ciberseguridad. Lo importante no es solo saber que hacen, sino **entender sus riesgos y sus patrones de uso correctos**.

---

## mkdir - crear directorios

`mkdir` crea carpetas. Tambien puedes usar su alias corto `md`.

```cmd
mkdir laboratorio
mkdir C:\Temp\logs
md evidencias
mkdir proyecto\docs
```

Si la ruta intermedia no existe, CMD puede crear varios niveles de una vez:

```cmd
mkdir C:\Labs\caso01\evidencias
mkdir C:\Labs\caso01\scripts
mkdir C:\Labs\caso01\logs
```

Usos tipicos:

- preparar una estructura de trabajo
- separar evidencias, scripts y resultados
- crear directorios temporales antes de copiar datos

En ciberseguridad, una buena estructura evita mezclar artefactos y perder trazabilidad.

---

## copy - copiar archivos

`copy` copia uno o varios archivos a otro destino. A diferencia de `move`, el original permanece intacto.

```cmd
copy notas.txt backup.txt
copy notas.txt C:\Temp\
copy *.log C:\Temp\logs\
copy C:\Windows\win.ini C:\Temp\
```

### Casos practicos

```cmd
copy hosts.txt evidencias\hosts_backup.txt
copy *.txt C:\Users\usuario\Desktop\
copy reporte.txt \\servidor\share\
```

### Ideas clave

- `copy` trabaja principalmente con archivos
- si usas comodines como `*.txt`, copia varios archivos
- si el destino es un directorio, mantiene el nombre original
- si el destino es un nombre de archivo, copia y renombra a la vez

En entornos reales, `copy` se usa para:

- hacer backups rapidos antes de tocar configuraciones
- duplicar logs para analizarlos sin alterar el original
- exfiltrar o mover evidencias a otro directorio de trabajo

---

## move - mover archivos o carpetas

`move` cambia un archivo o directorio de ubicacion. Tambien puede servir para renombrar si el destino cambia el nombre.

```cmd
move archivo.txt C:\Temp\
move archivo.txt C:\Temp\nuevo_nombre.txt
move carpeta C:\Backups\
move *.log logs\
```

### Diferencia esencial respecto a copy

`copy` deja el original.  
`move` lo quita de su ubicacion inicial.

Eso significa que `move` es mas rapido para reorganizar, pero tambien mas delicado: si mueves algo critico y luego no recuerdas adonde fue, te puedes complicar bastante.

### Uso seguro

```cmd
dir archivo.txt
move archivo.txt respaldo\
dir respaldo\
```

Comprobar antes y despues es una buena costumbre, sobre todo cuando trabajas con nombres parecidos o carpetas llenas de ficheros.

---

## ren - renombrar archivos y carpetas

`ren` cambia el nombre de un archivo o directorio sin moverlo de ruta.

```cmd
ren informe.txt informe_old.txt
ren script.bat script_legacy.bat
ren logs logs_2026
```

Tambien admite comodines en ciertos escenarios:

```cmd
ren *.txt *.bak
```

Esto puede ser util, pero hay que usarlo con mucho cuidado. Un renombrado masivo mal pensado genera caos muy rapido.

### Cuando usar ren en vez de move

Usa `ren` si:

- solo cambia el nombre
- el archivo sigue en la misma carpeta
- quieres dejar clara la intencion de "renombrar" y no de "mover"

Usa `move` si:

- cambia la ubicacion
- o cambian ubicacion y nombre al mismo tiempo

---

## del - borrar archivos

`del` elimina archivos. Es el equivalente mental de `rm` para archivos en CMD.

```cmd
del archivo.txt
del *.tmp
del C:\Temp\*.log
```

### Muy importante

`del` **no es un juego**. Si borras desde CMD, no estas usando una interfaz amable con papelera y boton de deshacer. En la practica, debes asumir que el borrado es serio y potencialmente irreversible.

Antes de usarlo con comodines:

```cmd
dir *.tmp
del *.tmp
```

Ese patron evita muchos accidentes.

### Flags comunes

```cmd
del /q archivo.txt
del /f archivo.txt
del /s *.log
```

- `/q` elimina en modo silencioso
- `/f` fuerza el borrado de archivos de solo lectura
- `/s` elimina archivos coincidentes en subdirectorios

En ciberseguridad, `del` aparece en dos contextos opuestos:

- administradores limpiando temporales o artefactos
- atacantes intentando borrar huellas basicas

---

## rmdir - borrar directorios

`rmdir` elimina directorios. Su alias corto es `rd`.

```cmd
rmdir carpeta_vacia
rd carpeta_vacia
```

Si la carpeta contiene archivos o subdirectorios, necesitas la opcion recursiva:

```cmd
rmdir /s carpeta
rmdir /s /q carpeta
```

### Diferencia critica

- `del` borra archivos
- `rmdir` borra directorios

No mezcles ambos mentalmente. Es una fuente muy comun de errores al empezar.

### Uso peligroso

```cmd
rmdir /s /q C:\Temp\viejo_laboratorio
```

Ese comando elimina toda la estructura sin pedir confirmacion. Solo debe usarse cuando estas completamente seguro del objetivo.

---

## type - leer el contenido de un archivo

`type` muestra el contenido de un archivo de texto directamente en la consola.

```cmd
type notas.txt
type C:\Windows\System32\drivers\etc\hosts
type script.bat
```

Es util para:

- revisar scripts `.bat` o `.cmd`
- ver configuraciones pequenas
- inspeccionar logs cortos
- comprobar rapido un archivo sin abrir Notepad

### Limitaciones

`type` no pagina ni filtra bien archivos largos. Si el fichero es enorme, la salida te pasara por delante de golpe.

Aun asi, para inspecciones rapidas es perfecto.

---

## Flujos de trabajo reales

### 1. Preparar una carpeta de analisis

```cmd
mkdir C:\Labs\caso02
mkdir C:\Labs\caso02\evidencias
mkdir C:\Labs\caso02\logs
mkdir C:\Labs\caso02\scripts
```

### 2. Hacer copia antes de tocar un fichero

```cmd
copy C:\Windows\System32\drivers\etc\hosts C:\Labs\caso02\evidencias\hosts.bak
type C:\Labs\caso02\evidencias\hosts.bak
```

### 3. Reorganizar resultados

```cmd
move *.log logs\
ren resultado.txt resultado_final.txt
```

### 4. Limpiar temporales con prudencia

```cmd
dir *.tmp
del *.tmp
```

### 5. Eliminar una estructura de pruebas al terminar

```cmd
rmdir /s /q C:\Labs\caso02\temp
```

---

## Relevancia en ciberseguridad

| Comando | Uso comun en seguridad |
|---------|------------------------|
| `copy` | backup de evidencias, copia de logs, duplicado de configuraciones |
| `move` | reorganizar artefactos o apartar ficheros sospechosos |
| `ren` | renombrar payloads, scripts o resultados |
| `del` | limpieza de temporales o borrado de huellas basicas |
| `rmdir /s /q` | eliminar directorios completos con rapidez |
| `type` | leer scripts, hosts, logs pequenos y configuraciones |
| `mkdir` | preparar laboratorios y separar evidencias |

Ejemplos muy reales:

```cmd
copy C:\Windows\System32\drivers\etc\hosts C:\Temp\
type C:\Temp\hosts
move suspicious.bat C:\Temp\cuarentena\
ren loader.exe loader_old.exe
del *.tmp
rmdir /s /q C:\Temp\old_drop
```

Para Blue Team y forense, estos comandos permiten recopilar y aislar artefactos. Para un atacante, los mismos comandos sirven para preparar persistencia, reorganizar herramientas o eliminar residuos. Por eso conviene conocerlos desde ambos enfoques.

---

## Errores tipicos al empezar

**1. Usar `del` sin listar antes**  
Especialmente con `*.tmp`, `*.log` o comodines mas amplios.

**2. Confundir `del` con `rmdir`**  
Uno borra archivos; el otro, directorios.

**3. Usar `move` cuando querias `copy`**  
Eso hace desaparecer el original.

**4. Renombrar en masa sin pensar**  
`ren *.txt *.bak` puede romper tu orden de trabajo si no tenias claro el alcance.

**5. Usar `rmdir /s /q` sobre la ruta equivocada**  
Es un comando muy potente y muy poco indulgente.

---

## Resumen

- `mkdir` crea carpetas
- `copy` duplica archivos
- `move` mueve o cambia nombre
- `ren` renombra sin mover
- `del` borra archivos
- `rmdir` borra directorios
- `type` muestra el contenido de archivos de texto

Dominar estos comandos significa pasar de "ver el sistema de archivos" a **operar sobre el** con criterio y precision.

En el siguiente post iremos a **informacion del sistema en Windows**: `whoami`, `hostname`, `systeminfo`, `ipconfig` y los primeros comandos de enumeracion reales.
