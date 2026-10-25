Plan de trabajo definitivo — Autenticación Full Stack Evolutiva
Versión: 1.0 — Planificación
Estado: Listo para revisión y aprobación final
Implementación: No iniciada
Instalación de dependencias: No iniciada
Enfoque: Desarrollo local, seguridad razonable, aprendizaje progresivo y arquitectura preparada para evolucionar.

1. Propósito del proyecto
El proyecto tiene dos objetivos inseparables.
Objetivo técnico
Construir un módulo de autenticación Full Stack que sea:
Seguro mediante controles concretos y verificables.
Sencillo, sin complejidad innecesaria.
Profesional, mantenible y reutilizable.
Modular, con responsabilidades bien separadas.
Evolutivo, preparado para incorporar nuevas capacidades.
Preparado arquitectónicamente para crecer y admitir múltiples servidores en el futuro, sin implementar esa infraestructura en el Nivel 1.
No se pretende garantizar protección frente a cualquier amenaza futura, sino aplicar buenas prácticas, probar los controles implementados y documentar sus limitaciones.
Objetivo educativo
Aprender a diseñar, construir, probar y mantener una aplicación Full Stack desde sus fundamentos, comprendiendo las decisiones técnicas y no limitándose a copiar código.
Cada fase seguirá el ciclo:
Predecir → ejecutar → diagnosticar → justificar → transferir → prevenir regresiones.
La implementación avanzará por etapas. No comenzaremos a programar las funciones de autenticación hasta cerrar las decisiones técnicas que condicionan la seguridad, el modelo de datos o las dependencias.

2. Stack tecnológico acordado
Frontend
React
TypeScript
Vite
Tailwind CSS
Responsabilidades: formularios, validación orientativa de la entrada, estados de interfaz, presentación de errores y comunicación con la API.
Backend
Node.js
Express
TypeScript
Zod
Responsabilidades: API REST, validación definitiva de solicitudes, lógica de autenticación, sesiones, controles de seguridad y manejo centralizado de errores.
Base de datos y persistencia
PostgreSQL
Prisma ORM
PostgreSQL almacenará las cuentas y las sesiones persistentes del servidor. Prisma gestionará el acceso a los datos de la aplicación.
La biblioteca y el adaptador específicos para el almacenamiento de sesiones se elegirán durante la Fase 0.
Pruebas y documentación
Vitest: pruebas unitarias.
Supertest: pruebas de integración HTTP y API.
OpenAPI/Swagger: contrato y documentación de la API.
Playwright: candidato para pruebas de extremo a extremo, pendiente de aprobación.
Herramientas y entorno
Ubuntu y terminal.
Git y GitHub.
npm.
Docker, únicamente si mejora la reproducibilidad del entorno local.
Las versiones concretas se comprobarán por compatibilidad antes de instalar las dependencias. No se asumirán como definitivas versiones que todavía no se hayan verificado.

3. Arquitectura general
La aplicación se organizará en frontend, backend, persistencia de usuarios y persistencia de sesiones.
Navegador
    |
    v
React + TypeScript + Vite + Tailwind
    |
    | Solicitudes HTTP, cookie y protección CSRF
    v
API REST de Express
    |
    +--> Validación con Zod
    |
    +--> Servicios de autenticación
    |
    +--> Middleware de sesión y seguridad
    |
    +--> Manejo centralizado de errores
    |
    +--> Prisma
            |
            v
        PostgreSQL
            |
            +--> Usuarios
            |
            +--> Sesiones del servidor
