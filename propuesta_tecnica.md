# Diseño técnico de una aplicación móvil multiplataforma para un contexto real

**Estudiante:** JUAN ETSEBAN SOSSA PARRA
**Asignatura:** Lenguaje de Computación para Móviles
**Unidad:** Unidad 3 — Desarrollo web multiplataforma orientado a dispositivos móviles
**Fecha:** 06 DE JUNIO de 2026
**Programa:** Ingeniería de Software / Tecnología en Implementación de Soluciones Digitales

**Enlace al video de sustentación:** *https://www.youtube.com/watch?v=VW1CsU2LaQQ*

---

## 1. Descripción del problema

Una institución educativa necesita mejorar la forma en que se comunica con sus estudiantes y hace seguimiento a las actividades académicas. Hoy la información viaja por varios canales distintos (correo, grupos de mensajería, carteleras, plataformas web), lo que genera tres problemas concretos: se pierden mensajes importantes, no hay forma clara de saber qué se entregó y qué no, y los estudiantes que tienen mala señal de internet quedan desconectados de la información.

La propuesta consiste en diseñar una **aplicación móvil única y centralizada** que permita a los estudiantes consultar sus actividades, recibir notificaciones, enviar evidencias con foto (y opcionalmente con la ubicación) y, lo más importante, **seguir consultando la información aunque no tengan conexión a internet en ese momento**.

### Público objetivo

Estudiantes de la institución educativa que usan, en su mayoría, **teléfonos Android de gama media o baja**, con planes de datos limitados y zonas donde la conectividad no siempre es estable.

### Escenarios principales de uso

1. Un estudiante en el bus o en un lugar sin señal abre la app y consulta las actividades pendientes que ya descargó antes.
2. Un estudiante hace un trabajo de campo y necesita tomar una foto como evidencia, registrar el lugar donde está y enviarla cuando le vuelva la señal.
3. Un estudiante recibe una notificación cuando se publica una nueva actividad o cuando se acerca una fecha de entrega.
4. Un coordinador publica una actividad desde el sistema central y todos los estudiantes la ven en su app casi de inmediato.

---

## 2. Historias de usuario priorizadas

Las historias están ordenadas de mayor a menor prioridad para el negocio.

| # | Prioridad | Historia de usuario |
|---|-----------|---------------------|
| HU-01 | Alta | **Como** estudiante, **quiero** consultar el listado de mis actividades académicas pendientes, **para** organizar mejor mi tiempo de estudio. |
| HU-02 | Alta | **Como** estudiante, **quiero** poder ver las actividades que ya descargué incluso cuando no tengo internet, **para** no depender de la conectividad para estar al día. |
| HU-03 | Alta | **Como** estudiante, **quiero** recibir una notificación cuando se publica una nueva actividad o cuando se acerca una fecha de entrega, **para** no olvidar mis compromisos académicos. |
| HU-04 | Alta | **Como** estudiante, **quiero** tomar una foto con la cámara desde la misma app y enviarla como evidencia, **para** registrar mis entregas de forma rápida y sin pasar por otras aplicaciones. |
| HU-05 | Media | **Como** estudiante, **quiero** que, cuando lo autorice, la app registre mi ubicación al enviar una evidencia, **para** validar actividades que requieren presencialidad o trabajo de campo. |
| HU-06 | Media | **Como** estudiante, **quiero** que mis evidencias se guarden en el teléfono si no hay conexión y se envíen automáticamente cuando vuelva el internet, **para** no perder el trabajo que ya hice. |
| HU-07 | Media | **Como** docente o coordinador, **quiero** publicar actividades desde el sistema central y que lleguen a la app, **para** mantener un único canal de comunicación con los estudiantes. |

---

## 3. Matriz comparativa de enfoques

Se comparan cuatro enfoques posibles para construir la solución usando criterios técnicos relevantes para el caso. 

### Tabla 1. Matriz comparativa de enfoques técnicos

