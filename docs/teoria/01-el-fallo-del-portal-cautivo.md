**Idiomas:** [Español] | [English](01-captive-portal-flaw.en.md)

---

# Análisis Teórico: Desvinculación de Sesión en Portales Cautivos mediante Capa 2

## Introducción

Los portales cautivos son mecanismos de control de acceso a nivel de red diseñados para interceptar el tráfico de usuarios no autenticados en redes de área local inalámbricas (WLAN). Este documento analiza, desde la perspectiva de la arquitectura de redes, la debilidad estructural que presentan los sistemas de control de acceso basados en el seguimiento exclusivo de identificadores de Capa 2 (dirección MAC).

## Arquitectura de un Portal Cautivo Tradicional

Un sistema de portal cautivo convencional involucra la interacción de diversos componentes de red en el modelo OSI:

1. **Punto de Acceso y Controlador WLAN:** Gestiona la asociación 802.11 y reenvía las tramas del cliente.
2. **Servidor DHCP:** Asigna direcciones IP dinámicas y mantiene una tabla de concesiones (leases) vinculadas a la dirección MAC del cliente.
3. **Servidor de Autenticación / Portal Cautivo:** Intercepta el tráfico HTTP/HTTPS no autorizado mediante redirección DNS o respuestas HTTP 302, sirviendo la interfaz web de acceso.
4. **Servidor de Acceso a la Red (NAS) / Cortafuegos:** Aplica las Listas de Control de Acceso (ACL) que permiten o bloquean el reenvío de paquetes hacia la red de área amplia (WAN).

## El Problema de la Identidad basada en la Capa de Enlace

El modelo de referencia OSI define la capa de enlace de datos como el nivel responsable de la entrega de tramas entre nodos dentro del mismo segmento de red local. Históricamente, las arquitecturas de portal cautivo empleaban la dirección MAC presente en la cabecera 802.11 como un identificador de sesión estático.

El flujo de administración de estado opera habitualmente bajo la siguiente lógica:

- **Estado No Autenticado:** La dirección MAC `AA:BB:CC:DD:EE:FF` no existe en la lista de clientes autorizados del NAS. Las peticiones salientes son interceptadas y redirigidas a la IP del portal.
- **Estado Autenticado:** Tras completar la condición de acceso (aceptación de políticas, temporizador de prueba o pago), el NAS registra la dirección MAC `AA:BB:CC:DD:EE:FF` en la tabla de estado permitido durante un periodo $T$.
- **Expiración de Estado:** Cumplido el tiempo $T$, el NAS elimina la dirección MAC de la lista de permitidos o la transfiere a una lista de bloqueo temporal.

## Análisis del Fallo de Diseño

La vulnerabilidad teórica de este esquema radica en la premisa errónea de que la dirección MAC es un identificador de hardware permanente e inmutable. 

En los sistemas operativos modernos, el subsistema de red gestiona el direccionamiento de Capa 2 mediante software. Cuando se habilita la aleatorización de MAC a nivel de sistema operativo:

1. La interfaz inalámbrica desasocia y vuelve a asociarse al Punto de Acceso.
2. El cliente envía un paquete `DHCP DISCOVER` utilizando una dirección MAC distinta en la cabecera Ethernet.
3. El controlador de red y el servidor DHCP procesan la solicitud como una entidad física totalmente independiente que no registra historial previo en las tablas de estado del NAS.
4. Al no existir la nueva dirección MAC en la lista de bloqueo ni en la lista de autorizados, la infraestructura aplica la regla por defecto para nuevos clientes, reiniciando la evaluación del portal cautivo.

## Consideraciones de Remediación y Diseño Seguro

Para evitar que los sistemas de control de acceso dependan de identificadores efímeros de Capa 2, la ingeniería de ciberseguridad recomienda implementar arquitecturas defensivas más robustas:

1. **Autenticación Basada en IEEE 802.1X:** Desplegar redes con cifrado WPA2/WPA3 Enterprise, donde la autenticación se realiza mediante credenciales criptográficas de usuario antes de otorgar acceso a la red de Capa 2.
2. **Vinculación de Sesión mediante Tokens Criptográficos:** Asociar la concesión del servicio a tokens de sesión firmados validados en el servidor, en lugar de confiar en direcciones MAC o IP.
3. **Reautenticación Estricta y Tiempos de Lease DHCP Reducidos:** Configurar políticas de expiración que requieran la reintroducción de credenciales de usuario independientemente de si la interfaz física se reconecta o modifica su dirección MAC.
4. **Inspección de Estado Multicapa:** Utilizar sistemas de detección que combinen múltiples atributos de red para identificar dispositivos independientemente de su dirección MAC local.