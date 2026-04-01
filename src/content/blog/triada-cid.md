---
title: 'La Triada CID - Confidencialidad, Integridad y Disponibilidad'
description: 'El modelo fundamental de la ciberseguridad explicado con ejemplos reales, controles tecnicos y errores habituales.'
pubDate: 'Apr 01 2026 13:15:00'
heroImage: '../../assets/cid-hero.svg'
category: 'seguridad'
---

La mayor parte de las decisiones de seguridad, desde cifrar una base de datos hasta desplegar un servicio redundante, pueden entenderse con un modelo muy simple: la **triada CID**.

CID significa:

- **Confidencialidad**
- **Integridad**
- **Disponibilidad**

No es un acronimo para memorizar y olvidar. Es una forma muy clara de responder a tres preguntas:

1. Quien puede ver la informacion.
2. Como se garantiza que no ha sido alterada.
3. Si sigue disponible cuando se necesita.

![Triada CID](/cid-pillars.svg)

Si uno de esos tres pilares falla, la seguridad del sistema tambien falla, aunque los otros dos parezcan bien cubiertos.

---

## La idea central de CID

La triada CID no describe herramientas concretas. Describe **objetivos de seguridad**.

Cuando analizas un sistema, una aplicacion o una arquitectura, puedes usarla como lente mental:

- **Confidencialidad**: evitar accesos no autorizados.
- **Integridad**: evitar o detectar cambios no autorizados.
- **Disponibilidad**: asegurar el acceso legitimo al servicio o dato cuando haga falta.

Eso aplica a:

- sistemas Windows y Linux
- aplicaciones web
- redes
- cloud
- backups
- incidentes
- cumplimiento

Su fuerza esta en que simplifica sin empobrecer.

---

## Confidencialidad

La confidencialidad busca que la informacion solo sea visible para quienes tienen permiso.

### Ejemplos

- una base de datos de clientes
- un historial medico
- secretos de una API
- credenciales internas
- correos corporativos

### Que rompe la confidencialidad

- fugas de datos
- buckets publicos
- sniffing de trafico sin cifrar
- credenciales robadas
- permisos mal configurados
- exfiltracion de archivos

### Controles tipicos

- cifrado en transito y en reposo
- control de acceso
- MFA
- minimo privilegio
- segmentacion
- gestion de secretos

La confidencialidad no depende solo de "tener login". Depende de que los datos correctos lleguen solo a los ojos correctos.

---

## Integridad

La integridad garantiza que la informacion y los sistemas no han sido alterados de forma indebida.

No basta con tener el archivo. Debe seguir siendo correcto, completo y confiable.

### Ejemplos

- una factura que no ha cambiado de importe
- un binario firmado
- un log fiable
- una transaccion bancaria correcta
- un backup sin corrupcion

### Que rompe la integridad

- manipulacion de registros
- cambios no auditados
- malware que modifica configuraciones
- inyeccion de datos maliciosos
- corrupcion
- supply chain compromise

### Controles tipicos

- hashes
- checksums
- firmas digitales
- control de cambios
- auditoria
- validacion de entradas

Si pierdes la integridad, dejas de poder confiar en lo que ves.

---

## Disponibilidad

La disponibilidad busca que los sistemas y datos esten accesibles cuando toca.

Un servicio cifrado y con datos intactos, pero caido, sigue siendo un fallo de seguridad.

### Ejemplos

- una web operativa durante picos de trafico
- autenticacion disponible en horario critico
- copias de seguridad restaurables
- un servicio con balanceo y failover

### Que rompe la disponibilidad

- DDoS
- ransomware
- borrado accidental
- fallos hardware
- caidas electricas
- saturacion
- dependencias unicas sin redundancia

### Controles tipicos

- alta disponibilidad
- redundancia
- backups
- balanceadores
- proteccion anti-DDoS
- monitoreo y respuesta

![Controles por pilar](/cid-controls.svg)

---

## Un incidente puede afectar a varios pilares

Ese es uno de los puntos mas importantes de la triada.

| Incidente | Pilar principal afectado |
|-----------|---------------------------|
| Fuga de base de datos | Confidencialidad |
| Cambio no autorizado en una factura | Integridad |
| Web caida por DDoS | Disponibilidad |
| Ransomware con robo y cifrado | Los tres |
| Bucket publico con informacion interna | Confidencialidad |
| Borrado de registros | Integridad y disponibilidad |

![Impacto de incidentes sobre la triada CID](/cid-incidents.svg)

La triada te ayuda a decir **que** esta fallando exactamente y no quedarte en un "tenemos un problema de seguridad".

---

## Equilibrios y tensiones

La triada no son tres cajas aisladas. Muchas decisiones mejoran un pilar y tensionan otro.

### Ejemplos reales

**Mas confidencialidad, menos comodidad**  
Cuantos mas controles de acceso y verificaciones pongas, mas friccion operativa puedes introducir.

**Mas integridad, menos velocidad de cambio**  
Firmas, validaciones y aprobaciones aumentan confianza, pero ralentizan despliegues.

**Mas disponibilidad, mas complejidad**  
Redundancia, replicacion y multiples puntos de acceso mejoran continuidad, pero tambien aumentan superficie y coste.

La seguridad real consiste en equilibrar los tres segun contexto y riesgo.

---

## Aplicacion practica de CID

### En una aplicacion web

- confidencialidad: sesiones seguras, TLS, permisos correctos
- integridad: validacion de entradas, logs fiables, control de cambios
- disponibilidad: escalado, cache, balanceo, mitigacion ante saturacion

### En sistemas

- confidencialidad: permisos, ACLs, cifrado, control de acceso
- integridad: firmas, auditoria, hardening, control de configuracion
- disponibilidad: backups, snapshots, redundancia, recuperacion

### En cloud

- confidencialidad: IAM, cifrado, secretos
- integridad: pipelines firmados, imagenes confiables, trazabilidad
- disponibilidad: multi-AZ, failover, monitoreo, restauracion

---

## Errores comunes al estudiar la triada

**1. Pensar que es demasiado basica**  
Sigue siendo util precisamente porque obliga a pensar bien.

**2. Tratarla como lista memorizada**  
No es para recitarla, sino para analizar sistemas reales.

**3. Reducir integridad a "que no haya virus"**  
Integridad es confianza en que el dato o sistema no ha sido alterado indebidamente.

**4. Olvidar que disponibilidad tambien es seguridad**  
Una organizacion parada es un problema de seguridad, no solo de IT.

**5. Ignorar que muchos incidentes golpean varios pilares a la vez**  
Los ataques modernos rara vez son de un solo color.

---

## Resumen

- la triada CID es el marco clasico de la seguridad de la informacion
- confidencialidad protege quien puede ver los datos
- integridad protege que los datos sigan siendo fiables
- disponibilidad protege que el acceso exista cuando hace falta
- un buen sistema no cubre solo un pilar
- muchos incidentes afectan a varios a la vez
- CID sigue siendo una herramienta muy practica para pensar mejor en seguridad

En la guia interactiva asociada veremos la triada CID de forma visual, con ejemplos, controles y un quiz de 10 preguntas.
