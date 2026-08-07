**Idiomas:** [Español] | [English](02-what-is-mac-randomization.en.md)

---

# Fundamentos Técnicos: Aleatorización de Direcciones MAC

## Introducción al Direccionamiento de Capa 2

En la arquitectura de redes definida por el modelo OSI, la dirección de Control de Acceso al Medio (MAC) es un identificador de 48 bits asignado a las interfaces de red para las comunicaciones en el segmento de red física (Capa 2). Tradicionalmente, este identificador era grabado en el hardware por el fabricante y se mantenía estático durante toda la vida útil del dispositivo.

Una dirección MAC estándar se compone de dos partes:
1. **OUI:** Los primeros 24 bits, asignados por el IEEE, que identifican al fabricante del hardware.
2. **UAA:** Los últimos 24 bits, asignados por el fabricante de manera secuencial.

## El Vector de Rastreo y la Necesidad de Privacidad

La naturaleza estática de la dirección MAC introdujo una vulnerabilidad significativa contra la privacidad del usuario en redes inalámbricas 802.11. Debido a que los dispositivos móviles transmiten constantemente tramas de sondeo para descubrir redes conocidas, los receptores pasivos, puntos de acceso y empresas de análisis de datos podían registrar la dirección MAC estática para perfilar patrones de movimiento, tiempos de permanencia y ubicación geoespacial de los individuos sin su consentimiento ni asociación a la red.

## Arquitectura de la Aleatorización

Para mitigar este rastreo pervasivo, los sistemas operativos modernos implementaron mecanismos de ofuscación a nivel del kernel y del suplicante de red (por ejemplo, `wpa_supplicant`).

El proceso de aleatorización consiste en enmascarar la dirección MAC de hardware reemplazándola por una Dirección Administrada Localmente generada por software. 

Para que la infraestructura de red reconozca que la dirección es generada lógicamente y no es una dirección física original, el sistema operativo altera un bit específico:
* En el primer octeto de la dirección MAC generada, el sistema establece el **segundo bit menos significativo en 1**. Esto define formalmente a la dirección como "Localmente Administrada" según los estándares del IEEE, evitando colisiones de red con direcciones asignadas de fábrica.

## Modalidades de Implementación en Sistemas Operativos

La generación de estas direcciones lógicas se presenta generalmente en dos modalidades operativas:

### 1. Aleatorización Persistente
Es el comportamiento predeterminado en la mayoría de los sistemas operativos actuales. El núcleo criptográfico del sistema genera una dirección MAC única basada en el SSID y credenciales de seguridad, pero mantiene dicha dirección de forma consistente cada vez que se conecta a esa misma red. 
* **Objetivo:** Evitar el rastreo entre múltiples redes distintas, pero mantener la estabilidad de la sesión en una red específica.

### 2. Aleatorización No Persistente
En esta configuración, el sistema operativo genera una nueva dirección MAC lógica en intervalos regulares o cada vez que la interfaz de radiofrecuencia se desasocia y se vuelve a asociar al Punto de Acceso, independientemente de si es el mismo SSID.
* **Objetivo:** Máxima privacidad. Impide que el administrador de una red específica pueda rastrear múltiples sesiones del mismo dispositivo a lo largo del tiempo.

## Implicación en Portales Cautivos

La implementación de la Aleatorización No Persistente es el mecanismo técnico que neutraliza los controles de sesión basados en Capa 2. Al forzar una reconexión, el subsistema de red del dispositivo móvil presenta una LAA completamente nueva al punto de acceso. 

Consecuentemente, las tablas de enrutamiento, el servidor DHCP y el controlador del portal cautivo procesan la solicitud como un nodo de hardware inédito, provocando la asignación de una nueva concesión de red y el reinicio de los contadores de sesión.