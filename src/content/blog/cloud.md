---
title: 'Fundamentos de la Computación en la Nube'
description: 'Cloud computing desde cero: evolución, modelos IaaS/PaaS/SaaS, proveedores, seguridad y relevancia en ciberseguridad.'
pubDate: 'Mar 20 2026 09:00:00'
heroImage: '../../assets/cloud-hero.svg'
category: 'fundamentos'
---

Transferir archivos de un portátil a almacenamiento online accesible desde cualquier lugar. Ejecutar una aplicación no en un solo ordenador, sino distribuida globalmente. Escalar de 10 a 10 millones de usuarios sin comprar nuevo hardware. Esto es la **computación en la nube**.

La nube no surgió de repente — es el resultado de décadas de evolución en cómo se usan y administran los servidores.

---

## Evolución hacia la nube

| Era | Periodo | Característica |
|-----|---------|---------------|
| **Servidores físicos** | 1960s–2000s | Un servidor = una app. Caro, lento de escalar |
| **Virtualización** | 1999–2006 | Múltiples VMs por servidor. Mejor utilización |
| **Automatización remota** | 2003–2006 | Gestión por internet. Infraestructura más flexible |
| **Cloud Computing** | 2006 (AWS) | Alquiler de recursos bajo demanda. Sin hardware propio |
| **Era moderna** | 2012–hoy | Contenedores, serverless, IA integrada, escala global |

El punto de inflexión fue **2006**, cuando Amazon lanzó AWS — convirtiendo su infraestructura interna en un servicio que cualquiera podía alquilar por horas.

---

## Beneficios clave

**Escalabilidad** — aumenta o reduce recursos según la demanda. Netflix escala sus servidores antes del estreno de una serie y los reduce después.

**Pago por uso** — no compras hardware, pagas por lo que consumes. Sin costes iniciales, sin hardware obsoleto.

**Alta disponibilidad** — los proveedores distribuyen la infraestructura en múltiples zonas geográficas. Si falla un centro de datos, otro toma el relevo automáticamente.

**Autoservicio inmediato** — crear un servidor virtual tarda segundos, no semanas.

**Acceso global** — despliega tu aplicación en múltiples regiones del mundo para reducir latencia.

**Seguridad gestionada** — los grandes proveedores invierten más en seguridad física y de red que la mayoría de empresas podrían permitirse.

---

## Tipos de nube

### Nube pública
El proveedor posee y gestiona la infraestructura. Los clientes alquilan recursos compartidos. Es la opción más común y económica.

**Ejemplos:** AWS, Azure, Google Cloud  
**Ideal para:** startups, aplicaciones web, servicios globales

### Nube privada
Infraestructura dedicada exclusivamente a una organización. Más control y personalización, pero más cara.

**Ideal para:** bancos, sanidad, gobierno — sectores con datos sensibles y requisitos de cumplimiento normativo estrictos

### Nube híbrida
Combina nube pública y privada. Los datos sensibles se mantienen en la privada; las cargas de trabajo variables se ejecutan en la pública.

**Ideal para:** comercio electrónico que necesita escalar durante el Black Friday sin exponer datos de pago

---

## Modelos de servicio — IaaS, PaaS, SaaS

La clave es entender **quién gestiona qué**:

| | IaaS | PaaS | SaaS |
|-|------|------|------|
| **Aplicación** | Tú | Tú | Proveedor |
| **Runtime / SO** | Tú | Proveedor | Proveedor |
| **Infraestructura** | Proveedor | Proveedor | Proveedor |
| **Analogía** | Piso vacío | Piso amueblado | Hotel |

### IaaS — Infraestructura como Servicio
Alquilas servidores virtuales, almacenamiento y red. Tú gestionas el SO y todo lo que va encima. Máximo control, máxima responsabilidad.

**Ejemplos:** EC2 (AWS), Azure VMs, Google Compute Engine  
**Ideal para:** ciberseguridad, pentesting, configuraciones personalizadas

