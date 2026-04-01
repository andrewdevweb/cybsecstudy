---
title: 'AAA y Control de Acceso - Autenticacion, Autorizacion y Trazabilidad'
description: 'Identidad, permisos, sesiones, MFA y accounting explicados con un enfoque practico de ciberseguridad.'
pubDate: 'Apr 01 2026 15:05:00'
heroImage: '../../assets/aaa-hero.svg'
category: 'seguridad'
---

Cuando hablamos de proteger un sistema, muchas veces pensamos enseguida en cifrado, firewalls o antivirus. Pero antes de todo eso hay una pregunta basica:

**quien puede entrar, que puede hacer y como queda registrado lo que hace**.

Esa es exactamente la idea que resume **AAA**:

- **Autenticacion**
- **Autorizacion**
- **Accounting**

AAA no es solo un acronimo clasico de teoria. Es uno de los marcos mas utiles para entender el control de acceso moderno en sistemas, redes, aplicaciones web, entornos cloud y plataformas empresariales.

![Flujo AAA](/aaa-flow.svg)

---

## La idea central de AAA

AAA organiza el acceso en tres capas logicas:

1. **Autenticacion**: demostrar quien eres.
2. **Autorizacion**: decidir que puedes hacer.
3. **Accounting**: registrar que hiciste y cuando.

Sin autenticacion, el sistema no sabe con quien trata.  
Sin autorizacion, cualquier usuario autenticado podria hacer demasiado.  
Sin accounting, no hay trazabilidad ni capacidad real de investigacion.

Por eso AAA se relaciona directamente con:

- identidades digitales
- permisos
- sesiones
- logs
- auditoria
- cumplimiento
- deteccion de abuso

---

## Identidad: el punto de partida

Antes de autenticar a nadie, el sistema necesita una **identidad** con la que trabajar.

Una identidad puede ser:

- un usuario humano
- una cuenta de servicio
- una aplicacion
- una API client
- una maquina
- un dispositivo

La identidad responde a la pregunta: **quien dice ser esta entidad**.

En seguridad real es importante distinguir entre:

- **identidad**: el sujeto
- **credencial**: lo que usa para probarlo
- **sesion**: el estado temporal que se crea tras validarlo

Confundir estas tres piezas es una fuente muy comun de errores de diseno.

---

## Autenticacion

La autenticacion verifica la identidad declarada.

Es el momento en el que el sistema comprueba si el sujeto es realmente quien dice ser.

### Factores de autenticacion

Los factores clasicos son:

- **algo que sabes**: contraseña, PIN
- **algo que tienes**: token, app MFA, tarjeta, llave FIDO
- **algo que eres**: huella, rostro, biometria

Cuantos mas factores independientes combines, mas dificil es suplantar la identidad.

### Ejemplos comunes

- login con usuario y contraseña
- inicio de sesion con MFA
- certificado de cliente
- clave SSH
- token de API
- SSO corporativo

### Errores frecuentes

- contraseñas debiles o reutilizadas
- MFA opcional en activos criticos
- credenciales compartidas
- secretos hardcodeados
- almacenamiento inseguro de credenciales
- no invalidar sesiones antiguas

La autenticacion no significa "tener una contraseña". Significa tener un proceso fiable para verificar identidad.

---

## Autorizacion

Una vez autenticado el sujeto, llega la segunda pregunta:

**que puede hacer realmente**.

La autorizacion decide si una accion concreta esta permitida:

- leer un archivo
- modificar un registro
- borrar un objeto
- reiniciar un servicio
- acceder a un panel de administracion
- invocar una API sensible

### Autorizacion no es autenticacion

Este es uno de los errores mas importantes al estudiar seguridad.

Un usuario puede estar **correctamente autenticado** y aun asi **no deberia tener permiso** para ejecutar cierta accion.

Ejemplo:

- Ana inicia sesion correctamente.
- El sistema confirma su identidad.
- Ana intenta ver nominas de toda la empresa.
- La autenticacion ha sido valida, pero la autorizacion debe denegar la accion.

### Modelos habituales de autorizacion

- **RBAC**: acceso segun rol
- **ABAC**: acceso segun atributos y contexto
- **ACL**: listas de control por objeto o recurso
- **policy-based access control**: decisiones guiadas por reglas centralizadas

![Modelos de acceso](/aaa-access-models.svg)

### Principios clave

- minimo privilegio
- separacion de funciones
- permisos explicitos
- negacion por defecto
- revision periodica de privilegios

---

