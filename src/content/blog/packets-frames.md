---
title: 'Packets & Frames — TCP, UDP y Puertos'
description: 'Qué son los paquetes y tramas, cómo funcionan TCP y UDP, el three-way handshake y los puertos más importantes en ciberseguridad.'
pubDate: 'Mar 06 2025'
heroImage: '../../assets/packets-hero.svg'
---

En redes, los datos no se envían de golpe — se dividen en fragmentos pequeños que viajan de forma independiente y se reconstruyen en el destino. Estos fragmentos se llaman **paquetes** en la Capa 3 (Red) y **tramas** en la Capa 2 (Enlace de Datos).

La analogía perfecta: imagina enviar una carta por correo. El **sobre** es la trama — contiene toda la información de entrega (direcciones MAC). La **carta dentro** es el paquete — contiene la información de destino final (direcciones IP) y los datos.

## Paquetes vs Tramas

| Concepto | Capa OSI | Contiene | Identificador |
|----------|----------|----------|---------------|
| **Trama** | Capa 2 — Enlace de Datos | Paquete encapsulado | Dirección MAC |
| **Paquete** | Capa 3 — Red | Segmento + cabecera IP | Dirección IP |

Cuando se elimina la información de encapsulación de una trama, obtenemos el paquete. Este proceso es la **desencapsulación**.

### ¿Por qué dividir los datos en paquetes?

Enviar datos en fragmentos pequeños tiene ventajas claras:
- Menos cuellos de botella en la red
- Si un paquete se pierde, solo se reenvía ese fragmento
- Diferentes paquetes pueden tomar rutas distintas y llegar por caminos más eficientes
- Se reconstruyen en orden en el destino gracias a los números de secuencia

---

## Cabeceras de un Paquete IP

Todo paquete IP lleva cabeceras con información esencial:

| Cabecera | Descripción |
|----------|-------------|
| **Dirección de origen** | IP del dispositivo que envía el paquete |
| **Dirección de destino** | IP del dispositivo destinatario |
| **TTL (Time To Live)** | Temporizador de expiración — evita que paquetes perdidos colapsen la red |
| **Suma de comprobación** | Verifica la integridad de los datos — si se modifica algo, el valor no coincide |

---

## TCP — Transmission Control Protocol

TCP es un protocolo **orientado a conexión** y **con estado**. Antes de enviar cualquier dato, debe establecerse una conexión entre cliente y servidor. Garantiza que todos los datos lleguen correctamente y en orden.

### Ventajas y desventajas de TCP

| Ventajas | Desventajas |
|----------|-------------|
| Garantiza la integridad de los datos | Requiere conexión confiable entre ambos dispositivos |
| Sincroniza los dispositivos para evitar desorden | Una conexión lenta puede generar cuellos de botella |
| Alto nivel de confiabilidad | Más lento que UDP por los procesos adicionales |

### Cabeceras de un paquete TCP

| Cabecera | Descripción |
|----------|-------------|
| **Puerto de origen** | Puerto aleatorio (0–65535) desde el que envía el cliente |
| **Puerto de destino** | Puerto del servicio en el servidor (ej: 80 para HTTP) |
| **IP de origen** | Dirección IP del remitente |
| **IP de destino** | Dirección IP del destinatario |
| **Número de secuencia** | Número aleatorio asignado al primer dato — permite reordenar |
| **Número de acuse de recibo** | Secuencia + 1 — confirma la recepción del fragmento anterior |
| **Suma de comprobación** | Verifica que los datos no estén corruptos |
| **Datos** | El contenido real que se transmite |
| **Flags** | Determinan cómo gestionar el paquete (SYN, ACK, FIN, RST...) |

---

## El Three-Way Handshake

Antes de enviar datos, TCP establece la conexión mediante el **protocolo de enlace de tres vías**:

```
Cliente                          Servidor
  |                                 |
  |-------- SYN (ISN=0) ----------->|   Paso 1: "Quiero conectar, mi seq es 0"
  |                                 |
  |<---- SYN/ACK (ISN=5000) --------|   Paso 2: "OK, mi seq es 5000, recibí tu 0"
  |                                 |
  |-------- ACK (seq=1) ----------->|   Paso 3: "Recibí tu 5000, empezamos"
  |                                 |
  |========= DATOS ================>|   Transferencia de datos
  |                                 |
  |-------- FIN ------------------>|   Cierre de conexión
  |<------- FIN/ACK ---------------|
```