En desarrollo, frontend y backend podrán ejecutarse como procesos separados. Vite utilizará un proxy para dirigir las solicitudes /api al backend.
El objetivo de despliegue futuro será servir el frontend en / y la API en /api bajo el mismo dominio. Esta decisión no obliga a desplegar el sistema en producción durante el Nivel 1.
Principios arquitectónicos
La autenticación se verifica en el servidor.
La interfaz no sustituye los controles de autorización.
Las contraseñas nunca se almacenan en texto plano.
La cookie contiene únicamente el identificador de sesión, no el estado completo de autenticación.
La persistencia de usuarios y sesiones tiene responsabilidades diferenciadas.
Los secretos y credenciales reales no se guardan en Git.
Los fallos de dependencias esenciales no conceden acceso.
Las decisiones técnicas importantes se documentan junto con sus alternativas y justificación.
La aplicación se desarrollará localmente, sin introducir infraestructura distribuida innecesaria.

4. Alcance funcional del Nivel 1
El Nivel 1 construirá el ciclo básico de registro, inicio de sesión, consulta de identidad, acceso protegido y cierre de sesión.
4.1 Registro
El formulario solicitará exclusivamente:
Correo electrónico.
Contraseña.
Confirmación de contraseña.
La confirmación se validará en el frontend y no se almacenará ni se enviará como un dato persistente innecesario.
El registro será público y no iniciará sesión automáticamente.
4.2 Contraseñas
La política funcional acordada será:
Longitud objetivo admitida: de 15 a 128 caracteres.
Admitir Unicode.
No truncar contraseñas silenciosamente.
Aplicar un límite técnico de bytes para prevenir entradas excesivas, una vez aprobado su valor exacto.
Eliminar los espacios en blanco iniciales y finales antes de procesar la contraseña, tanto en registro como en inicio de sesión.
Conservar los espacios internos.
Aplicar exactamente la misma regla en ambos flujos.
Mostrar claramente en la interfaz las características y reglas de contraseña antes de que el usuario envíe el formulario.
La implementación deberá definir cómo se cuentan los caracteres Unicode, qué espacios se consideran iniciales o finales y cómo se aplica el límite de bytes. Estas precisiones técnicas se documentarán antes de implementar el validador.
El hash se generará con Argon2id. Sus parámetros concretos se elegirán después de revisar las recomendaciones técnicas y medir el rendimiento del entorno.
4.3 Correo electrónico
Se establecerá una función común de normalización para registro e inicio de sesión.
La política deberá especificar el tratamiento de espacios externos, la comparación sin distinción de mayúsculas y minúsculas y las restricciones de unicidad. No se aplicarán transformaciones específicas de proveedores de correo, como eliminar puntos o sufijos +, sin un requisito explícito.
PostgreSQL impondrá la unicidad correspondiente para evitar cuentas duplicadas incluso ante registros concurrentes.
4.4 Inicio de sesión
El usuario enviará correo y contraseña. El backend verificará las credenciales y, si son válidas, creará una sesión nueva y confirmará su persistencia antes de responder con éxito.
Los errores de autenticación serán genéricos: no revelarán si un correo está registrado.
4.5 Sesiones simultáneas
Un usuario podrá mantener sesiones independientes en varios navegadores o dispositivos.
Cerrar una sesión invalidará únicamente esa sesión, sin cerrar automáticamente las demás.
No se implementará la función «Recordarme» en el Nivel 1. La cookie será una cookie de sesión del navegador, sin persistencia deliberada entre cierres normales del navegador. La expiración y la autorización siempre se comprobarán en el servidor.
4.6 Cierre de sesión
El backend deberá destruir la sesión persistida y limpiar la cookie correspondiente.
Si no se confirma la destrucción de la sesión, no se informará que la revocación fue exitosa. La aplicación deberá tratar el fallo de forma segura y mostrar una respuesta apropiada.
4.7 Recurso protegido
Se incluirá un recurso de demostración accesible únicamente con una sesión válida. Su finalidad será comprobar que la autorización depende del backend y no de la interfaz.

5. Contrato de la API
Método y ruta
Función
Respuesta exitosa
POST /api/auth/register
Registrar una cuenta sin iniciar sesión automáticamente
201 Created
POST /api/auth/login
Validar credenciales y crear una sesión
200 OK
POST /api/auth/logout
Destruir la sesión actual
204 No Content
GET /api/auth/me
Obtener la identidad autenticada
200 OK
GET /api/protected
Demostrar el acceso condicionado a una sesión válida
200 OK

