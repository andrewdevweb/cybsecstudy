---
title: 'Ampliando tu red — Port Forwarding, Firewalls, VPNs, Routers y Switches'
description: 'Tecnologías esenciales para conectar redes a Internet: cómo funcionan el reenvío de puertos, los firewalls, las VPNs, los routers y los switches.'
pubDate: 'Mar 06 2025'
heroImage: '../../assets/blog-placeholder-2.jpg'
---

Hasta ahora sabemos cómo funcionan las redes internas. Pero ¿qué pasa cuando queremos **conectarnos al mundo exterior**? En esta entrada veremos las tecnologías clave que hacen posible extender una red a Internet — y por qué cada una importa en ciberseguridad.

---

## 🔀 Reenvío de Puertos (Port Forwarding)

Por defecto, un servidor dentro de una red local solo es accesible para dispositivos en esa misma red — esto se llama **intranet**. Si tienes un servidor web en `192.168.1.10:80`, ningún dispositivo externo puede llegar a él.

El **reenvío de puertos** cambia eso. Configurado en el router, permite que tráfico externo llegue a un dispositivo interno específico a través de un puerto concreto.

**Ejemplo práctico:**

```
Internet → IP pública 82.62.51.70:80 → Router → 192.168.1.10:80 (servidor web)
```

> ⚠️ Importante: el reenvío de puertos **abre** un puerto. El firewall decide **si el tráfico puede pasar** por ese puerto. Son dos conceptos distintos.

---

## 🛡️ Firewalls

Un firewall es el **guardia de seguridad** de una red. Controla qué tráfico puede entrar y salir basándose en reglas definidas por el administrador.

Un firewall puede filtrar tráfico según:

- **Origen** — ¿de dónde viene el tráfico?
- **Destino** — ¿a dónde va?
- **Puerto** — ¿a qué puerto está destinado?
- **Protocolo** — ¿usa TCP, UDP o ambos?

Para tomar estas decisiones, los firewalls realizan **inspección de paquetes**.

### Tipos de Firewall

| Tipo | Descripción | Ventaja | Desventaja |
|------|-------------|---------|------------|
| **Con estado** (Stateful) | Analiza la conexión completa, no solo paquetes individuales | Más inteligente, bloquea dispositivos completos si son maliciosos | Consume más recursos |
| **Sin estado** (Stateless) | Aplica reglas estáticas a cada paquete individualmente | Ligero, ideal contra ataques DDoS masivos | Menos eficaz, solo tan bueno como sus reglas |

Los firewalls pueden ser **dispositivos físicos dedicados** (en empresas grandes), **routers domésticos** o **software** como Snort.

---

## 🔐 VPN — Red Privada Virtual

Una VPN crea un **túnel cifrado** entre dispositivos en redes separadas, permitiéndoles comunicarse como si estuvieran en la misma red local.

**Caso de uso típico:** dos oficinas de una empresa en ciudades distintas conectadas como si fueran una sola red.

### Beneficios de una VPN

| Beneficio | Detalle |
|-----------|---------|
| **Conectividad geográfica** | Acceso a recursos de otra oficina desde cualquier lugar |
| **Privacidad** | El tráfico viaja cifrado, invisible para intermediarios |
| **Anonimato** | Tu ISP no puede rastrear fácilmente tu actividad |

> 💡 TryHackMe usa VPN para conectarte a sus máquinas vulnerables sin exponerlas directamente a Internet — así practicas de forma segura y legal.

### Tecnologías VPN

| Tecnología | Descripción |
|------------|-------------|
| **PPP** | Base de autenticación y cifrado. No enrutable por sí solo. |
| **PPTP** | Transporta datos PPP fuera de la red. Fácil de configurar pero cifrado débil. |
| **IPSec** | Cifrado fuerte usando el framework IP. Difícil de configurar pero muy seguro y compatible. |

---

## 🌐 Routers

Un router **conecta redes distintas** y decide cómo pasar datos entre ellas. Opera en la **Capa 3 del modelo OSI** y utiliza direcciones IP para tomar decisiones de enrutamiento.

Cuando hay varias rutas posibles entre dos puntos, el router elige la óptima según:

- ¿Cuál es el camino más **corto**?
- ¿Cuál es el más **confiable**?
- ¿Cuál tiene el medio más **rápido** (cobre vs fibra)?

Diferentes **protocolos de enrutamiento** (como OSPF o BGP) determinan automáticamente estas rutas.

---

## 🔌 Switches

Un switch permite **conectar múltiples dispositivos** dentro de una misma red (de 3 a 63 mediante cables Ethernet). A diferencia del router, un switch no conecta redes distintas — trabaja dentro de una sola red.

### Switch de Capa 2

Opera en la **Capa 2 del modelo OSI**. Reenvía **tramas** entre dispositivos usando sus **direcciones MAC**. Solo responsable de entregar datos al dispositivo correcto dentro de la red.

### Switch de Capa 3

Más sofisticado. Puede hacer **ambas cosas**:
- Reenviar tramas por MAC (como capa 2)
- Enrutar paquetes por IP (como un router básico)

### VLAN — Red de Área Local Virtual

Una **VLAN** permite dividir virtualmente una red física en segmentos separados, aunque todos los dispositivos estén conectados al mismo switch.

**Ejemplo:** el Departamento de Ventas y el de Contabilidad comparten switch e Internet, pero **no pueden comunicarse entre sí**. Más seguridad, menos superficie de ataque.

```
Switch físico único
├── VLAN 10 → Ventas        (acceso a Internet ✓, acceso a Contabilidad ✗)
└── VLAN 20 → Contabilidad  (acceso a Internet ✓, acceso a Ventas ✗)
```

---

## 🗺️ Resumen Visual

![Diagrama de red](/network-diagram.svg)

---

## 🔑 Conceptos Clave

| Concepto | Capa OSI | Función principal |
|----------|----------|-------------------|
| Router | Capa 3 | Conecta redes distintas, enruta paquetes IP |
| Switch L2 | Capa 2 | Conecta dispositivos, reenvía tramas por MAC |
| Switch L3 | Capas 2-3 | Reenvía tramas + enruta paquetes |
| Firewall | Múltiples | Filtra tráfico según reglas |
| VPN | Múltiples | Túnel cifrado entre redes |
| Port Forwarding | Capa 3-4 | Expone servicios internos al exterior |
| VLAN | Capa 2 | Segmentación virtual de red |