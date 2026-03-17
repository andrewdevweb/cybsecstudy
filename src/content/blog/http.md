---
title: 'HTTP en Detalle — Solicitudes, Respuestas y Cookies'
description: 'Cómo funciona HTTP y HTTPS, métodos, códigos de estado, cabeceras y cookies explicados desde cero.'
pubDate: 'Mar 14 2026 09:00:00'
heroImage: '../../assets/http-hero.svg'
category: 'web'
---

HTTP (HyperText Transfer Protocol) es el protocolo que hace posible la web. Fue desarrollado por Tim Berners-Lee entre 1989 y 1991 y define las reglas para comunicarse con servidores web y transferir recursos: HTML, imágenes, vídeos, APIs...

**HTTPS** es la versión segura. El tráfico viaja cifrado, lo que impide que terceros intercepten los datos y garantiza que estás hablando con el servidor correcto — no con un impostor.

---

## ¿Qué es una URL?

Una URL (Localizador Uniforme de Recursos) es una instrucción completa sobre cómo acceder a un recurso en Internet. Cada parte tiene un rol específico:

```
http://usuario:contraseña@tryhackme.com:80/view-room?id=1#task3
```

| Parte | Ejemplo | Función |
|-------|---------|---------|
| **Esquema** | `http` | Protocolo a usar: HTTP, HTTPS, FTP |
| **Usuario** | `usuario:contraseña` | Credenciales para autenticación |
| **Host** | `tryhackme.com` | Dominio o IP del servidor |
| **Puerto** | `80` | Puerto de conexión (80=HTTP, 443=HTTPS) |
| **Ruta** | `/view-room` | Ubicación del recurso en el servidor |
| **Query string** | `?id=1` | Parámetros adicionales para la ruta |
| **Fragmento** | `#task3` | Referencia a una sección dentro de la página |

---

## Métodos HTTP

Los métodos indican al servidor qué acción quiere realizar el cliente:

| Método | Uso |
|--------|-----|
| **GET** | Obtener información del servidor |
| **POST** | Enviar datos — crear nuevos registros |
| **PUT** | Enviar datos — actualizar información existente |
| **DELETE** | Eliminar información del servidor |

En el mundo real trabajarás principalmente con **GET** y **POST**.

---

## Hacer una solicitud HTTP

La solicitud más básica posible es una sola línea:

```
GET / HTTP/1.1
```

Pero en la práctica siempre se envían cabeceras adicionales:

```
GET / HTTP/1.1
Host: tryhackme.com
User-Agent: Mozilla/5.0 Firefox/87.0
Referer: https://tryhackme.com/
```

- **Línea 1** — Método `GET`, ruta `/`, protocolo `HTTP/1.1`
- **Host** — Qué sitio web queremos (un servidor puede alojar varios)
- **User-Agent** — Qué navegador estamos usando
- **Referer** — Desde qué página venimos
- **Línea en blanco** — Indica al servidor que la solicitud ha terminado

---

## Respuesta HTTP

```
HTTP/1.1 200 OK
Server: nginx/1.15.8
Date: Fri, 09 Apr 2021 13:34:03 GMT
Content-Type: text/html
Content-Length: 98

<html>
<head>
    <title>TryHackMe</title>
</head>
<body>
    Welcome To TryHackMe.com
</body>
</html>
```

- **Línea 1** — Versión HTTP + código de estado
- **Server** — Software del servidor web
- **Date** — Fecha y hora del servidor
- **Content-Type** — Tipo de datos que se devuelven
- **Content-Length** — Tamaño de la respuesta en bytes
- **Línea en blanco** — Fin de las cabeceras
- **Cuerpo** — El recurso solicitado

---

## Códigos de estado HTTP

La primera línea de toda respuesta HTTP incluye un código de estado que indica el resultado:

| Rango | Categoría | Significado |
|-------|-----------|-------------|
| **100–199** | Informativo | La solicitud está en proceso — continúa enviando |
| **200–299** | Éxito | La solicitud se completó correctamente |
| **300–399** | Redirección | El recurso está en otro lugar |
| **400–499** | Error del cliente | Algo está mal en la solicitud |
| **500–599** | Error del servidor | Algo falló en el servidor |

### Códigos más comunes

| Código | Nombre | Cuándo ocurre |
|--------|--------|---------------|
| `200` | OK | Solicitud completada con éxito |
| `201` | Created | Se creó un nuevo recurso |
| `301` | Moved Permanently | La página se movió para siempre — actualiza tu enlace |
| `302` | Found | Redirección temporal |
| `400` | Bad Request | La solicitud tiene errores o le faltan parámetros |
| `401` | Unauthorized | Necesitas autenticarte primero |
| `403` | Forbidden | No tienes permiso, aunque estés autenticado |
| `404` | Not Found | El recurso no existe |
| `405` | Method Not Allowed | Ese método no está permitido para esa ruta |
| `500` | Internal Server Error | El servidor no supo manejar la solicitud |
| `503` | Service Unavailable | El servidor está sobrecargado o en mantenimiento |

---

## Cabeceras HTTP

Las cabeceras son metadatos que viajan junto a la solicitud o respuesta. No son obligatorias, pero sin ellas los navegadores no pueden renderizar correctamente los sitios.

### Cabeceras de solicitud (cliente → servidor)

| Cabecera | Función |
|----------|---------|
| `Host` | Indica qué sitio web quieres — útil en servidores que alojan varios |
| `User-Agent` | Navegador y versión — permite al servidor adaptar la respuesta |
| `Content-Length` | Tamaño de los datos enviados — el servidor espera exactamente eso |
| `Accept-Encoding` | Métodos de compresión que acepta el cliente (`gzip`, `br`...) |
| `Cookie` | Datos de sesión almacenados previamente |

### Cabeceras de respuesta (servidor → cliente)

| Cabecera | Función |
|----------|---------|
| `Set-Cookie` | Ordena al navegador que guarde esta cookie |
| `Cache-Control` | Cuánto tiempo puede el navegador cachear este recurso |
| `Content-Type` | Tipo de datos devueltos: `text/html`, `application/json`, `image/png`... |
| `Content-Encoding` | Método de compresión usado: `gzip`, `br`... |

---

## Cookies

Las cookies son pequeños archivos de datos que el servidor manda al navegador mediante la cabecera `Set-Cookie`. A partir de entonces, el navegador las incluye automáticamente en cada solicitud al mismo dominio.

HTTP es un protocolo **sin estado** — no recuerda solicitudes anteriores. Las cookies resuelven esto: permiten al servidor saber quién eres entre una petición y otra.

**Usos principales:**

- **Autenticación** — El servidor guarda un token de sesión único, no la contraseña en texto plano
- **Preferencias** — Idioma, tema, configuración del sitio
- **Seguimiento** — Analítica y publicidad

```
# Servidor → Cliente
Set-Cookie: sessionId=abc123xyz; Expires=Wed, 21 Oct 2025 07:28:00 GMT; HttpOnly

# Cliente → Servidor (en cada solicitud posterior)
Cookie: sessionId=abc123xyz
```

> 💡 Puedes ver las cookies que envía tu navegador en las DevTools → pestaña **Red** → selecciona una solicitud → pestaña **Cookies**.