El contrato se documentará con OpenAPI y se verificará mediante pruebas automatizadas.
Códigos HTTP acordados
Código
Uso
400
Entrada inválida o solicitud malformada
401
Credenciales inválidas o ausencia de sesión válida
403
Fallo de CSRF u otro control de seguridad aplicable
409
Correo ya registrado
429
Límite de solicitudes superado
500
Error interno genérico
503
Dependencia esencial no disponible

Los errores tendrán una estructura uniforme, un código seguro y un mensaje comprensible. Los detalles de validación se incluirán cuando corresponda, sin exponer secretos, hashes, consultas SQL ni información interna sensible.
El registro podrá responder 409 si el correo ya existe. En cambio, el inicio de sesión utilizará mensajes genéricos para no revelar si una cuenta existe.

6. Políticas de seguridad
6.1 Hash de contraseñas
Se utilizará Argon2id con parámetros documentados y medidos antes de la implementación definitiva.
Las contraseñas originales nunca se guardarán en la base de datos, los registros de aplicación ni los mensajes de error.
6.2 Sesiones del lado del servidor
Las sesiones se almacenarán en PostgreSQL mediante un adaptador seleccionado durante la Fase 0. No se utilizarán JWT como sustituto de la sesión persistida para este proyecto.
La solución deberá admitir:
Persistencia entre reinicios del backend mientras la sesión siga vigente.
Identificadores nuevos al autenticar.
Revocación de la sesión cerrada.
Expiración validada por el servidor.
Rechazo de sesiones que no puedan verificarse.
Tratamiento seguro de errores de lectura, escritura y eliminación.
La elección de biblioteca, adaptador y estrategia de migraciones deberá preceder al diseño definitivo del almacenamiento de sesiones.
6.3 Expiración de sesiones
Se aplicarán dos límites independientes:
Inactividad: 30 minutos sin actividad válida.
Duración absoluta: 12 horas desde la creación de la sesión, sin extensión por actividad.
La aplicación comprobará ambos límites al autorizar solicitudes. La eliminación física de registros vencidos será una tarea de mantenimiento independiente y no el mecanismo principal de seguridad.
6.4 Cookie de sesión
Atributos acordados:
HttpOnly
SameSite=Lax
Path=/
Sin Domain explícito inicialmente.
Secure=false únicamente en desarrollo local con HTTP.
Secure=true en producción con HTTPS.
La cookie no se guardará en localStorage ni contendrá datos de autenticación completos.
6.5 Protección CSRF
La arquitectura incluirá protección CSRF explícita para las solicitudes que cambien el estado, incluyendo el flujo de inicio de sesión.
La estrategia concreta, el mecanismo de obtención del token, las solicitudes protegidas y la rotación de sesión se documentarán y aprobarán durante la Fase 0.
CORS no se considerará un sustituto de la protección CSRF. Las operaciones que cambien el estado no se implementarán mediante solicitudes GET.
6.6 Limitación de intentos
Se utilizará express-rate-limit para limitar los intentos de inicio de sesión a:
10 solicitudes por dirección IP cada 15 minutos.
Al superar el límite, se responderá 429. No se implementará un bloqueo permanente de cuentas.
Para desarrollo local con una sola instancia podrá evaluarse un almacén en memoria, documentando sus límites. Si el sistema evoluciona a varias instancias, se deberá revisar la estrategia de almacenamiento compartido.
La interpretación de la IP dependerá de una configuración explícita y justificada de trust proxy.
6.7 CORS y configuración por entorno
Se definirá la política CORS según los orígenes reales de desarrollo y el objetivo de despliegue. No se habilitará una política permisiva indiscriminadamente.
La configuración del proxy confiable, cookies y orígenes permitidos deberá ser explícita y distinta cuando las condiciones de desarrollo y producción lo requieran.
6.8 Fallos de persistencia
Si PostgreSQL o el almacén de sesiones no están disponibles, la aplicación fallará de forma segura.
No deberá crear una identidad autenticada ficticia, conceder acceso por no poder verificar una sesión ni informar como exitosa una operación de persistencia que no se haya confirmado.
Cuando corresponda, se utilizará 503 Service Unavailable.
6.9 Secretos y entorno
Las credenciales reales se guardarán en variables de entorno locales.
Se mantendrá un archivo .env.example sin secretos reales.
Los archivos de entorno sensibles se excluirán de Git.
Las pruebas verificarán que los errores no revelan información interna.
La configuración de producción exigirá HTTPS.

