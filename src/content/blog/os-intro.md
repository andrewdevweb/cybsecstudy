---
title: 'Sistemas Operativos — Introducción'
description: 'Qué es un SO, capas de privilegios, kernel vs user space, funciones principales, GUI vs CLI y el panorama de sistemas operativos reales.'
pubDate: 'Mar 20 2026 10:00:00'
heroImage: '../../assets/os-hero.svg'
category: 'fundamentos'
---

Tu ordenador tiene miles de componentes trabajando al mismo tiempo — CPU ejecutando instrucciones, RAM almacenando datos, disco leyendo archivos, tarjeta de red enviando paquetes. Sin algo que coordine todo esto, sería el caos. Ese "algo" es el **sistema operativo**.

El SO se sitúa entre el usuario, las aplicaciones y el hardware físico, actuando como el administrador invisible que mantiene toda la máquina funcionando como un sistema unificado.

---

## La analogía del aeropuerto

Imagina tu ordenador como un aeropuerto:

| Aeropuerto | Ordenador |
|-----------|-----------|
| Pistas, radar, infraestructura | Hardware — CPU, RAM, almacenamiento |
| Aerolíneas y pasajeros | Aplicaciones — navegador, juegos, editor |
| Sistema de control de tráfico | Sistema operativo — coordina todo |

El sistema de control de tráfico no vuela ningún avión — pero sin él, ninguno podría despegar ni aterrizar de forma segura.

---

## Capas de privilegios — Kernel Space vs User Space

Un concepto fundamental en seguridad y en el diseño de sistemas operativos:

### Kernel Space
El núcleo privilegiado del SO. El **kernel** se ejecuta aquí con acceso directo y sin restricciones a todo el hardware: CPU, RAM, almacenamiento, red. Es la torre de control del aeropuerto — área de acceso restringido donde solo trabajan los controladores autorizados.

### User Space
Donde se ejecutan todas las aplicaciones normales. Las apps tienen **prohibido** acceder directamente al hardware. Si necesitan abrir un archivo, reproducir audio o conectarse a WiFi, deben hacer una **llamada al sistema** (syscall) — una petición formal al kernel para que actúe en su nombre.

```
[Aplicación] → syscall → [Kernel] → Hardware
```

**¿Por qué esta separación?** Seguridad y estabilidad. Si una aplicación tiene un bug, no puede corromper el kernel ni afectar a otras apps.

> ⚠️ En ciberseguridad, una **escalada de privilegios** es cuando un atacante consigue pasar de User Space a Kernel Space — el control total del sistema. Es uno de los objetivos más codiciados en pentesting.

---

## Funciones principales del SO

| Función | Qué hace | Ejemplo |
|---------|---------|---------|
| **Gestión de procesos** | Crea, planifica y termina programas. Decide cuánta CPU recibe cada proceso | Tener Chrome, Spotify y VS Code abiertos sin que el sistema se congele |
| **Gestión de memoria** | Asigna RAM a procesos, los aísla entre sí, usa memoria virtual cuando la RAM se agota | Cada pestaña del navegador tiene su propia memoria — un crash no afecta a las demás |
| **Sistema de archivos** | Organiza archivos en directorios, gestiona permisos, nombres, rutas y metadatos | Crear una carpeta, guardar un archivo como solo lectura |
| **Gestión de usuarios** | Múltiples cuentas, autenticación, permisos de acceso | Tu contraseña de inicio de sesión — tus archivos son inaccesibles para otras cuentas |
| **Gestión de dispositivos** | Carga drivers, abstrae el hardware para que las apps no necesiten saber los detalles | Conectar un USB y que funcione inmediatamente |
| **Seguridad** | Autenticación, permisos, aislamiento de procesos, protección del sistema | UAC en Windows, sudo en Linux |

---

## GUI vs CLI — Dos formas de hablar con el SO

### GUI — Interfaz Gráfica de Usuario
Lo que la mayoría conoce: iconos, ventanas, menús. Intuitivo, visual, fácil de aprender. Es como usar una app de navegación con mapa visual.

### CLI — Interfaz de Línea de Comandos
Comandos de texto directos al sistema. Más potente, más preciso, más rápido para tareas avanzadas.

```bash
# GUI: clic en carpeta → clic en subcarpeta → ver archivos
# CLI: mismo resultado en un comando
ls /home/usuario/documentos
```

**En ciberseguridad, la CLI es fundamental.** La mayoría de herramientas de seguridad (nmap, metasploit, burpsuite) se usan desde la terminal. Los servidores Linux no tienen GUI. Kali Linux se controla principalmente por CLI.

| CLI | Sistema |
|-----|---------|
| **Bash / Zsh** | Linux y macOS |
| **PowerShell** | Windows (también en Linux) |
| **CMD** | Windows legacy |

---

## Tipos de sistemas operativos

| Tipo | Uso principal | Características clave |
|------|--------------|----------------------|
| **Escritorio** | PCs, trabajo diario, gaming | GUI avanzada, multitarea, centrado en usuario |
| **Servidor** | Hosting, bases de datos, cloud | Sin GUI, máximo uptime, multiusuario, acceso remoto |
| **Móvil** | Smartphones y tablets | UI táctil, bajo consumo, apps aisladas |
| **Embebido** | IoT, coches, routers | Mínimo tamaño, propósito único |
| **Virtual/Cloud** | VMs, contenedores | Ligero, escalable, despliegue rápido |

---

## El panorama real — Familias de SO

### Windows
El SO más usado en escritorio. Windows 11 actual, Windows Server 2022/2025 para empresas. Mayor superficie de ataque del mundo — más malware apunta a Windows que a cualquier otro SO.

### Linux
No es un SO — es una **familia de distribuciones** que comparten el mismo kernel. Domina los servidores — más del **90% de los servidores web** corren Linux.

| Distribución | Uso |
|-------------|-----|
| Ubuntu | Escritorio y servidor — la más popular |
| Debian | Base de Ubuntu, muy estable |
| **Kali Linux** | Pentesting y ciberseguridad |
| Ubuntu Server / Rocky | Servidores web |
| Alpine Linux | Contenedores Docker |

### macOS
Basado en Unix. Muy popular entre desarrolladores. Versiones: Sonoma (14), Sequoia (15).

### Android e iOS
Los dos SO móviles dominantes. Android (basado en Linux) lidera en cuota global. iOS es exclusivo de Apple, más cerrado y con un modelo de seguridad más estricto.

### RTOS — Sistemas en tiempo real
Para aplicaciones donde **el tiempo de respuesta es crítico**: aviones, coches autónomos, marcapasos. Ejemplos: FreeRTOS, VxWorks, QNX.

---

## Relevancia en ciberseguridad

| Concepto | Importancia |
|---------|------------|
| **Kernel Space** | Objetivo de rootkits — malware a nivel kernel es casi indetectable |
| **Syscalls** | Vector de ataque — hooking de syscalls para interceptar operaciones |
| **Permisos** | Misconfiguration = escalada de privilegios |
| **Procesos** | DLL injection, process hollowing — inyección en procesos legítimos |
| **Linux servers** | La mayoría de servidores son Linux — fundamental para pentesting web |
| **CLI** | Herramientas de seguridad, scripting, automatización — todo requiere terminal |