## Accounting: trazabilidad y responsabilidad

La tercera A suele ser la menos explicada, pero es critica.

**Accounting** significa registrar eventos de acceso y actividad para saber:

- quien entro
- desde donde
- cuando
- a que recurso accedio
- que accion realizo
- si fue permitida o denegada

Esto conecta directamente con:

- logs
- auditoria
- deteccion de incidentes
- respuesta forense
- cumplimiento normativo

Sin accounting, el sistema puede autenticar y autorizar, pero la organizacion se queda ciega.

### Ejemplos de eventos utiles

- login correcto o fallido
- elevacion de privilegios
- acceso a datos sensibles
- cambios de permisos
- borrado de objetos
- uso de cuentas de servicio
- creacion y revocacion de tokens

![Trazabilidad y accounting](/aaa-traceability.svg)

---

## Sesiones, tokens y continuidad del acceso

AAA no termina en el login inicial.

Despues de autenticar a un usuario, la mayoria de sistemas crean una **sesion** o emiten un **token** para evitar repetir la autenticacion en cada accion.

### Que representa una sesion

Una sesion es una relacion temporal entre:

- una identidad validada
- un contexto de acceso
- unos permisos activos
- un tiempo de vida

### Riesgos asociados

- robo de cookies o tokens
- expiraciones demasiado largas
- falta de revocacion
- session fixation
- refresh tokens mal protegidos
- sesiones compartidas entre dispositivos o usuarios

Una sesion mal gestionada convierte una autenticacion correcta en una puerta abierta.

---

## AAA en entornos reales

### En aplicaciones web

- autenticacion: login, MFA, SSO, passkeys
- autorizacion: roles, permisos por recurso, middleware
- accounting: logs de acceso, cambios de perfil, acciones administrativas

### En sistemas Windows y Linux

- autenticacion: credenciales locales, dominio, claves, PAM
- autorizacion: grupos, ACLs, permisos, sudo, GPO
- accounting: event logs, syslog, auditoria de comandos y cambios

### En redes

- autenticacion: 802.1X, VPN, NAC, credenciales de admin
- autorizacion: acceso por perfil, VLAN, privilegios de equipo
- accounting: sesiones, origen, comandos, cambios de configuracion

### En cloud

- autenticacion: IAM, federacion, claves, identidades de workload
- autorizacion: politicas, roles, permisos granulares
- accounting: CloudTrail, activity logs, trazas de API y eventos

---

## Fallos comunes en control de acceso

Estos son algunos de los errores mas repetidos en sistemas reales:

### 1. Dar por bueno que "si ha iniciado sesion, puede hacerlo"

Eso mezcla autenticacion con autorizacion.

### 2. Roles demasiado amplios

Cuando muchas cuentas reciben permisos de administrador por comodidad, el riesgo crece de forma brutal.

### 3. No revisar privilegios con el tiempo

Las cuentas acumulan permisos aunque su funcion ya haya cambiado.

### 4. No registrar eventos relevantes

Sin trazabilidad, responder a un incidente se vuelve lento y poco fiable.

### 5. Sesiones demasiado largas o mal revocadas

Un token robado puede dar acceso mucho despues de que el usuario haya dejado de usarlo.

### 6. Cuentas compartidas

Si varias personas usan la misma identidad, desaparece la responsabilidad individual.

---

## AAA y principio de minimo privilegio

AAA y minimo privilegio encajan de forma natural.

- la autenticacion confirma identidad
- la autorizacion limita capacidades
- el accounting deja evidencia del uso real

Con esas tres piezas puedes responder a preguntas muy practicas:

- quien hizo esto
- tenia permiso para hacerlo
- desde donde lo hizo
- cuando paso
- que impacto tuvo

Ese tipo de visibilidad es exactamente lo que separa un sistema "que funciona" de un sistema "que funciona con criterio de seguridad".

---

## Resumen

- AAA significa autenticacion, autorizacion y accounting
- identidad, credencial y sesion no son lo mismo
- autenticar no implica autorizar
- authorization decide acciones concretas sobre recursos concretos
- accounting aporta trazabilidad, auditoria y capacidad de investigacion
- sesiones y tokens son parte critica del control de acceso moderno
- AAA aplica a web, sistemas, redes, cloud y entornos corporativos
- un mal control de acceso suele acabar en abuso de privilegios, fugas o falta de trazabilidad

En la guia interactiva asociada veremos AAA de forma visual, con modelos de acceso, sesiones, trazabilidad y un quiz de 10 preguntas.