7. Funciones fuera del Nivel 1
Las siguientes capacidades quedan reservadas para una evolución posterior, de acuerdo con los requisitos futuros:
Verificación de correo electrónico.
Recuperación y cambio de contraseña.
MFA, TOTP, códigos de recuperación y passkeys.
Función «Recordarme».
Panel de dispositivos y administración de sesiones.
Roles y permisos.
Multi-organización.
Auditoría y monitorización avanzadas.
Defensas antifraude avanzadas.
Escalamiento horizontal efectivo y almacenes compartidos para rate limiting.
Despliegue e infraestructura de producción.
Funciones adicionales que no sean necesarias para demostrar el ciclo básico de autenticación.
Mantener estas funciones fuera del alcance inicial no impide diseñar módulos con responsabilidades claras que puedan evolucionar más adelante.

8. Plan de trabajo por fases
Las fases de construcción comenzarán una vez cerradas las decisiones bloqueantes de la Fase 0. Cada fase tendrá entregables, pruebas, criterios de aceptación y una comprobación de aprendizaje.
Fase 0 — Cierre técnico previo a la implementación
Objetivo técnico: resolver las decisiones que afectan la arquitectura, la seguridad, los datos y las dependencias.
Objetivo educativo: aprender a evaluar alternativas y convertir requisitos en especificaciones implementables.
Actividades:
Comparar bibliotecas de sesiones y adaptadores PostgreSQL.
Revisar compatibilidad, mantenimiento, dependencias y comportamiento ante fallos.
Seleccionar la estrategia de migraciones de la tabla de sesiones.
Definir los parámetros de Argon2id mediante referencias técnicas y mediciones.
Precisar la política de contraseñas: Unicode, espacios, longitud y límite de bytes.
Documentar la normalización del correo y la prevención de registros duplicados concurrentes.
Elegir el mecanismo CSRF y definir la protección del inicio de sesión.
Formalizar la expiración de sesiones y el procedimiento de limpieza.
Definir cookies, CORS, trust proxy y rate limiting por entorno.
Completar la matriz de fallos de persistencia y el contrato de errores.
Evaluar Playwright como herramienta E2E y solicitar aprobación.
Verificar las versiones compatibles del stack.
Registrar las decisiones arquitectónicas importantes, alternativas y justificaciones.
Entregables:
Matriz de decisiones.
Contrato HTTP.
Política de contraseñas y de correo.
Diagrama del ciclo de vida de una sesión.
Matriz de errores y fallos.
Estrategia de persistencia y migraciones.
Matriz de pruebas.
Registro de riesgos y decisiones arquitectónicas.
Criterios de aceptación:
No quedan decisiones bloqueantes sin resolver.
Las decisiones aprobadas están diferenciadas de las propuestas pendientes.
Cada requisito de seguridad tiene una prueba o método de verificación.
Las versiones seleccionadas son compatibles.
Hito Git: planning-approved.
Fase 1 — Entorno y estructura del proyecto
Objetivo técnico: preparar un entorno local reproducible.
Actividades:
Verificar Node.js, npm, PostgreSQL y herramientas disponibles.
Crear la estructura de frontend y backend en TypeScript.
Configurar React, Vite, Tailwind y Express.
Preparar PostgreSQL; utilizar Docker solo si aporta reproducibilidad.
Configurar variables de entorno y .env.example.
Configurar las exclusiones de Git.
Configurar el proxy de Vite para /api.
Preparar Vitest y Supertest.
Ejecutar una prueba mínima de comunicación.
Entregables: estructura inicial, configuración documentada y guía de arranque.
Criterios de aceptación:
Frontend y backend arrancan de forma independiente.
El backend verifica la conexión a PostgreSQL.
El proxy permite alcanzar la API.
Las pruebas se ejecutan.
No hay secretos reales en Git.
Hito Git: environment-ready.
Fase 2 — Modelo de datos y persistencia
Objetivo técnico: definir el modelo de usuarios y el almacenamiento de sesiones.
Actividades:
Diseñar el modelo de usuario.
Definir campos necesarios y restricciones.
Configurar Prisma y las migraciones.
Aplicar la política de unicidad y normalización del correo.
Crear el almacenamiento de sesiones conforme al adaptador seleccionado.
Definir índices y relaciones necesarios.
Probar persistencia, restricciones y errores.
Entregables: esquema de datos, migraciones y pruebas de persistencia.
Criterios de aceptación:
La base de datos se puede crear desde un entorno limpio.
La unicidad se aplica en la base de datos.
Las migraciones son reproducibles.
Los fallos de persistencia se identifican y gestionan.
No se almacenan contraseñas en texto plano.
Hito Git: data-ready.
Fase 3 — Registro y validación
Objetivo técnico: implementar el registro público sin iniciar sesión automáticamente.
Actividades:
Definir los esquemas de entrada con Zod.
Validar correo, contraseña y confirmación.
Normalizar el correo mediante una función compartida.
Aplicar la política documentada de espacios en la contraseña.
Validar límites de caracteres y bytes.
Generar el hash Argon2id.
Persistir el usuario.
Convertir conflictos de unicidad en 409.
Documentar las respuestas y mostrar las reglas de contraseña en el frontend.
Entregables: endpoint de registro, formulario, validaciones, pruebas y documentación.
Criterios de aceptación:
El registro válido devuelve 201.
La entrada inválida devuelve 400.
El correo duplicado devuelve 409.
Los registros concurrentes no crean dos cuentas.
La confirmación no se almacena.
El hash se genera con la configuración aprobada.
La contraseña no se trunca.
El fallo de PostgreSQL no se presenta como éxito.
La interfaz comunica las reglas de contraseña.
Hito Git: registration-ready.
Fase 4 — Inicio de sesión y sesiones persistentes
Objetivo técnico: verificar credenciales y crear sesiones seguras.
Actividades:
Validar la entrada y normalizar el correo.
Aplicar la misma política de contraseña usada en el registro.
Buscar la cuenta y verificar Argon2id.
Devolver errores genéricos para credenciales inválidas.
Aplicar el límite de 10 intentos por IP cada 15 minutos.
Completar la protección CSRF del inicio de sesión.
Rotar el identificador al autenticar.
Persistir la sesión y confirmar la escritura antes de responder éxito.
Configurar la cookie y los límites de expiración.
Entregables: endpoint de login, configuración de sesión y pruebas de seguridad.
Criterios de aceptación:
Las credenciales válidas crean una sesión persistida.
Las credenciales inválidas producen una respuesta genérica.
El identificador de sesión cambia al autenticar.
La cookie contiene los atributos acordados.
La sesión sobrevive a un reinicio del backend mientras siga vigente.
Se aplican los límites de inactividad y duración absoluta.
Se devuelve 429 al superar el límite.
Un fallo del almacén no produce una respuesta de autenticación exitosa.
Hito Git: sessions-ready.
Fase 5 — Identidad, cierre de sesión y recurso protegido
Objetivo técnico: completar el ciclo de vida de la sesión y demostrar autorización del lado del servidor.
Actividades:
Implementar GET /api/auth/me.
Crear middleware para verificar sesiones válidas.
Implementar GET /api/protected.
Implementar el cierre de sesión.
Confirmar la destrucción de la sesión persistida.
Rechazar sesiones vencidas, revocadas o no verificables.
Gestionar de forma segura los fallos del almacén.
Entregables: endpoints de identidad, logout y recurso protegido.
Criterios de aceptación:
Una sesión válida obtiene la identidad correcta.
Una sesión ausente o inválida obtiene 401.
El recurso protegido no es accesible sin una sesión válida.
Tras el logout, la cookie anterior deja de autorizar.
La expiración por inactividad y la absoluta se comprueban por separado.
Un fallo de verificación no concede acceso.
Un fallo al destruir la sesión no se presenta como revocación confirmada.
Hito Git: auth-flow-ready.
Fase 6 — Interfaz y experiencia de usuario
Objetivo técnico: completar una interfaz coherente para registro, login, identidad y acceso protegido.
Actividades:
Crear los formularios de registro e inicio de sesión.
Mostrar las reglas de contraseña antes del envío.
Implementar validaciones de interfaz sin depender exclusivamente de ellas.
Mostrar estados de carga y errores comprensibles.
Consultar la identidad mediante /api/auth/me.
Gestionar estados de sesión sin almacenar credenciales en localStorage.
Mostrar el recurso protegido únicamente cuando corresponda.
Gestionar correctamente el cierre de sesión y los fallos de red.
Entregables: interfaz funcional, estados de carga y error, y pruebas de componentes.
Criterios de aceptación:
La interfaz comunica las reglas de contraseña.
Los mensajes son comprensibles y no revelan información sensible.
El frontend no decide por sí solo si el usuario está autorizado.
Una sesión inválida no mantiene acceso aparente al recurso protegido.
La interfaz funciona con los cinco endpoints acordados.
Hito Git: frontend-ready.
Fase 7 — Integración, pruebas y endurecimiento
Objetivo técnico: comprobar el comportamiento conjunto y los controles de seguridad.
Actividades:
Ejecutar pruebas unitarias e integración con Vitest y Supertest.
Probar registro, login, identidad, recurso protegido y logout.
Probar CSRF y login CSRF.
Verificar los atributos de las cookies.
Probar expiración por inactividad y duración absoluta.
Probar sesiones simultáneas e independencia del logout.
Simular fallos de lectura, escritura y eliminación de sesiones.
Simular indisponibilidad de PostgreSQL.
Probar rate limiting y códigos de error.
Incorporar Playwright para E2E si se aprueba en la Fase 0.
Corregir regresiones y documentar limitaciones conocidas.
Entregables: suite de pruebas, matriz de resultados y registro de defectos corregidos.
Criterios de aceptación:
Los flujos funcionales están cubiertos.
Los fallos críticos no conceden acceso.
Las pruebas de expiración y revocación pasan.
Las respuestas HTTP coinciden con el contrato.
Los secretos no aparecen en registros ni errores.
Los fallos conocidos quedan documentados.
Hito Git: security-tested.
Fase 8 — Documentación y versión 1.0.0
Objetivo técnico: entregar un proyecto reproducible, documentado y verificable.
Actividades:
Actualizar el README con la arquitectura y el alcance reales.
Documentar requisitos previos, instalación y arranque.
Documentar las variables de entorno.
Explicar las migraciones y preparación de PostgreSQL.
Documentar los endpoints y códigos HTTP.
Documentar las pruebas y cómo ejecutarlas.
Registrar decisiones arquitectónicas y limitaciones conocidas.
Verificar el procedimiento desde un entorno limpio.
Preparar el cierre de la versión.
Entregables: README final, documentación de API, instrucciones de ejecución, decisiones técnicas y resultados de pruebas.
Criterios de aceptación:
Una persona puede preparar el entorno siguiendo la documentación.
Las migraciones se ejecutan desde una base de datos limpia.
Las pruebas documentadas se pueden ejecutar.
README y plan no se contradicen.
No quedan secretos reales en el repositorio.
El alcance implementado coincide con el alcance declarado.
Hito Git: v1.0.0.

