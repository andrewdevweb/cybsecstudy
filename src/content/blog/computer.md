---
title: 'Dentro de un Sistema Informático'
description: 'Componentes de un ordenador, cómo arrancan los sistemas y por qué entender el hardware es la base de la ciberseguridad.'
pubDate: 'Mar 15 2026 14:15:00'
heroImage: '../../assets/computer-hero.svg'
---

Antes de proteger un sistema, necesitas entenderlo. Como un castillo: para defenderlo, primero debes conocer su distribución — dónde está la cámara del tesoro, quién puede entrar y cómo. Intentar defender lo que no se entiende es defender a ciegas.

En esta guía veremos qué es un ordenador, sus componentes básicos y cómo arrancan — la base de todo lo que viene después en ciberseguridad.

---

## Los componentes principales

Casi todos los sistemas informáticos comparten los mismos componentes básicos. Cada uno tiene una función específica y todos trabajan juntos.

### CPU — El cerebro

La **Unidad Central de Procesamiento** es el componente que ejecuta las instrucciones. Cada acción que realiza un programa — sumar, comparar, mover datos — pasa por la CPU. Su velocidad se mide en GHz (ciclos por segundo).

Desde el punto de vista de seguridad, vulnerabilidades como **Spectre** y **Meltdown** afectan directamente a la CPU, permitiendo leer memoria de otros procesos.

### RAM — Memoria a corto plazo

La **Memoria de Acceso Aleatorio** almacena los datos que el sistema está usando en este momento. Es rápida pero volátil — cuando apagas el ordenador, su contenido desaparece.

En ciberseguridad, el **análisis forense de RAM** permite capturar contraseñas, claves de cifrado y procesos maliciosos en ejecución que no dejan rastro en disco.

### Almacenamiento — Memoria a largo plazo

Los dispositivos de almacenamiento (HDD y SSD) guardan datos de forma permanente.

| Tipo | Tecnología | Velocidad | Uso |
|------|-----------|-----------|-----|
| **HDD** | Disco magnético giratorio | Lento | Almacenamiento masivo |
| **SSD** | Chips de memoria flash | Rápido | Sistema operativo, apps |
| **NVMe** | Flash directa a PCIe | Muy rápido | Alto rendimiento |

El malware, los logs y los archivos robados residen en el almacenamiento — es el objetivo principal en análisis forense.

### Placa base — El esqueleto y los nervios

La **placa base** conecta todos los componentes entre sí. Contiene el chipset, los slots de RAM, los puertos PCIe para la GPU y los conectores SATA para el almacenamiento. Sin ella, nada se comunica con nada.

### GPU — El córtex visual

La **Unidad de Procesamiento Gráfico** está diseñada para cálculos paralelos masivos. Originalmente para gráficos, hoy se usa también para IA, minería de criptomonedas y... cracking de contraseñas por fuerza bruta (herramientas como Hashcat aprovechan la GPU para probar miles de millones de hashes por segundo).

### PSU — El corazón y los pulmones

La **Fuente de Alimentación** convierte la corriente alterna de la red eléctrica en corriente continua que usan los componentes. Sin energía estable, el sistema es inestable — un vector de ataque físico poco conocido.

---

## Cómo arranca un sistema informático

Al pulsar el botón de encendido ocurre una secuencia precisa antes de que veas el escritorio. Entender este proceso es esencial para comprender ataques como bootkits y rootkits de firmware.

### El proceso paso a paso

**Paso 1 — Pulsar el botón de encendido**
La PSU recibe la señal y distribuye energía a todos los componentes. El sistema "despierta".

**Paso 2 — El firmware se inicia (UEFI/BIOS)**
El firmware es el primer software que se ejecuta — está grabado en un chip de la placa base. El sistema moderno usa **UEFI** (Unified Extensible Firmware Interface). El antiguo **BIOS** hacía lo mismo pero ha sido reemplazado. UEFI inicializa el hardware y prepara el entorno para cargar el sistema operativo.

> ⚠️ Los **bootkits** son malware que infecta el firmware UEFI/BIOS, arrancando antes que el sistema operativo y siendo casi imposibles de detectar o eliminar con un antivirus convencional.

**Paso 3 — POST (Power-On Self-Test)**
El firmware ejecuta el autodiagnóstico: comprueba que la RAM, CPU, almacenamiento y otros componentes están presentes y funcionan. Si algo falla, el sistema emite pitidos o muestra un error.

**Paso 4 — Seleccionar el dispositivo de arranque**
La UEFI tiene una lista ordenada de dispositivos de arranque (SSD, USB, red...). Busca en el primero de la lista un gestor de arranque válido. Esta lista puede modificarse para arrancar desde un USB malicioso — técnica usada en ataques físicos.

**Paso 5 — Iniciar el gestor de arranque**
El gestor de arranque (como GRUB en Linux o el de Windows) carga el sistema operativo desde el almacenamiento a la RAM. Una vez cargado, la UEFI le cede el control de todos los componentes. El sistema está listo.

---

## La jerarquía de memoria

Los sistemas informáticos usan diferentes tipos de memoria con distintas velocidades y capacidades:

| Tipo | Velocidad | Capacidad | Volátil |
|------|-----------|-----------|---------|
| **Registros CPU** | Instantánea | Bytes | Sí |
| **Caché L1/L2/L3** | Nanosegundos | KB / MB | Sí |
| **RAM** | ~100 ns | GB | Sí |
| **SSD** | Microsegundos | TB | No |
| **HDD** | Milisegundos | TB | No |

Cuanto más rápida es la memoria, más cara y pequeña es. La CPU trabaja primero con los registros y la caché — si no encuentra los datos ahí, los busca en RAM, y si no en disco.

---

## Relevancia para la ciberseguridad

Entender el hardware no es solo teoría — tiene implicaciones directas:

- **Análisis forense** — los datos en RAM desaparecen al apagar; hay que capturarlos en vivo
- **Bootkits** — malware que infecta el firmware, previo al SO
- **Cold boot attacks** — enfriar la RAM para leer su contenido segundos después de apagar
- **GPU cracking** — la GPU procesa millones de hashes por segundo para romper contraseñas
- **Evil maid attacks** — acceso físico al hardware para instalar keyloggers de hardware o modificar el firmware
- **Side-channel attacks** — Spectre/Meltdown extraen datos aprovechando optimizaciones de la CPU

La seguridad empieza en el hardware. Un sistema con el software más seguro del mundo puede ser comprometido si el atacante tiene acceso físico o puede explotar el firmware.