| Criterio | PWA | Híbrida con Capacitor / Ionic | Nativa Android | Flutter |
|---|---|---|---|---|
| **Costo de desarrollo** | Muy bajo: una sola base de código web. | Bajo: se reutilizan conocimientos web. | Alto: equipo especializado en Kotlin/Java. | Medio: lenguaje propio (Dart) que hay que aprender. |
| **Reutilización de código** | Total para web y móvil. | Alta: el mismo código corre como web y como app instalable. | Nula entre Android e iOS. | Alta entre Android e iOS. |
| **Acceso a cámara y GPS** | Funciona, pero con limitaciones en algunos navegadores y permisos menos estables. | Bueno: plugins oficiales de Capacitor para Camera y Geolocation. | Excelente, acceso completo y directo. | Excelente, con plugins maduros. |
| **Funcionamiento offline** | Posible con Service Workers, pero limitado para datos complejos. | Muy bueno: SQLite, Ionic Storage y Service Workers combinados. | Excelente, control total del almacenamiento. | Excelente, con paquetes como `sqflite` o `hive`. |
| **Rendimiento en dispositivos de gama media/baja** | Depende del navegador; suele ser aceptable pero no óptimo. | Aceptable; el WebView de Android es suficiente para esta app. | El mejor rendimiento posible. | Muy bueno; se compila a código nativo. |
| **Facilidad de mantenimiento** | Muy alta: se actualiza desde el servidor. | Alta: un solo código, actualizaciones controladas. | Media: cada plataforma se mantiene por separado. | Alta: una sola base de código. |
| **Publicación e instalación** | Se "instala" desde el navegador, pero no aparece en Play Store de forma natural. | Se empaqueta como APK / AAB y se publica en Play Store. | Publicación nativa en Play Store. | Publicación nativa en Play Store. |
| **Limitaciones principales** | Permisos del dispositivo restringidos, dificultad para notificaciones push completas en iOS, menor visibilidad en la tienda. | Rendimiento ligeramente inferior a lo nativo y dependencia del WebView. | Costo y tiempo de desarrollo; duplicar para iOS. | Curva de aprendizaje de Dart y tamaño inicial del APK mayor. |

### Lectura de la matriz

Las dos opciones que mejor responden al caso son **Ionic con Capacitor** y **Flutter**. Las dos cumplen los requisitos funcionales, ofrecen buen soporte offline, acceso a cámara y GPS, y permiten publicar la app como instalable en Play Store. La PWA queda corta por las restricciones de permisos y de instalación. La opción nativa pura queda descartada por costo: la institución necesita una solución económica y de mantenimiento sencillo.

---

## 4. Selección tecnológica

### Enfoque seleccionado: **Aplicación híbrida con Ionic + Angular + Capacitor**

### Justificación

Se eligió este enfoque porque equilibra mejor que ningún otro las restricciones del contexto: presupuesto bajo, dispositivos modestos, conectividad limitada y la necesidad de mantener la app a largo plazo con un equipo pequeño.

A continuación se explica la decisión punto por punto, usando los criterios pedidos en la guía:

- **Reutilización de código.** Con Ionic se escribe una sola vez la aplicación y la misma base sirve para Android, iOS y web. Eso reduce esfuerzo y permite que, en el futuro, la institución pueda ofrecer la misma app desde un navegador sin reescribir nada.

- **Acceso a capacidades nativas.** Capacitor ofrece plugins oficiales y mantenidos para los dos requisitos sensibles del proyecto: cámara (`@capacitor/camera`) y ubicación (`@capacitor/geolocation`). Además incluye `@capacitor/push-notifications` para alertas y `@capacitor/network` para detectar el estado de la conexión, que es clave en este caso.

- **Rendimiento.** Para esta aplicación, que es principalmente consulta de listas, formularios sencillos y envío ocasional de fotos, el rendimiento del WebView moderno de Android es suficiente. No se trata de un juego ni de una app con animaciones complejas, por lo que ganar rendimiento nativo no compensa el costo extra.

- **Costo y tiempo de desarrollo.** Ionic permite aprovechar conocimientos de desarrollo web (HTML, CSS, TypeScript) que son los más fáciles de conseguir en el mercado y los más económicos de contratar. Esto reduce el tiempo de la primera versión y baja el costo de mantenimiento.

- **Facilidad de mantenimiento.** Una sola base de código quiere decir un solo lugar donde corregir errores y agregar funcionalidades. Angular, además, impone una estructura clara (módulos, servicios, componentes) que facilita que otra persona retome el proyecto más adelante.

- **Soporte offline.** Capacitor permite usar SQLite (a través de `@capacitor-community/sqlite`) para guardar datos estructurados, e Ionic Storage para datos sencillos clave-valor. Combinado con Service Workers, la app puede seguir funcionando sin internet y sincronizar cuando vuelva la conexión.

- **Escalabilidad.** Si más adelante la institución quiere lanzar la app en iOS, agregar nuevas funcionalidades o convertirla en PWA, el mismo proyecto lo soporta sin reescritura. Angular escala bien para proyectos que crecen con el tiempo.

### ¿Por qué no Flutter, que también es una buena opción?

