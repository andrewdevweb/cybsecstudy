---
title: 'DNS — El Sistema de Nombres de Dominio'
description: 'Qué es el DNS, jerarquía de dominios, tipos de registros y cómo funciona una solicitud DNS paso a paso.'
pubDate: 'Mar 13 2026 12:00:00'
heroImage: '../../assets/dns-hero.svg'
category: 'redes'
---

El DNS (Sistema de Nombres de Dominio) es el servicio que traduce nombres como `tryhackme.com` a la dirección IP real del servidor — por ejemplo `104.26.10.229`. Sin él, tendrías que memorizar números para acceder a cada web.

Cada dispositivo en Internet tiene una **dirección IP única**: cuatro grupos de dígitos del 0 al 255 separados por puntos (IPv4), o un formato más largo en IPv6. El DNS hace de intermediario para que tú solo tengas que recordar el nombre.

---

## Jerarquía de dominios

Un nombre de dominio como `admin.tryhackme.com` tiene tres partes distintas:

| Parte | Ejemplo | Descripción |
|-------|---------|-------------|
| **TLD** | `.com` | Dominio de nivel superior — parte más a la derecha |
| **Dominio de segundo nivel** | `tryhackme` | El nombre registrado — máx. 63 caracteres |
| **Subdominio** | `admin` | Parte izquierda del dominio — máx. 63 caracteres |

### TLD — Dominio de nivel superior

Existen dos tipos:

- **gTLD** (genérico): indica el propósito del dominio. `.com` para comercial, `.org` para organizaciones, `.edu` para educación, `.gov` para gobierno. La demanda ha generado más de 2000 TLDs nuevos: `.online`, `.club`, `.website`, `.biz`...
- **ccTLD** (código de país): indica ubicación geográfica. `.ca` para Canadá, `.co.uk` para Reino Unido, `.es` para España.

### Subdominio

El subdominio se ubica a la izquierda del dominio de segundo nivel separado por un punto. En `admin.tryhackme.com`, `admin` es el subdominio. Puedes encadenar varios: `jupiter.servers.tryhackme.com`. La longitud total no puede superar **253 caracteres** y no hay límite en la cantidad de subdominios.

---

## Tipos de registros DNS

El DNS no solo sirve para sitios web — gestiona múltiples tipos de información sobre un dominio:

| Registro | Resuelve a | Ejemplo |
|----------|-----------|---------|
| **A** | Dirección IPv4 | `104.26.10.229` |
| **AAAA** | Dirección IPv6 | `2606:4700:20::681a:be5` |
| **CNAME** | Otro nombre de dominio | `store.tryhackme.com` → `shops.shopify.com` |
| **MX** | Servidor de correo del dominio | `alt1.aspmx.l.google.com` |
| **TXT** | Texto libre | Verificación, SPF, DMARC... |

### Registro A y AAAA

`A` mapea un nombre a una IPv4. `AAAA` hace lo mismo pero para IPv6. Son los registros más básicos — sin ellos, el dominio no resuelve a ningún servidor.

### Registro CNAME

Un alias que apunta a otro dominio. Útil cuando varios subdominios deben apuntar al mismo servidor sin duplicar IPs. Si la IP cambia, solo se actualiza en un sitio.

```
store.tryhackme.com → CNAME → shops.shopify.com → A → 23.227.38.65
```

### Registro MX

Indica qué servidor gestiona el correo del dominio. Incluye un valor de **prioridad** — si el servidor principal falla, el correo se redirige al siguiente en la lista.

```
tryhackme.com   MX  10  alt1.aspmx.l.google.com
tryhackme.com   MX  20  alt2.aspmx.l.google.com
```

### Registro TXT

Campos de texto libre con múltiples usos:

```
_acme-challenge.example.com   TXT  "token_value_here"
@ TXT "v=spf1 ip4:192.0.2.0/24 include:_spf.google.com ~all"
_dmarc.example.com TXT "v=DMARC1; p=reject; rua=mailto:dmarc@example.com"
@ TXT "MS=ms12345678"
```

Sus usos más comunes son: verificar propiedad del dominio, configurar SPF/DMARC para evitar spam, y autenticar servicios de terceros.

---

## ¿Cómo funciona una solicitud DNS?

Cuando escribes `tryhackme.com` en el navegador, ocurre lo siguiente:

**1. Caché local:**
Tu ordenador comprueba si ya resolvió ese dominio recientemente. Si está en caché y el TTL no ha expirado, usa esa respuesta directamente.

**2. Servidor DNS recursivo:**
Si no hay caché local, la consulta va al servidor DNS recursivo — normalmente proporcionado por tu ISP, aunque puedes usar otros como `8.8.8.8` (Google) o `1.1.1.1` (Cloudflare). Este servidor también tiene su propia caché.

**3. Servidores raíz:**
Si el recursivo no tiene la respuesta, consulta uno de los 13 servidores raíz de Internet. Su función es redirigir al servidor TLD correcto según la extensión del dominio (`.com`, `.org`, `.es`...).

**4. Servidor TLD:**
El servidor TLD sabe dónde están los servidores autoritativos para cada dominio registrado bajo esa extensión. Devuelve la dirección del servidor de nombres del dominio.

**5. Servidor autoritativo:**
Es el servidor final — el que tiene los registros DNS reales del dominio. Devuelve la respuesta al recursivo, que la cachea y la envía a tu ordenador.

**TTL (Time To Live):** cada registro DNS lleva un valor TTL en segundos. Mientras no expire, la respuesta se puede reutilizar desde caché sin volver a consultar.

![Flujo de resolución DNS](/dns-resolution.svg)


---

## Resumen

```
Tú → Caché local → DNS recursivo (ISP) → Servidores raíz → Servidor TLD → Servidor autoritativo → IP
```

El proceso completo suele tardar **menos de 100ms** gracias al sistema de caché en cada etapa.
