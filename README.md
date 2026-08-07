# Evasión de Portal Cautivo mediante Aleatorización de MAC

Este repositorio documenta una vulnerabilidad inherente en los mecanismos de autenticación y autorización de portales cautivos desplegados en redes Wi-Fi públicas. El análisis utiliza la infraestructura de red de la aerolínea AeroMexico como caso de estudio. El objetivo de este proyecto es proveer una prueba de concepto (Proof of Concept - PoC) con fines estrictamente educativos sobre las deficiencias de los controles de acceso basados en el filtrado de direcciones de Control de Acceso al Medio (MAC).

## Contexto del Caso de Estudio

La infraestructura de red analizada corresponde al Identificador de Conjunto de Servicios (SSID) **Aeromexico-WiFi**, configurado como una red abierta (sin encriptación de capa 2 como WPA2 o WPA3). La topología implementa un portal cautivo que intercepta el tráfico HTTP inicial y redirige al cliente a un formulario de autenticación web.

El modelo de provisión de servicio de este portal ejecuta la siguiente lógica:

- Asignación de un periodo de concesión (*lease time*) de **15 minutos** de acceso a internet a nivel de capa 3.
- Tras la expiración del periodo, el sistema bloquea el reenvío de paquetes y exige una transacción de **5 dólares** para restaurar la conectividad de la red de área amplia (WAN).

## La Vulnerabilidad: Deficiencia en la Gestión de Identidad de Sesión

La vulnerabilidad reside en la metodología utilizada por el sistema de control de acceso para rastrear la identidad de los clientes. Los portales cautivos de redes gratuitas suelen registrar la dirección MAC de la tarjeta de red para vincularla a una sesión y controlar el tiempo de uso. Una vez transcurrido el intervalo de **15 minutos**, dicha dirección MAC ingresa a una lista de control de acceso (ACL) restrictiva. Este enfoque asume de manera incorrecta que la dirección MAC es un identificador estático e inmutable del usuario en el entorno de la red de área local inalámbrica (WLAN).

## La Solución: Evasión mediante Aleatorización de Capa 2

Dado que la validación de la sesión depende exclusivamente del identificador físico de la capa de enlace de datos, la mitigación del bloqueo se logra mediante la alteración de dicho identificador. Al rotar la dirección MAC desde la configuración nativa del sistema operativo, el enrutador identifica al equipo como un dispositivo completamente nuevo. Al restablecer la asociación 802.11 con una nueva dirección MAC generada mediante algoritmos de aleatorización a nivel del sistema operativo, el servidor DHCP y el controlador del portal cautivo registran un nuevo cliente. Al ser visto por la red como un teléfono que acaba de llegar por primera vez, el portal reinicia el contador y otorga otros **15 minutos**.

## Índice de Documentación

A continuación, se detalla la arquitectura técnica de esta vulnerabilidad y los procedimientos de configuración para la aleatorización de MAC en diversos entornos de ejecución.

### Base Técnica

- **El fallo del portal cautivo** (`docs/teoria/01-el-fallo-del-portal-cautivo.md`): Análisis del ciclo de vida de la sesión de red y la ineficacia del filtrado MAC.
- **¿Qué es la aleatorización MAC?** (`docs/teoria/02-que-es-la-aleatorizacion-mac.md`): Implementación de protocolos de privacidad y mitigación de rastreo en la capa 2 por sistemas operativos contemporáneos.

### Procedimientos de Implementación

- Configuración en Android (`docs/tutoriales/01-android.md`)
- Configuración en iOS (iPhone/iPad) (`docs/tutoriales/02-ios.md`)

## Nota Legal y Descargo de Responsabilidad

La información divulgada en este repositorio tiene fines exclusivamente educativos y de investigación en ciberseguridad. El autor no asume responsabilidad alguna por la explotación de esta información con el propósito de evadir controles de acceso o infringir los términos de servicio de los proveedores de infraestructura de red.