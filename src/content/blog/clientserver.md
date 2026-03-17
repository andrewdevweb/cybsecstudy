---
title: 'Conceptos Básicos de Cliente-Servidor'
description: 'El modelo cliente-servidor, DNS, puertos, protocolos y HTTP explicados desde cero con analogías reales.'
pubDate: 'Mar 17 2026 09:00:00'
heroImage: '../../assets/clientserver-hero.svg'
---

Al principio, los ordenadores funcionaban solos. Almacenaban sus propios archivos, ejecutaban sus propios programas y no se comunicaban con nadie. Pronto, organizaciones de todo el mundo comenzaron a interconectarlos — ARPANET, CYCLADES, NPL y NSFNET sentaron las bases de lo que hoy conocemos como Internet.

Con la interconexión llegó la especialización. Algunos sistemas comenzaron a ofrecer servicios; otros, a consumirlos. Así nació el **modelo cliente-servidor**.

---

## La analogía de la pizzería

Es viernes por la noche. Alice y Bob quieren pizza. Alice mira el menú de Luigi's Pizza, decide qué quiere y Bob conduce hasta allí. Entra, hace el pedido: "Una pizza grande de pepperoni y una Coca-Cola". El empleado confirma y prepara la pizza. Bob vuelve a casa.

Este proceso cotidiano esconde toda la arquitectura de Internet.

---

## Cliente, Servidor y Servicio

| Analogía | Informática |
|---------|-------------|
| Luigi's Pizza | Servidor web |
| Bob (el que pide) | Navegador (cliente) |
| Alice (quien quiere la pizza) | Usuario final |
| El menú | API / endpoints disponibles |
| El pedido | Solicitud HTTP (request) |
| La pizza entregada | Respuesta HTTP (response) |

**El cliente siempre inicia la solicitud.** El servidor espera, escucha y responde.

En términos informáticos: Alice usa un **navegador** (cliente) para solicitar una página web. El **servidor** es el sistema que la almacena y la sirve. El cliente hace la solicitud; el servidor devuelve el recurso.

---

## Solicitud y Respuesta

Alice pidió una pizza de pepperoni. Si no había pepperoni, el empleado respondía con un error. Si el pedido era incomprensible, tampoco se podía procesar.

En sistemas informáticos ocurre lo mismo:
- Si la solicitud está mal formateada → error
- Si el recurso no existe → `404 Not Found`
- Si el servidor tiene un problema → `500 Internal Server Error`
- Si todo va bien → `200 OK` + el contenido solicitado

---

## Protocolo

Cuando Bob llega a Luigi's, habla en inglés y usa el formato del menú. No puede llegar hablando en ruso y pidiendo sushi — el servidor no lo entendería.

Un **protocolo** define exactamente cómo se comunican cliente y servidor:

- Qué comandos entienden ambas partes (ej: `GET`, `POST`)
- Cómo se estructura una solicitud
- Qué sintaxis usar
- Qué respuesta dar a cada tipo de solicitud
- Qué hacer ante errores

Los protocolos más importantes en la web:

| Protocolo | Puerto | Uso |
|-----------|--------|-----|
| **HTTP** | 80 | Web sin cifrar |
| **HTTPS** | 443 | Web cifrada (TLS) |
| **DNS** | 53 | Resolución de nombres |
| **FTP** | 21 | Transferencia de archivos |
| **SSH** | 22 | Acceso remoto seguro |
| **SMTP** | 25/587 | Envío de correo |

---

## Puerto

Luigi's tiene tres entradas: puerta A para llevar, puerta B para comer en el local, puerta C para entregas. Cada servicio usa su propia entrada.

Los **puertos** funcionan igual. Un servidor puede ejecutar múltiples servicios simultáneamente, cada uno escuchando en un puerto distinto:

```
servidor.com:80   → HTTP (web)
servidor.com:443  → HTTPS (web cifrada)
servidor.com:22   → SSH (acceso remoto)
servidor.com:3306 → MySQL (base de datos)
```

Los puertos van del **0 al 65535**:
- **0–1023** — puertos bien conocidos (reservados para servicios estándar)
- **1024–49151** — puertos registrados (aplicaciones conocidas)
- **49152–65535** — puertos dinámicos (asignados temporalmente)

