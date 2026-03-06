---
title: 'Modelo OSI — Las 7 Capas'
description: 'Guía completa del Modelo de Interconexión de Sistemas Abiertos: capas, protocolos, encapsulación y dispositivos.'
pubDate: 'Mar 06 2025'
heroImage: '../../assets/osi-hero.svg'
---

El Modelo OSI (**Open Systems Interconnection**) es un framework estándar que define cómo los dispositivos en red envían, reciben e interpretan datos. Fue desarrollado por la ISO en 1984 y sigue siendo el modelo de referencia más usado para entender las redes.

La principal ventaja del modelo OSI es que permite que dispositivos con diferentes funciones y diseños puedan comunicarse entre sí usando un lenguaje común.

## ¿Por qué 7 capas?

Cada capa tiene una responsabilidad específica y solo se comunica con la capa inmediatamente superior e inferior. Esto permite que cada capa sea desarrollada, actualizada o reemplazada de forma independiente sin afectar al resto.

Los datos viajan **de la Capa 7 a la Capa 1** al enviar, y **de la Capa 1 a la Capa 7** al recibir.

## Las 7 Capas

| Capa | Nombre | PDU | Dispositivo |
|------|--------|-----|-------------|
| 7 | Aplicación | Datos | — |
| 6 | Presentación | Datos | — |
| 5 | Sesión | Datos | — |
| 4 | Transporte | Segmento | — |
| 3 | Red | Paquete | Router |
| 2 | Enlace de Datos | Trama | Switch |
| 1 | Física | Bit | Hub, Cable |

---

## Capa 7 — Aplicación

Es la capa más cercana al usuario. Proporciona interfaces de red para las aplicaciones, como navegadores web, clientes de email o aplicaciones de transferencia de archivos.

**No es la aplicación en sí**, sino la interfaz que permite a las aplicaciones acceder a los servicios de red.

**Protocolos:** HTTP, HTTPS, FTP, SMTP, DNS, POP3, IMAP, SSH, Telnet

**Ejemplos de uso:**
- Cuando abres un navegador y visitas una web → HTTP/HTTPS
- Cuando envías un email → SMTP
- Cuando resuelves un nombre de dominio → DNS

---

## Capa 6 — Presentación

Actúa como el **traductor** de la red. Se encarga de que los datos enviados por una aplicación puedan ser leídos por la aplicación destino, independientemente del formato o sistema operativo.

**Funciones principales:**
- **Traducción:** convierte formatos de datos (ASCII, Unicode, EBCDIC)
- **Cifrado/Descifrado:** protege los datos en tránsito (SSL/TLS)
- **Compresión:** reduce el tamaño de los datos para optimizar la transmisión

**Protocolos y estándares:** SSL, TLS, JPEG, MPEG, GIF, ASCII, Unicode

---

## Capa 5 — Sesión

Gestiona el establecimiento, mantenimiento y terminación de **sesiones** entre dispositivos. Una sesión es una conexión semipermanente entre dos dispositivos.

**Funciones principales:**
- Abrir, mantener y cerrar sesiones
- Control de diálogo: determina quién puede transmitir y cuándo (simplex, half-duplex, full-duplex)
- Puntos de control: si una transferencia falla, puede reanudarse desde el último punto guardado

**Protocolos:** NetBIOS, RPC, PPTP, L2TP

---

## Capa 4 — Transporte

Proporciona comunicación **extremo a extremo** entre aplicaciones. Es responsable de que los datos lleguen completos, en orden y sin errores.

**TCP vs UDP:**

| Característica | TCP | UDP |
|---------------|-----|-----|
| Confiabilidad | Alta (acuses de recibo) | Baja (sin verificación) |
| Velocidad | Más lento | Más rápido |
| Orden | Garantizado | No garantizado |
| Uso | Web, email, FTP | Streaming, VoIP, DNS |

**Funciones principales:**
- **Segmentación:** divide los datos en segmentos más pequeños
- **Control de flujo:** evita que el emisor sature al receptor
- **Multiplexación:** usa puertos para distinguir diferentes conexiones (HTTP = 80, HTTPS = 443, SSH = 22)