### PaaS — Plataforma como Servicio
El proveedor gestiona la infraestructura y el SO. Tú solo despliegas código. Más rápido de desarrollar, menos control sobre el entorno.

**Ejemplos:** Heroku, Google App Engine, AWS Elastic Beanstalk  
**Ideal para:** desarrollo de aplicaciones web, APIs

### SaaS — Software como Servicio
Usas una aplicación completa a través del navegador. El proveedor gestiona absolutamente todo.

**Ejemplos:** Gmail, Zoom, Slack, Office 365, Salesforce  
**Ideal para:** usuarios finales que no necesitan control técnico

---

## Principales proveedores

| Proveedor | Cuota de mercado | Fortaleza |
|-----------|-----------------|-----------|
| **AWS** | ~32% | Más servicios, mayor alcance global |
| **Microsoft Azure** | ~23% | Integración empresarial, nube híbrida |
| **Google Cloud (GCP)** | ~12% | IA, análisis de datos, Kubernetes |
| **Alibaba Cloud** | ~4% | Líder en Asia |
| **IBM Cloud** | ~3% | Nube híbrida, IA empresarial |

AWS sigue siendo líder porque fue el primero (2006) y tiene el catálogo más amplio — más de 200 servicios distintos.

---

## Terminología esencial de AWS

| Término | Qué es |
|---------|--------|
| **EC2** | Servidor virtual (Elastic Compute Cloud) |
| **S3** | Almacenamiento de objetos (Simple Storage Service) |
| **VPC** | Red virtual privada en la nube |
| **IAM** | Gestión de identidades y accesos |
| **Region** | Ubicación geográfica (ej: eu-west-1 = Irlanda) |
| **Availability Zone** | Centro de datos dentro de una región |
| **AMI** | Imagen de máquina — snapshot del SO para lanzar instancias |

---

## Seguridad en la nube — Modelo de responsabilidad compartida

Uno de los conceptos más importantes en cloud security:

**El proveedor es responsable de** la seguridad *de* la nube:
- Seguridad física de los centros de datos
- Hardware, red y virtualización
- Disponibilidad de la infraestructura

**El cliente es responsable de** la seguridad *en* la nube:
- Configuración de los servicios
- Gestión de identidades y accesos (IAM)
- Cifrado de datos
- Actualizaciones del SO (en IaaS)
- Configuración de firewalls y grupos de seguridad

> ⚠️ La mayoría de brechas de seguridad en la nube son por **misconfiguration** del cliente, no por fallos del proveedor. Un bucket S3 mal configurado que expone datos públicamente es responsabilidad del cliente, no de AWS.

---

## Casos de uso reales

**Netflix** — toda su plataforma corre en AWS. Escala a millones de usuarios durante estrenos sin comprar servidores.

**Spotify** — usa la nube para gestionar 100M+ canciones y servir recomendaciones personalizadas en tiempo real.

**Instagram** — almacena y distribuye miles de millones de fotos y vídeos globalmente con baja latencia.

**Tiendas online** — escalan durante el Black Friday 10x sin infraestructura permanente extra.

---

## Relevancia en ciberseguridad

La nube ha cambiado radicalmente el panorama de la seguridad:

| Área | Implicación |
|------|-------------|
| **Superficie de ataque** | APIs expuestas, buckets S3 públicos, instancias sin parchear |
| **IAM mal configurado** | Permisos excesivos = movimiento lateral en caso de compromiso |
| **Logs y monitorización** | CloudTrail, CloudWatch — auditoría de toda la actividad |
| **Pentesting cloud** | AWS permite pentesting en tus propios recursos con previa notificación |
| **CSPM** | Cloud Security Posture Management — detecta misconfigurations automáticamente |
| **Secretos expuestos** | Claves AWS en repositorios GitHub = compromiso inmediato |

Herramientas clave para seguridad en cloud: **ScoutSuite**, **Prowler**, **CloudSploit**, **Pacu** (framework de explotación de AWS).