Desde el punto de vista de seguridad, escanear puertos abiertos (**nmap**) es uno de los primeros pasos en un reconocimiento — revela qué servicios expone un servidor.

---

## DNS — Domain Name System

Bob no sabía la dirección física de Luigi's — solo su nombre. Introdujo "Luigi's Pizza" en el GPS y obtuvo las coordenadas.

El **DNS** hace exactamente eso en Internet: traduce nombres de dominio (`tryhackme.com`) a direcciones IP (`104.26.10.229`). Las IPs son las "coordenadas" de los servidores.

Sin DNS tendrías que memorizar la IP de cada sitio web que visitas.

---

## HTTP — El protocolo de la Web

**HTTP** (HyperText Transfer Protocol) es el protocolo cliente-servidor que usa la World Wide Web. Es **sin estado** — cada solicitud se procesa de forma independiente, sin que el servidor recuerde solicitudes anteriores.

Pero entonces, ¿cómo funcionan los inicios de sesión? Los sitios web implementan **sesiones** mediante cookies o tokens. Al iniciar sesión, el servidor crea un identificador de sesión que se envía en cada solicitud posterior — así el servidor "recuerda" quién eres sin que HTTP lo haga nativamente.

### Métodos HTTP

La especificación HTTP define 9 métodos principales:

| Método | Uso |
|--------|-----|
| **GET** | Obtener un recurso del servidor |
| **POST** | Enviar datos para crear un nuevo recurso |
| **PUT** | Actualizar un recurso existente |
| **DELETE** | Eliminar un recurso |
| **PATCH** | Modificación parcial de un recurso |
| **HEAD** | Como GET pero sin cuerpo en la respuesta |
| **OPTIONS** | Consultar qué métodos acepta el servidor |
| **CONNECT** | Establecer un túnel (usado con proxies) |
| **TRACE** | Diagnóstico — eco de la solicitud recibida |

En la práctica, el 95% del tráfico web usa **GET** y **POST**.

### Una solicitud GET real

Cuando escribes `https://tryhackme.com` en tu navegador, este construye automáticamente una solicitud GET:

```http
GET / HTTP/1.1
Host: tryhackme.com
User-Agent: Mozilla/5.0 Firefox/87.0
Accept: text/html
```

El servidor responde con un código de estado y el contenido:

```http
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 12483

<!DOCTYPE html>
<html>...
```

### Anatomía de una solicitud — campos clave

| Campo | Ejemplo | Significado |
|-------|---------|-------------|
| **Esquema** | `https` | Protocolo usado |
| **Host** | `tryhackme.com` | Servidor destino |
| **Ruta** | `/index.html` | Recurso solicitado |
| **Dirección IP** | `104.26.10.229` | IP resuelta por DNS |
| **Estado** | `200 OK` | Resultado de la solicitud |

> 💡 Puedes ver todas las solicitudes HTTP que hace tu navegador en **DevTools → pestaña Red** (F12). Cada elemento de la página (HTML, CSS, imágenes, scripts) es una solicitud GET separada.

---

## El flujo completo

```
1. Escribes tryhackme.com en el navegador
2. DNS traduce el nombre a una IP (104.26.10.229)
3. El navegador se conecta al puerto 443 (HTTPS)
4. Envía: GET / HTTP/1.1
5. El servidor procesa la solicitud
6. Responde: 200 OK + HTML
7. El navegador renderiza la página
```

Todo esto ocurre en **milisegundos**.

---

## Relevancia en ciberseguridad

El modelo cliente-servidor es el corazón de prácticamente todos los ataques web:

| Concepto | Vector de ataque |
|---------|-----------------|
| **Protocolo HTTP** | Interceptación si no hay HTTPS (MitM) |
| **Puertos** | Servicios expuestos innecesariamente = superficie de ataque |
| **DNS** | DNS spoofing, DNS poisoning, DNS over HTTPS |
| **Cookies/Sesiones** | Robo de sesión (session hijacking), XSS |
| **Método GET** | Parámetros visibles en URL — nunca enviar credenciales por GET |
| **Método POST** | SQL injection, CSRF si no hay protección |
