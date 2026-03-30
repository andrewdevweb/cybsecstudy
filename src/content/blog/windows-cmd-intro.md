---
title: 'Introducción a la CLI de Windows — CMD, PowerShell y por qué importan'
description: 'Qué es el Símbolo del sistema de Windows, diferencias con PowerShell y la Terminal, historia y relevancia en ciberseguridad.'
pubDate: 'Mar 29 2026 09:00:00'
heroImage: '../../assets/windows-cli-hero.svg'
category: 'fundamentos'
---

Cuando la mayoría de personas piensa en "hackear Windows", imagina herramientas sofisticadas y exploits complejos. La realidad es que muchos ataques reales — y mucho trabajo legítimo de administración — se hacen con las herramientas que vienen instaladas por defecto: CMD y PowerShell.

La línea de comandos de Windows no es solo un vestigio del pasado. Es una herramienta activa, potente y omnipresente en entornos corporativos. Saber usarla bien es tan fundamental como saber usarla para atacar o defender.

---

## Un poco de historia — de MS-DOS a la Terminal moderna

**1981 — MS-DOS**: Microsoft lanza el sistema operativo de línea de comandos que dominó los PCs durante una década. Todo se hacía escribiendo comandos.

**1987 — COMMAND.COM**: El intérprete de comandos de DOS, ancestro directo de CMD.

**1993 — Windows NT y CMD.EXE**: Con Windows NT llegó `cmd.exe` — un intérprete más potente que COMMAND.COM, diseñado para sistemas de 32 bits. Es el que sigue existiendo hoy.

**2006 — PowerShell 1.0**: Microsoft lanza PowerShell, una shell completamente nueva basada en .NET. No trabaja con texto sino con **objetos** — un cambio filosófico radical.

**2019 — Windows Terminal**: Microsoft lanza Windows Terminal, una aplicación moderna que unifica CMD, PowerShell y WSL (Windows Subsystem for Linux) en pestañas.

**Hoy**: Los tres coexisten. CMD para compatibilidad y tareas simples, PowerShell para administración avanzada y scripting, Windows Terminal como interfaz unificada.

---

## CMD vs PowerShell vs Windows Terminal

Es una confusión muy común. Aclaremos:

| | CMD | PowerShell | Windows Terminal |
|-|-----|------------|-----------------|
| **¿Qué es?** | Intérprete de comandos | Shell + lenguaje de scripting | Emulador de terminal (contenedor) |
| **Trabaja con** | Texto | Objetos .NET | Lo que sea (CMD, PS, WSL...) |
| **Scripting** | .bat / .cmd (básico) | .ps1 (potente) | — |
| **Disponible en** | Windows XP+ | Windows 7+ | Windows 10+ |
| **Uso típico** | Comandos rápidos, legado | Administración, automatización | Interfaz moderna para ambos |

### La analogía

Piensa en Windows Terminal como el **edificio** (la ventana donde escribes), CMD como el **idioma básico** que el edificio puede hablar, y PowerShell como el **idioma avanzado** con gramática completa.

---

## ¿Por qué aprender CMD si existe PowerShell?

Buena pregunta. Tres razones:

**1. Está en todas partes.** CMD existe en todos los Windows desde XP. PowerShell no siempre está disponible — versiones antiguas, sistemas restringidos, o configuraciones de seguridad que lo bloquean.

**2. Es la base.** Muchos comandos de CMD funcionan también en PowerShell. Aprender CMD primero da una base sólida antes de pasar a la complejidad de PowerShell.

**3. Es el punto de entrada de muchos ataques.** Un atacante que obtiene ejecución de código remoto en Windows frecuentemente termina con un CMD shell. Entender CMD desde dentro es entender cómo piensan los atacantes.

---

## Abriendo la terminal — cuatro formas

```
1. Win + R → escribir "cmd" → Enter
2. Barra de búsqueda → escribir "cmd" → Enter
3. Win + X → "Símbolo del sistema" o "Terminal"
4. En cualquier carpeta: Shift + clic derecho → "Abrir ventana de comandos aquí"
```

### CMD como administrador

Muchos comandos requieren privilegios elevados. Para abrir CMD como administrador:
```
Búsqueda → "cmd" → clic derecho → "Ejecutar como administrador"
```

El prompt cambia — aparece `Administrator:` en la barra de título y la ruta es `C:\Windows\System32`:

```
# CMD normal:
C:\Users\usuario>

# CMD como administrador:
C:\Windows\System32>
```