Flutter compite muy de cerca con Ionic y, en otros contextos, podría ser la mejor elección. La razón principal para preferir Ionic es **la curva de aprendizaje y el costo de equipo**. En el contexto colombiano y latinoamericano hay muchos más desarrolladores con experiencia en HTML, CSS, JavaScript y Angular que en Dart. Para una institución educativa que probablemente no tiene un equipo de desarrollo grande, esto se traduce en menor costo de contratación y mayor facilidad para mantener la app en el tiempo.

---

## 5. Arquitectura mínima viable

La arquitectura se organiza en **cuatro capas** claramente separadas, lo que permite que cada parte de la aplicación tenga una responsabilidad bien definida.

REVISAR ARCHIVO DE Arquitectura

### Descripción de las capas

**1. Capa de Presentación (UI).**
Aquí viven las pantallas que ve el estudiante. Está construida con componentes de Ionic (botones, listas, tarjetas, formularios) y la lógica de cada pantalla se maneja con Angular. Las pantallas principales son:

- **Login:** ingreso del estudiante con sus credenciales institucionales.
- **Lista de actividades:** muestra las actividades académicas, marcando cuáles están pendientes y cuáles ya tienen evidencia enviada.
- **Detalle de actividad:** descripción, fecha límite y acciones disponibles.
- **Captura de evidencia:** acceso a la cámara y registro opcional de ubicación.
- **Centro de notificaciones:** historial de avisos recibidos.

**2. Capa de Lógica / Servicios.**
Son los "cerebros" de la app, escritos como servicios de Angular. Las pantallas no hablan directamente con la API ni con la base de datos: le piden las cosas a estos servicios.

- **AuthService:** maneja el inicio de sesión y guarda el token de seguridad.
- **ActividadService:** trae las actividades desde la API o desde el almacenamiento local según haya internet o no.
- **SyncService:** vigila la conexión y, cuando vuelve, envía las evidencias que quedaron pendientes.
- **NotificationService:** se encarga de registrar el dispositivo para recibir notificaciones y mostrarlas correctamente.

**3. Capa de Datos y Acceso al Dispositivo.**
Esta capa se divide en dos partes:

- **Almacenamiento local:** se usa **SQLite** (a través del plugin de Capacitor) para guardar las actividades descargadas y las evidencias pendientes de envío. Para datos pequeños como preferencias del usuario se usa **Ionic Storage**.
- **Capacitor Plugins:** son los puentes que permiten hablar con el hardware del teléfono. Se usarán los plugins oficiales de Camera, Geolocation, Push Notifications, Network (para detectar conexión) y Filesystem (para guardar imágenes).

**4. Backend y Servicios Externos.**
Para el ejercicio se simula un backend con **json-server** o un servicio sencillo en Node.js que expone los endpoints REST necesarios:

- `GET /actividades` — listado de actividades del estudiante.
- `GET /actividades/{id}` — detalle de una actividad.
- `POST /evidencias` — envío de una evidencia con imagen y datos.
- `POST /auth/login` — autenticación.

Las **notificaciones push** se manejan con **Firebase Cloud Messaging (FCM)**, que es gratuito para el volumen esperado y se integra naturalmente con Capacitor.

### Flujo offline y sincronización

Uno de los puntos más importantes de la propuesta es el comportamiento sin internet. El diagrama siguiente muestra cómo se resuelve.

REVISAR ARCHIVO DE Flujo-offline

La idea central es:

- **Cache-first al consultar:** cuando el estudiante abre la lista de actividades, primero se le muestra lo que ya está guardado en el teléfono y, en paralelo, se intenta actualizar desde la API. Así nunca ve una pantalla en blanco.
- **Cola de envíos:** cuando toma una foto y no hay internet, la evidencia se guarda en SQLite con estado *pendiente*. Cuando el `SyncService` detecta que volvió la conexión, envía automáticamente todo lo pendiente.

---

## 6. Consideraciones móviles

La propuesta responde de manera específica a las particularidades del entorno móvil.

### Conectividad limitada
La aplicación está pensada para **funcionar primero con lo local y consultar al servidor cuando se pueda**. Esto incluye guardar las actividades en SQLite al descargarlas y mantener una cola de envíos pendientes. El estudiante puede usar la app aunque pase una hora sin señal y, cuando vuelva, todo se sincronizará solo.

### Bajo consumo de datos
Las imágenes capturadas se **comprimen antes de enviarse** (calidad ajustable, máximo 1024 px de ancho por defecto). Las peticiones a la API solo traen lo nuevo desde la última sincronización, no todo el listado cada vez. Las notificaciones push usan FCM, que es eficiente en consumo.