### Mensajes del handshake

| Mensaje | Descripción |
|---------|-------------|
| **SYN** | Inicia la conexión — sincroniza los números de secuencia |
| **SYN/ACK** | El servidor confirma y envía su propio número de secuencia |
| **ACK** | Confirma la recepción de mensajes |
| **DATA** | Los datos reales una vez establecida la conexión |
| **FIN** | Cierra la conexión de forma ordenada |
| **RST** | Termina la conexión abruptamente — indica un error grave |

### Números de secuencia

Todos los datos enviados reciben un número de secuencia que se incrementa en 1 con cada fragmento. Esto permite reconstruir los datos en el orden correcto aunque lleguen desordenados:

```
ISN del cliente: 0  →  siguiente: 0+1=1  →  siguiente: 1+1=2  →  2+1=3...
```

---

## UDP — User Datagram Protocol

UDP es un protocolo **sin estado** y **sin conexión**. No realiza handshake ni verifica que los datos lleguen. Es más rápido pero menos fiable.

### Ventajas y desventajas de UDP

| Ventajas | Desventajas |
|----------|-------------|
| Mucho más rápido que TCP | No garantiza que los datos lleguen |
| No reserva conexión continua | Conexiones inestables = mala experiencia |
| Flexible para el desarrollador | Sin mecanismos de integridad de datos |

### ¿Cuándo usar UDP?

UDP se usa cuando **la velocidad importa más que la fiabilidad**:
- Streaming de vídeo y audio
- Videojuegos online
- VoIP (llamadas por internet)
- DNS (resoluciones rápidas)

### Cabeceras de un paquete UDP

| Cabecera | Descripción |
|----------|-------------|
| **TTL** | Temporizador de expiración del paquete |
| **Dirección de origen** | IP del remitente |
| **Dirección de destino** | IP del destinatario |
| **Puerto de origen** | Puerto aleatorio del remitente |
| **Puerto de destino** | Puerto del servicio destino |
| **Datos** | El contenido transmitido |

> UDP no tiene cabeceras de secuencia, acuse de recibo ni flags — por eso es más simple y rápido.

---

## TCP vs UDP — Comparativa rápida

| Característica | TCP | UDP |
|---------------|-----|-----|
| Conexión | Orientado a conexión | Sin conexión |
| Fiabilidad | Alta | Baja |
| Velocidad | Más lento | Más rápido |
| Orden de datos | Garantizado | No garantizado |
| Handshake | Three-way handshake | Ninguno |
| Uso típico | Web, email, FTP, SSH | Streaming, DNS, VoIP |

---

## Puertos

Los puertos son valores numéricos entre **0 y 65535** que identifican qué aplicación o servicio debe recibir los datos. Sin puertos, el sistema no sabría si un paquete entrante es para el navegador web, el cliente SSH o el servidor FTP.

Analogía: si la dirección IP es como la dirección de un edificio, el puerto es el número de apartamento.

### Rangos de puertos

| Rango | Nombre | Descripción |
|-------|--------|-------------|
| 0 – 1023 | Puertos bien conocidos | Servicios estándar (HTTP, SSH, FTP...) |
| 1024 – 49151 | Puertos registrados | Aplicaciones de terceros |
| 49152 – 65535 | Puertos dinámicos | Puertos de origen aleatorios |

### Puertos más importantes en ciberseguridad

| Puerto | Protocolo | Descripción |
|--------|-----------|-------------|
| **21** | FTP | Transferencia de archivos |
| **22** | SSH | Acceso remoto seguro por terminal |
| **80** | HTTP | Navegación web sin cifrar |
| **443** | HTTPS | Navegación web cifrada (SSL/TLS) |
| **445** | SMB | Compartir archivos e impresoras en red |
| **3389** | RDP | Escritorio remoto (Remote Desktop Protocol) |

> Estos protocolos pueden ejecutarse en puertos no estándar. Por ejemplo, un servidor web en el puerto 8080 en vez del 80. En ese caso debes indicarlo explícitamente: `http://ip:8080`

---

## Resumen visual del flujo

```
APLICACIÓN
    ↓ datos
TCP/UDP  →  añade puertos + cabeceras de transporte
    ↓ segmento
IP       →  añade IPs de origen/destino + TTL
    ↓ paquete
Ethernet →  añade MACs de origen/destino
    ↓ trama
Físico   →  01010101... (bits por el cable)
```
