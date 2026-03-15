---
title: 'La Web por Dentro — Infraestructura y Servidores'
description: 'Balanceadores de carga, CDNs, bases de datos, WAF, servidores web, hosts virtuales y contenido dinámico explicados desde cero.'
pubDate: 'Mar 15 2026 13:35:00'
heroImage: '../../assets/infra-hero.svg'
---

Cuando solicitas una página web, tu navegador necesita la dirección IP del servidor — para eso usa **DNS**. Luego se comunica con el servidor web mediante **HTTP**, que devuelve HTML, JavaScript, CSS e imágenes para que el navegador renderice la página.

Pero hay mucho más funcionando en segundo plano.

---

## Balanceadores de Carga

Cuando el tráfico crece o se necesita alta disponibilidad, un solo servidor no basta. Los balanceadores de carga resuelven dos problemas: gestionan grandes volúmenes de tráfico y proporcionan redundancia si un servidor falla.

Cuando haces una solicitud, el balanceador la recibe primero y la reenvía a uno de los servidores disponibles según un algoritmo:

| Algoritmo | Funcionamiento |
|-----------|---------------|
| **Round-robin** | Envía cada solicitud al siguiente servidor por turnos |
| **Ponderado** | Envía al servidor con menor carga actual |

Los balanceadores también hacen **comprobaciones de estado** periódicas — si un servidor no responde, dejan de enviarle tráfico hasta que se recupere.

---

## CDN — Red de Distribución de Contenido

Una CDN aloja archivos estáticos (JavaScript, CSS, imágenes, vídeos) en miles de servidores repartidos por todo el mundo. Cuando un usuario solicita uno de estos archivos, la CDN lo sirve desde el servidor más cercano geográficamente — reduciendo la latencia y la carga del servidor principal.

---

## Bases de Datos

Los sitios web necesitan almacenar información. Los servidores web se comunican con bases de datos para guardar y recuperar datos. Las hay de muchos tipos:

| Base de datos | Tipo | Uso común |
|--------------|------|-----------|
| **MySQL** | Relacional | Aplicaciones web generales |
| **PostgreSQL** | Relacional | Sistemas complejos |
| **MongoDB** | No relacional | Datos flexibles / JSON |
| **MSSQL** | Relacional | Entornos Microsoft |

---

## WAF — Web Application Firewall

Un WAF se sitúa entre la solicitud del usuario y el servidor web. Su función es proteger contra ataques y abuso:

- Detecta técnicas de ataque conocidas (SQLi, XSS, etc.)
- Distingue navegadores reales de bots
- Aplica **rate limiting** — limita solicitudes por segundo desde una misma IP
- Descarta solicitudes sospechosas antes de que lleguen al servidor

---

## ¿Qué es un Servidor Web?

Un servidor web es software que escucha conexiones entrantes y entrega contenido mediante HTTP. Los más comunes son:

| Servidor | Sistema | Directorio raíz por defecto |
|---------|---------|----------------------------|
| **Apache** | Linux | `/var/www/html` |
| **Nginx** | Linux | `/var/www/html` |
| **IIS** | Windows | `C:\inetpub\wwwroot` |
| **NodeJS** | Ambos | Configurable |

Si solicitas `http://example.com/foto.jpg`, el servidor busca `/var/www/html/foto.jpg` en su disco y lo devuelve.

---

## Hosts Virtuales

Un mismo servidor puede alojar múltiples sitios web con diferentes dominios usando **hosts virtuales**. El servidor compara el `Host` de la cabecera HTTP con sus archivos de configuración y sirve el sitio correcto.

```
one.com   →  /var/www/website_one
two.com   →  /var/www/website_two
```

No hay límite en la cantidad de sitios que puede alojar un servidor.

---

## Contenido Estático vs Dinámico

**Contenido estático** — nunca cambia. Se sirve directamente desde el disco: imágenes, CSS, JS, HTML fijo.

**Contenido dinámico** — cambia según la solicitud. Se genera en el **backend** en tiempo real usando lenguajes de scripting:

| Lenguaje | Plataforma |
|---------|-----------|
| PHP | Apache / Nginx |
| Python | Flask / Django |
| Ruby | Rails |
| JavaScript | NodeJS |

Ejemplo en PHP:

```php
// URL: http://example.com/index.php?name=adam
<html><body>Hello <?php echo $_GET["name"]; ?></body></html>

// El cliente recibe:
<html><body>Hello adam</body></html>
```

El cliente nunca ve el código PHP — solo el HTML resultante. Esta separación entre lo que el usuario ve (**frontend**) y lo que procesa el servidor (**backend**) es fundamental en seguridad web, ya que introduce vectores de ataque que no existen en sitios estáticos.

---

## El recorrido completo de una solicitud web

Cuando escribes una URL y pulsas Enter, esto es exactamente lo que ocurre:

![Flujo completo de una solicitud web](/infra-request-flow.svg)

En resumen: **DNS** resuelve el nombre, el **WAF** filtra la solicitud, el **balanceador** la distribuye, el **servidor** la procesa y devuelve el HTML que tu navegador renderiza.