9. Decisiones aprobadas y pendientes
Aprobadas
Stack general: React, TypeScript, Vite, Tailwind, Node.js, Express, PostgreSQL, Prisma, Zod, Vitest, Supertest y OpenAPI/Swagger.
Registro público con correo, contraseña y confirmación.
No iniciar sesión automáticamente después del registro.
Hash de contraseñas con Argon2id.
Sesiones del lado del servidor persistidas en PostgreSQL; no JWT para el Nivel 1.
Sesiones simultáneas e independientes por dispositivo o navegador.
Sin función «Recordarme» en el Nivel 1.
Expiración por inactividad de 30 minutos y duración absoluta de 12 horas.
Cookies HttpOnly, SameSite=Lax, Path=/, sin Domain explícito inicialmente y Secure según el entorno.
Protección CSRF explícita, incluida la revisión del login CSRF.
Límite de 10 intentos de login por IP cada 15 minutos, con respuesta 429.
No bloquear permanentemente cuentas por intentos fallidos.
Fallo seguro cuando PostgreSQL o el almacén de sesiones no estén disponibles.
No informar un logout como exitoso si no se confirma la destrucción de la sesión.
Códigos HTTP y tratamiento general de errores definidos.
Meta futura de servir frontend y API bajo el mismo dominio, con API en /api.
La interfaz debe mostrar las características y reglas de contraseña.
Política funcional de espacios: eliminar espacios iniciales y finales, conservar los internos y aplicar la misma regla en registro y login.
Pendientes de resolución técnica
Estos puntos no son contradicciones del alcance; requieren investigación, comprobación o aprobación antes de implementar:
Biblioteca de sesiones y adaptador PostgreSQL.
Propiedad y estrategia de migraciones del almacenamiento de sesiones.
Parámetros concretos de Argon2id.
Límite preventivo de bytes para contraseñas y reglas exactas de conteo Unicode.
Definición técnica precisa de la normalización del correo.
Mecanismo CSRF, protección de login CSRF y flujo de pre-sesión.
Configuración exacta de CORS y trust proxy.
Intervalo y procedimiento de limpieza física de sesiones.
Herramienta E2E: evaluar Playwright y aprobar su incorporación.
Versiones concretas de las dependencias compatibles entre sí.
No se instalarán dependencias ni se comenzará la construcción de las funciones de autenticación hasta cerrar las decisiones bloqueantes.

