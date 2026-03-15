---
title: 'Cómo Funcionan los Sitios Web'
description: 'Frontend, backend, HTML, CSS, JavaScript, exposición de datos y HTML injection explicados desde cero.'
pubDate: 'Mar 14 2026 10:00:00'
heroImage: '../../assets/web-hero.svg'
---

Cuando visitas un sitio web, tu navegador envía una solicitud a un servidor web pidiendo información. El servidor responde con datos que el navegador usa para mostrar la página. Un servidor web es simplemente un ordenador dedicado, en algún lugar del mundo, que gestiona esas solicitudes.

Un sitio web tiene dos partes principales:

- **Front End (lado del cliente):** lo que tu navegador renderiza y muestra
- **Back End (lado del servidor):** el servidor que procesa la solicitud y devuelve una respuesta

---

## Los tres pilares: HTML, CSS y JavaScript

| Tecnología | Función |
|-----------|---------|
| **HTML** | Estructura y contenido de la página |
| **CSS** | Estilo visual — colores, fuentes, layout |
| **JavaScript** | Interactividad y comportamiento dinámico |

---

## HTML — HyperText Markup Language

HTML define la estructura de una página web mediante **elementos** (también llamados etiquetas). Todo documento HTML sigue la misma estructura base:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Mi Página</title>
  </head>
  <body>
    <h1>Título principal</h1>
    <p>Un párrafo de texto.</p>
  </body>
</html>
```

### Elementos clave

| Etiqueta | Función |
|----------|---------|
| `<!DOCTYPE html>` | Define el documento como HTML5 |
| `<html>` | Elemento raíz — todo va dentro |
| `<head>` | Metadatos de la página (título, estilos...) |
| `<body>` | Contenido visible en el navegador |
| `<h1>` | Encabezado grande |
| `<p>` | Párrafo |
| `<button>` | Botón interactivo |
| `<img>` | Imagen |

### Atributos

Las etiquetas pueden tener **atributos** que añaden información o comportamiento:

```html
<p class="bold-text">Texto en negrita</p>
<img src="img/cat.jpg" alt="Un gato">
<p id="ejemplo" class="destacado" data-valor="42">Múltiples atributos</p>
```

- **`class`** — puede compartirse entre varios elementos (para estilos)
- **`id`** — único por elemento (para JS y estilos específicos)
- **`src`** — ruta de un recurso externo (imágenes, scripts)

> 💡 Puedes ver el HTML de cualquier sitio haciendo clic derecho → **Ver código fuente de la página**.

---

## JavaScript — Interactividad

JavaScript es el lenguaje más popular del mundo web. Sin él, las páginas serían estáticas. JS permite actualizar la página en tiempo real, gestionar eventos y manipular el DOM.

Se incluye en la página de dos formas:

```html
<!-- Inline -->
<script>
  document.getElementById("demo").innerHTML = "Hack the Planet";
</script>

<!-- Externo -->
<script src="/js/app.js"></script>
```

### Eventos

Los elementos HTML pueden reaccionar a eventos del usuario:

```html
<button onclick='document.getElementById("demo").innerHTML = "Botón pulsado"'>
  Haz clic
</button>
```

Los eventos más comunes son `onclick`, `onhover`, `onsubmit`, `onload`.

---

## Exposición de Datos Sensibles

Un sitio web no protege adecuadamente la información cuando deja datos confidenciales visibles en el código fuente del lado del cliente.

**Qué buscar al auditar una web:**

- Comentarios HTML con credenciales o notas internas
- Contraseñas o tokens hardcodeados en JavaScript
- URLs o rutas ocultas a secciones privadas
- Claves de API en el código fuente

```html
<!-- TODO: eliminar antes de producción -->
<!-- admin:password123 -->
<a href="/admin-panel">Panel de administración</a>
```

> ⚠️ Siempre que evalúes una aplicación web, lo primero es revisar el código fuente con **Ctrl+U** o las DevTools.

---

## HTML Injection

La inyección HTML ocurre cuando un sitio web muestra texto introducido por el usuario **sin sanitizar**. Si la entrada del usuario se incluye directamente en la página, un atacante puede inyectar etiquetas HTML o código JavaScript.

**Ejemplo vulnerable:**

```javascript
function sayHi() {
  const name = document.getElementById('name').value;
  document.getElementById("welcome-msg").innerHTML = "Welcome " + name;
}
```

Si el usuario introduce `<h1>Hackeado</h1>` en el campo de nombre, el navegador lo renderizará como HTML real.

**Consecuencias:**
- Modificar la apariencia de la página para otros usuarios
- Phishing — mostrar formularios falsos de login
- Escalada a **XSS** (Cross-Site Scripting) si se inyecta JavaScript

**Solución — sanitizar siempre la entrada:**

```javascript
// MAL — muestra HTML directamente
element.innerHTML = userInput;

// BIEN — escapa el HTML
element.textContent = userInput;
```

> 🔑 Regla de oro: **nunca confíes en la entrada del usuario**. Sanitiza todo antes de usarlo.

---

## Resumen de seguridad

| Vulnerabilidad | Causa | Impacto |
|----------------|-------|---------|
| **Exposición de datos** | Datos sensibles en código fuente | Credenciales, rutas privadas expuestas |
| **HTML Injection** | Input de usuario sin sanitizar | Control visual de la página, phishing |
| **XSS** | JS inyectado sin filtrar | Robo de cookies, ejecución de código |