> ⚠️ En ciberseguridad, conseguir una CMD con privilegios de administrador (o SYSTEM) es el objetivo de la escalada de privilegios en Windows. Si ves un prompt con `C:\Windows\System32>` tras explotar un sistema, has llegado.

---

## Anatomía del prompt

```
C:\Users\usuario>
│  │             │
│  │             └── > indica que el prompt espera un comando
│  └────────────── ruta del directorio actual
└─────────────── letra de unidad (C: es el disco principal)
```

A diferencia de Linux donde todo parte de `/`, Windows usa **letras de unidad**: `C:` es el disco principal, `D:` podría ser un segundo disco o DVD, `Z:` una unidad de red, etc.

---

## Los primeros comandos — orientarse en el sistema

```cmd
cd              :: mostrar el directorio actual (como pwd en Linux)
dir             :: listar contenido del directorio actual (como ls)
dir /a          :: incluir archivos y carpetas ocultos
cls             :: limpiar la pantalla (como clear en Linux)
exit            :: cerrar la terminal
help            :: mostrar lista de comandos disponibles
help dir        :: ayuda sobre un comando específico
dir /?          :: otra forma de obtener ayuda (más completa)
```

### Navegación básica

```cmd
cd Documentos           :: entrar en la carpeta Documentos
cd ..                   :: subir un nivel
cd ..\..                :: subir dos niveles
cd \                    :: ir a la raíz de la unidad (C:\)
cd /d D:\               :: cambiar a otra unidad (D:)
D:                      :: cambiar a la unidad D: (forma corta)
```

---

## Diferencias clave con Linux

| Concepto | Linux | Windows CMD |
|---------|-------|-------------|
| Raíz del sistema | `/` | `C:\` |
| Separador de ruta | `/` | `\` |
| Ver directorio actual | `pwd` | `cd` (sin argumentos) |
| Listar archivos | `ls` | `dir` |
| Borrar pantalla | `clear` | `cls` |
| Ver contenido de archivo | `cat` | `type` |
| Mover/renombrar | `mv` | `move` / `ren` |
| Eliminar archivo | `rm` | `del` |
| Crear directorio | `mkdir` | `mkdir` / `md` |
| Ayuda de comando | `man comando` | `comando /?` |
| Case sensitive | Sí | No |
| Variables de entorno | `$VARIABLE` | `%VARIABLE%` |

**Case insensitive**: en Windows, `DIR`, `dir` y `Dir` son el mismo comando. Los nombres de archivo tampoco distinguen mayúsculas — `archivo.txt` y `ARCHIVO.TXT` son el mismo archivo.

---

## Relevancia en ciberseguridad

CMD es protagonista en múltiples escenarios de seguridad:

| Escenario | Cómo aparece CMD |
|-----------|-----------------|
| **Post-explotación** | Reverse shell en CMD tras explotar una vulnerabilidad |
| **Living off the Land (LotL)** | Usar solo herramientas del sistema para evitar detección |
| **Análisis forense** | Recopilar evidencias con comandos del sistema |
| **Escalada de privilegios** | Buscar misconfigurations con comandos del sistema |
| **Lateral movement** | Moverse entre máquinas con net use, psexec |
| **Persistencia** | Crear tareas programadas con schtasks |

```cmd
:: Comandos típicos en post-explotación
whoami                          :: ¿quién soy?
whoami /priv                    :: ¿qué privilegios tengo?
net user                        :: usuarios del sistema
net localgroup administrators   :: administradores locales
systeminfo                      :: info completa del sistema
ipconfig /all                   :: configuración de red
netstat -ano                    :: conexiones de red activas
```

---

## Resumen

- **CMD.EXE** es el intérprete de comandos de Windows, heredero de MS-DOS, presente en todos los Windows modernos
- **PowerShell** es una shell más potente basada en .NET que trabaja con objetos
- **Windows Terminal** es solo el contenedor visual moderno — no es un intérprete en sí
- El prompt muestra la **unidad + ruta actual** (`C:\Users\usuario>`)
- Los comandos básicos de orientación: `cd`, `dir`, `dir /a`, `cls`, `help`
- CMD es **case insensitive** — mayúsculas y minúsculas no importan
- En ciberseguridad, CMD es el punto de entrada más común tras comprometer un sistema Windows

En el siguiente post: **Navegación y sistema de archivos en Windows** — dir en profundidad, rutas absolutas y relativas, y cómo encontrar archivos desde la terminal.