### Rendimiento en dispositivos modestos
Se evitan animaciones pesadas y librerías que carguen mucho. Las listas largas usan **scroll virtual** (componente `ion-virtual-scroll` o equivalente) para no renderizar todos los elementos al tiempo. El APK final se mantiene por debajo de 25 MB usando solo las dependencias necesarias.

### Experiencia de usuario en pantallas pequeñas
Ionic ya trae componentes diseñados para móviles y se adaptan automáticamente al tamaño de pantalla. La interfaz se planea con **tipografía legible**, **botones grandes** y **contraste adecuado** para usarse a una mano. Se sigue el patrón de navegación que el usuario ya conoce en otras apps Android (pestañas inferiores y stack de navegación).

### Permisos del dispositivo
La app pide los permisos solo cuando los necesita y siempre **explica antes para qué los pide**. Por ejemplo, el permiso de ubicación se solicita en el momento de enviar una evidencia, no al abrir la app. Si el usuario lo niega, la app sigue funcionando (la ubicación es opcional).

### Seguridad básica de los datos
- Las comunicaciones con la API usan **HTTPS**.
- La autenticación se hace con **token JWT**, que se guarda en almacenamiento seguro (`@capacitor/preferences`, no en localStorage plano).
- Las imágenes y datos sensibles guardados localmente quedan en la zona privada de la app (no son accesibles para otras apps del teléfono).
- No se guardan contraseñas en el dispositivo.

---

## 7. Riesgos y limitaciones

### Tabla 2. Matriz de riesgos técnicos y estrategias de mitigación

| # | Riesgo | Impacto | Estrategia de mitigación |
|---|--------|---------|--------------------------|
| R-01 | **Fallos prolongados de conexión** | El estudiante no puede ver información actualizada ni enviar evidencias. | Almacenamiento local con SQLite e Ionic Storage. Cola de envíos pendientes que se sincronizan automáticamente al recuperar la conexión. Mostrar siempre el estado de conexión en la interfaz. |
| R-02 | **El usuario niega permisos de cámara o GPS** | Se bloquean funcionalidades clave de la app. | Diseñar la app para que **degrade con elegancia**: si no hay cámara, permitir seleccionar imagen de la galería; si no hay GPS, marcar la ubicación como opcional. Mostrar mensajes claros explicando para qué se pide cada permiso, con la opción de habilitarlos desde la configuración del sistema. |
| R-03 | **Bajo rendimiento o poca memoria en celulares muy antiguos** | La app puede ir lenta o cerrarse sola. | Definir como requisito mínimo Android 7.0 (API 24). Comprimir imágenes antes de mostrarlas y antes de enviarlas. Limitar el tamaño de la base de datos local (mantener solo las últimas 100 actividades, archivar el resto). Usar listas virtuales para no cargar todo en memoria. |
| R-04 | **Pérdida o robo del teléfono con datos académicos guardados** | Compromiso de información del estudiante. | Guardar datos sensibles únicamente en almacenamiento privado de la app. Token de sesión con expiración corta. Botón de "cerrar sesión" que borra los datos locales. No guardar contraseñas. |
| R-05 | **Cambios en la API del backend que rompen la app** | El estudiante recibe errores al usar la app después de una actualización del servidor. | Versionar la API (`/api/v1/...`) y mantener compatibilidad hacia atrás durante al menos una versión. Manejar errores en el cliente mostrando mensajes claros en lugar de pantallas en blanco. |

### Limitaciones conocidas de la propuesta

- El rendimiento de un proyecto en Ionic, aunque suficiente para este caso, **no iguala el de una app nativa pura**. Si en el futuro se agregan funciones muy exigentes (procesamiento de video en vivo, realidad aumentada, etc.), habría que revisar el enfoque.
- Para iOS, aunque el código es el mismo, la publicación en App Store requiere una cuenta de desarrollador de Apple con un costo anual. La propuesta inicial se enfoca en Android.
- Las notificaciones push dependen de **Google Play Services**, que en algunos dispositivos muy antiguos o sin servicios de Google podrían no estar disponibles.

---

## 8. Cierre

La propuesta responde al problema planteado con una solución **técnica, viable y económica**: una aplicación móvil construida con Ionic, Angular y Capacitor, organizada en cuatro capas, capaz de funcionar sin internet, integrarse con la cámara y el GPS del teléfono, y publicarse como app instalable en Android.

La decisión tecnológica no se tomó por moda ni por popularidad, sino por su ajuste a las restricciones del contexto: presupuesto bajo, dispositivos modestos, conectividad inestable y la necesidad de un mantenimiento sencillo a lo largo del tiempo.