**Protocolos:** TCP, UDP, SCTP

---

## Capa 3 — Red

Gestiona el **direccionamiento lógico** y el **enrutamiento** de paquetes entre redes distintas. El dispositivo principal de esta capa es el **router**.

**Funciones principales:**
- Direccionamiento lógico mediante direcciones IP (IPv4 e IPv6)
- Determinación de la mejor ruta para los paquetes
- Fragmentación de paquetes si son demasiado grandes

**Protocolos:** IP, IPv6, ICMP, OSPF, BGP, RIP, ARP

**Dato importante:** ARP (Address Resolution Protocol) traduce direcciones IP a direcciones MAC, haciendo de puente entre la Capa 3 y la Capa 2.

---

## Capa 2 — Enlace de Datos

Proporciona transferencia de datos confiable entre **nodos directamente conectados** en la misma red. El dispositivo principal es el **switch**.

Se divide en dos subcapas:
- **LLC (Logical Link Control):** controla el flujo y los errores
- **MAC (Media Access Control):** gestiona el acceso al medio y usa direcciones MAC

**Direcciones MAC:** identificadores únicos de 48 bits (6 bytes) asignados a cada tarjeta de red. Ejemplo: `00:1A:2B:3C:4D:5E`

**Funciones principales:**
- Encapsulación de paquetes en tramas
- Detección y corrección de errores mediante FCS (Frame Check Sequence)
- Control de acceso al medio

**Protocolos:** Ethernet, Wi-Fi (802.11), PPP, HDLC, Frame Relay

---

## Capa 1 — Física

Define las características **eléctricas, mecánicas y funcionales** de la transmisión de bits a través del medio físico. Trabaja con señales eléctricas, ópticas o de radio.

**Funciones principales:**
- Transmisión y recepción de bits (0s y 1s)
- Definición de voltajes, frecuencias y tiempos
- Especificaciones de conectores, cables y medios inalámbricos
- Sincronización de bits

**Medios de transmisión:**
- **Cable de cobre:** Ethernet (UTP, STP), coaxial
- **Fibra óptica:** transmisión mediante luz, mayor velocidad y distancia
- **Inalámbrico:** Wi-Fi, Bluetooth, 4G/5G

**Estándares:** USB, Bluetooth, DSL, ISDN, IEEE 802.3

---

## Encapsulación y Desencapsulación

La **encapsulación** es el proceso por el cual cada capa añade su propia información (cabecera y/o trailer) a los datos antes de pasarlos a la capa inferior.

```
Capa 7 → [DATOS]
Capa 6 → [H6][DATOS]
Capa 5 → [H5][H6][DATOS]
Capa 4 → [H4][H5][H6][DATOS]           ← Segmento
Capa 3 → [H3][H4][H5][H6][DATOS]       ← Paquete
Capa 2 → [H2][H3][H4][H5][H6][DATOS][FCS] ← Trama
Capa 1 → 010101001010110...              ← Bits
```

La **desencapsulación** es el proceso inverso: al recibir datos, cada capa elimina su cabecera y pasa el resto a la capa superior.

---

## Mnemotécnico para recordar las capas

De arriba a abajo (7→1):
> **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing

| Letra | Inglés | Español |
|-------|--------|---------|
| A | Application | Aplicación |
| P | Presentation | Presentación |
| S | Session | Sesión |
| T | Transport | Transporte |
| N | Network | Red |
| D | Data Link | Enlace de Datos |
| P | Physical | Física |

---

## Modelo OSI vs Modelo TCP/IP

El **Modelo TCP/IP** es el modelo que realmente se usa en internet hoy en día. Tiene 4 capas en lugar de 7:

| TCP/IP | OSI equivalente |
|--------|----------------|
| Aplicación | Capas 7, 6, 5 |
| Transporte | Capa 4 |
| Internet | Capa 3 |
| Acceso a la red | Capas 2, 1 |

El Modelo OSI sigue siendo el estándar de referencia para **entender y diagnosticar redes**, mientras que TCP/IP es el que se implementa en la práctica.