10. Evolución posterior al Nivel 1
Una vez completado y probado el módulo básico, se podrán evaluar nuevas etapas según necesidades reales:
Verificación de correo.
Recuperación y cambio de contraseña.
MFA y passkeys.
Gestión de sesiones y dispositivos.
Roles y permisos.
Auditoría y monitorización.
Limitación de intentos con almacenamiento compartido.
Despliegue con HTTPS y configuración de producción.
Escalamiento horizontal y despliegue en varios servidores.
Cada ampliación deberá incorporar sus propios requisitos, decisiones, pruebas y criterios de aceptación. No se añadirán de manera anticipada funciones que no sean necesarias para completar el Nivel 1.

11. Criterio general de finalización
El proyecto se considerará terminado en su versión 1.0.0 cuando:
Los cinco endpoints funcionen según el contrato.
Registro, login, consulta de identidad, recurso protegido y logout estén integrados.
Las sesiones sean persistentes, revocables y expiren correctamente.
Los controles de seguridad acordados tengan pruebas verificables.
Los fallos de persistencia se gestionen de forma segura.
El entorno pueda prepararse desde cero siguiendo la documentación.
Las pruebas automatizadas estén disponibles y sus resultados documentados.
README, decisiones técnicas y plan de trabajo describan el mismo sistema.
Las limitaciones y las funciones futuras estén identificadas sin presentarlas como capacidades ya implementadas.
Principio rector: construir primero un sistema pequeño que funcione correctamente, entender por qué funciona y demostrar mediante pruebas que los controles importantes se mantienen ante errores y casos límite. Después, evolucionarlo de forma deliberada.




