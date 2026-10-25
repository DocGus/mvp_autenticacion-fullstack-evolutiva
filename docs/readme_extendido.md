# Proyecto 01 — Autenticación Full Stack Evolutiva

**Programa:** Biblioteca de módulos Full Stack reutilizables  
**Módulo actual:** 01 — Autenticación (*Authentication*)  
**Estado:** Planificación definida; decisiones técnicas de la Fase 0 pendientes de cierre  
**Implementación:** No iniciada  
**Dependencias del proyecto:** Aún no instaladas  
**Stack principal:** React, TypeScript, Vite, Tailwind CSS, Node.js, Express, PostgreSQL y Prisma  
**Metodología:** Aprendizaje progresivo, desarrollo modular, pruebas verificables y documentación técnica.

---

## 1. Visión general del programa

Este repositorio forma parte de un programa de aprendizaje cuyo objetivo es construir una biblioteca de módulos Full Stack reutilizables para integrarlos posteriormente en distintas aplicaciones web.

En lugar de desarrollar ejercicios aislados, construiremos módulos funcionales de extremo a extremo. Cada módulo permitirá aprender y practicar frontend, backend, bases de datos, seguridad, arquitectura, pruebas y documentación.

El primer módulo será Autenticación. A partir de sus fundamentos desarrollaremos otras capacidades y aprenderemos a integrarlas en aplicaciones completas.

La meta es construir software seguro, profesional, mantenible y evolutivo, sin introducir complejidad innecesaria ni asumir que podemos anticipar todas las amenazas futuras.

### Principios del programa

- Aprender mediante módulos funcionales de extremo a extremo.
- Construir componentes reutilizables, no ejercicios desechables.
- Separar responsabilidades entre frontend, backend y persistencia.
- Incorporar seguridad desde el diseño y verificarla mediante pruebas.
- Preferir contratos claros, dependencias compatibles y migraciones reproducibles.
- Conservar versiones funcionales, probadas y documentadas.
- Avanzar de los fundamentos a las capacidades especializadas.
- Resolver las decisiones que bloquean una fase antes de comenzar su implementación.
- Evitar tanto la sobreingeniería como las decisiones que obliguen a rehacer trabajo innecesariamente.

---

## 2. Mapa general de módulos

El programa contempla inicialmente seis módulos.

### 01. Autenticación — Authentication

**Objetivo:** gestionar de forma segura el acceso de los usuarios a una aplicación.

Capacidades previstas a lo largo de su evolución:

- Registro de cuentas.
- Inicio y cierre de sesión.
- Hash seguro de contraseñas.
- Gestión y persistencia de sesiones.
- Protección de recursos privados.
- Recuperación de acceso en una etapa posterior.
- Incorporación progresiva de medidas de seguridad adicionales.

### 02. Perfiles de usuario — User Profiles

**Objetivo:** administrar la información descriptiva asociada a los usuarios, separándola de sus credenciales de autenticación.

### 03. Gestión de datos — Data Management

**Objetivo:** aprender a crear, consultar, actualizar y eliminar registros, aplicando validaciones, reglas de negocio y persistencia.

### 04. Navegación y panel principal — App Shell

**Objetivo:** establecer una estructura compartida de páginas, layouts, navegación y rutas.

### 05. Roles y permisos — Authorization

**Objetivo:** controlar qué acciones puede realizar cada usuario sobre los recursos de una aplicación.

La autenticación identifica al usuario; la autorización determina qué puede hacer.

### 06. Búsqueda, filtros y paginación

**Objetivo:** consultar colecciones de datos de manera organizada y eficiente.

Los módulos tendrán contratos y responsabilidades documentados. Cada aplicación podrá integrar los que necesite, siempre que sus dependencias estén satisfechas.

---

## 3. Evolución del programa y del módulo de autenticación

Son dos hojas de ruta relacionadas, pero diferentes.

### Evolución del programa Full Stack

1. Autenticación.
2. Perfiles de usuario.
3. Gestión de datos.
4. Navegación y panel principal.
5. Roles y permisos.
6. Búsqueda, filtros y paginación.

### Evolución interna de Autenticación

1. Autenticación fundamental.
2. Gestión de cuentas y recuperación.
3. Autenticación reforzada.
4. Integración con autorización.
5. Multi-organización.
6. Operación, endurecimiento y preparación para producción.

El módulo de autenticación no asumirá toda la lógica de negocio de las aplicaciones futuras. Su responsabilidad inicial será establecer los fundamentos de identidad y acceso.

---

## 4. Alcance del Nivel 1 — Autenticación fundamental

El Nivel 1 construirá un flujo funcional de autenticación de extremo a extremo.

### Funcionalidades incluidas

- Registro público mediante correo electrónico, contraseña y confirmación de contraseña.
- Validación de la confirmación en el frontend; no se almacenará.
- Hash de contraseñas mediante Argon2id.
- Inicio y cierre de sesión.
- Sesiones administradas por el servidor y persistidas en PostgreSQL.
- Sesiones simultáneas independientes en distintos navegadores y dispositivos.
- Consulta de identidad autenticada.
- Acceso a un endpoint protegido.
- Integración del frontend con la API.
- Validación de entradas y manejo uniforme de errores.
- Protección CSRF, configuración de cookies y limitación de intentos.
- Pruebas automatizadas de funcionamiento, persistencia y seguridad.
- Documentación técnica y educativa.
- Hitos de Git y etiqueta `v1.0.0` cuando se cumplan todos los criterios de aceptación.

### Endpoints previstos

| Método y ruta | Función | Respuesta exitosa |
|---|---|---|
| `POST /api/auth/register` | Registrar una cuenta sin iniciar sesión automáticamente | `201 Created` |
| `POST /api/auth/login` | Verificar credenciales y crear una sesión | `200 OK` |
| `POST /api/auth/logout` | Destruir la sesión actual | `204 No Content` |
| `GET /api/auth/me` | Obtener la identidad autenticada | `200 OK` |
| `GET /api/protected` | Demostrar el acceso condicionado a una sesión válida | `200 OK` |

La API será la autoridad para autorizar el acceso. Ocultar una pantalla en React no constituye protección del recurso.

### Registro y política de contraseñas

El formulario solicitará exclusivamente:

- Correo electrónico.
- Contraseña.
- Confirmación de contraseña.

La política acordada para la contraseña contempla:

- Longitud admitida de 15 a 128 caracteres.
- Compatibilidad con Unicode.
- Eliminación de espacios en blanco iniciales y finales antes del procesamiento.
- Conservación de los espacios internos.
- Aplicación de la misma política en registro e inicio de sesión.
- Prohibición de truncar silenciosamente la contraseña.
- Comunicación visible de las reglas de contraseña en el frontend.

Antes de implementar el validador se precisarán el conteo de caracteres Unicode, la definición de espacios y el límite técnico de bytes. El límite concreto de bytes todavía requiere aprobación.

Las contraseñas se almacenarán únicamente como hashes Argon2id, con parámetros técnicos seleccionados y medidos antes de la implementación.

### Correo electrónico

Se definirá una función común de normalización para registro e inicio de sesión.

La política contemplará la eliminación de espacios externos, una comparación sin distinción de mayúsculas y minúsculas y una restricción de unicidad en PostgreSQL.

No se aplicarán transformaciones específicas de proveedores de correo, como eliminar puntos o sufijos `+`, sin un requisito explícito.

### Sesiones

- Las sesiones se administrarán en el servidor y se persistirán en PostgreSQL.
- Se permitirán sesiones simultáneas e independientes por usuario.
- Cerrar una sesión invalidará únicamente esa sesión.
- No se implementará «Recordarme» en el Nivel 1.
- La cookie será de sesión del navegador, sin persistencia deliberada entre cierres normales.
- Expiración por inactividad: 30 minutos.
- Duración absoluta máxima: 12 horas, no renovable por actividad.
- La autorización y la expiración se comprobarán en el servidor.
- La limpieza física de sesiones vencidas será independiente de la expiración lógica.

La biblioteca y el adaptador de sesiones todavía deben evaluarse y aprobarse.

### Cookies, CSRF y limitación de intentos

Los atributos acordados para la cookie son:

- `HttpOnly`
- `SameSite=Lax`
- `Path=/`
- Sin `Domain` explícito inicialmente.
- `Secure=false` únicamente en desarrollo local con HTTP.
- `Secure=true` en producción con HTTPS.

La arquitectura incorporará protección CSRF explícita, incluida la protección del inicio de sesión. CORS no se considerará un sustituto de CSRF.

El inicio de sesión tendrá un límite de **10 solicitudes por dirección IP cada 15 minutos**. Al superar el límite, se responderá `429`. No se implementará un bloqueo permanente de cuentas.

La configuración de CORS, `trust proxy` y el almacenamiento del rate limiting se concretará según el entorno.

### Fallos de persistencia

Si PostgreSQL o el almacén de sesiones no están disponibles, la aplicación fallará de forma segura.

No se concederá acceso cuando no se pueda verificar una sesión ni se informará una operación como exitosa si no se ha confirmado la persistencia necesaria. Cuando corresponda, se utilizará `503 Service Unavailable`.

### Códigos HTTP previstos

| Código | Uso |
|---|---|
| `400` | Entrada inválida o solicitud malformada |
| `401` | Credenciales inválidas o ausencia de sesión válida |
| `403` | Fallo CSRF u otro control de seguridad aplicable |
| `409` | Correo ya registrado |
| `429` | Límite de solicitudes superado |
| `500` | Error interno genérico |
| `503` | Dependencia esencial no disponible |

El registro podrá revelar que un correo ya existe mediante `409`. Los errores de inicio de sesión serán genéricos para no revelar si una cuenta existe.

---

## 5. Stack tecnológico

### Frontend

- React
- TypeScript
- Vite
- Tailwind CSS

### Backend

- Node.js
- Express
- TypeScript
- Zod

### Base de datos y persistencia

- PostgreSQL
- Prisma ORM
- Biblioteca y adaptador de sesiones PostgreSQL por seleccionar

### Pruebas y documentación

- Vitest
- Supertest
- OpenAPI/Swagger
- Playwright como candidato para pruebas E2E, pendiente de aprobación

### Herramientas y entorno

- Ubuntu
- Git y GitHub
- npm
- Docker, si aporta reproducibilidad al entorno local

Las versiones concretas deberán verificarse por compatibilidad antes de instalar dependencias. No se consideran seleccionadas versiones que todavía no se hayan comprobado.

---

## 6. Arquitectura general

```text
Navegador
    |
    v
React + TypeScript + Vite + Tailwind
    |
    | HTTP + cookie de sesión + protección CSRF
    v
API REST de Express (/api)
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
```

Durante el desarrollo, frontend y backend podrán ejecutarse como procesos separados. Vite utilizará un proxy para dirigir las solicitudes `/api` al backend.

El objetivo de despliegue futuro será servir el frontend en `/` y la API en `/api` bajo el mismo dominio. El Nivel 1 no incluye la construcción de infraestructura de producción ni el escalamiento horizontal efectivo.

### Principios arquitectónicos

1. La autenticación y autorización se verifican en el servidor.
2. Las contraseñas nunca se almacenan en texto plano.
3. La cookie contiene únicamente el identificador de sesión.
4. La persistencia de usuarios y sesiones tiene responsabilidades diferenciadas.
5. Los secretos reales no se guardan en Git.
6. Los fallos de dependencias esenciales no conceden acceso.
7. Las decisiones arquitectónicas importantes se documentan.
8. El sistema se construye localmente y evoluciona por etapas verificables.

---

## 7. Funcionalidades fuera del Nivel 1

Las siguientes capacidades se reservan para etapas posteriores:

- Verificación de correo electrónico.
- Recuperación y cambio de contraseña.
- MFA, TOTP, códigos de recuperación y passkeys.
- Función «Recordarme».
- Panel de dispositivos y administración avanzada de sesiones.
- Roles y permisos.
- Multi-organización.
- Auditoría y monitorización avanzadas.
- Defensas antifraude avanzadas.
- Escalamiento horizontal efectivo y almacenamiento compartido para rate limiting.
- Despliegue y endurecimiento de producción.

Que una funcionalidad esté fuera del alcance inicial no significa que nunca se desarrollará. Significa que no debe aumentar innecesariamente la complejidad de la primera versión.

---

## 8. Plan de trabajo

El desarrollo se organizará en una Fase 0 de cierre técnico y ocho fases de construcción.

| Fase | Objetivo | Hito de Git |
|---|---|---|
| 0. Cierre técnico | Resolver decisiones bloqueantes de arquitectura y seguridad | `planning-approved` |
| 1. Entorno | Preparar frontend, backend, PostgreSQL y pruebas | `environment-ready` |
| 2. Persistencia | Diseñar usuarios y sesiones | `data-ready` |
| 3. Registro | Implementar validación y creación de cuentas | `registration-ready` |
| 4. Inicio de sesión | Crear sesiones seguras y persistentes | `sessions-ready` |
| 5. Identidad y logout | Completar el ciclo de sesión y el recurso protegido | `auth-flow-ready` |
| 6. Interfaz | Integrar formularios, estados y errores | `frontend-ready` |
| 7. Pruebas | Verificar integración, seguridad y fallos | `security-tested` |
| 8. Documentación | Validar reproducibilidad y preparar la versión estable | `v1.0.0` |

Cada fase tendrá entregables, pruebas, criterios de aceptación y una comprobación de aprendizaje.

Las fases de construcción no comenzarán hasta cerrar las decisiones bloqueantes de la Fase 0.

El documento **Plan de trabajo definitivo — Autenticación Full Stack Evolutiva** contiene el desglose detallado de las actividades, dependencias, riesgos, pruebas y criterios de aceptación de cada fase.

---

## 9. Decisiones técnicas pendientes

Los siguientes puntos requieren investigación, precisión o aprobación antes de implementar las partes afectadas:

1. Biblioteca de sesiones y adaptador PostgreSQL.
2. Propiedad y estrategia de migraciones del almacenamiento de sesiones.
3. Parámetros concretos de Argon2id.
4. Límite preventivo de bytes para contraseñas y reglas exactas de conteo Unicode.
5. Especificación técnica de la normalización del correo.
6. Mecanismo CSRF, incluida la protección del inicio de sesión y la rotación de sesión.
7. Configuración exacta de CORS y `trust proxy`.
8. Intervalo y procedimiento de limpieza física de sesiones.
9. Herramienta E2E: evaluar Playwright y aprobar su incorporación.
10. Compatibilidad de versiones y comportamiento de las dependencias seleccionadas.

Las decisiones funcionales ya aprobadas se conservan. Los puntos anteriores no implican que todo el diseño deba discutirse de nuevo; son tareas técnicas necesarias para llevarlo a una implementación verificable.

---

## 10. Documentación del proyecto

Los documentos tendrán responsabilidades diferentes:

- **`README.md`:** visión del programa, mapa de módulos, alcance, stack, estado y siguiente paso.
- **Plan de trabajo definitivo:** fases, actividades, dependencias, criterios de aceptación, pruebas y riesgos.
- **Registros de decisiones de arquitectura (ADR):** alternativas evaluadas, decisión, justificación y consecuencias.
- **OpenAPI/Swagger:** contratos de la API y sus respuestas.
- **Documentación de ejecución:** configuración, variables de entorno, migraciones y pruebas.

El README resume el proyecto; no sustituye la especificación técnica detallada.

---

## 11. Estado actual y siguiente paso

**Estado del programa:** planificación y diseño inicial definidos.  
**Módulo activo:** 01 — Autenticación.  
**Plan de trabajo:** versión consolidada, pendiente de aprobación final.  
**Fase 0:** decisiones técnicas pendientes de investigación, precisión o aprobación.  
**Implementación:** no iniciada.  
**Dependencias específicas del proyecto:** no instaladas.

### Siguiente paso

Cerrar las decisiones técnicas bloqueantes de la Fase 0:

1. Seleccionar la biblioteca de sesiones y el adaptador PostgreSQL.
2. Definir migraciones y limpieza de sesiones.
3. Precisar la política de contraseñas y los parámetros de Argon2id.
4. Formalizar la normalización del correo.
5. Seleccionar el mecanismo CSRF y definir la protección del login.
6. Completar la configuración de cookies, CORS, proxy y rate limiting.
7. Confirmar el contrato de errores y el comportamiento ante fallos.
8. Evaluar la herramienta E2E y solicitar aprobación.
9. Verificar las versiones compatibles del stack.

El objetivo es cerrar lo imprescindible sin prolongar indefinidamente la planificación. No es necesario decidir ahora el proveedor de despliegue ni construir infraestructura de producción.

### Criterios de éxito del Nivel 1

El módulo se considerará terminado cuando:

- El registro cree una cuenta válida sin iniciar sesión automáticamente.
- Las contraseñas se almacenen únicamente mediante hashes Argon2id.
- La interfaz comunique las reglas de contraseña acordadas.
- El inicio de sesión cree una sesión persistida y segura.
- Los recursos protegidos rechacen solicitudes sin una sesión válida.
- El cierre de sesión invalide la sesión en el servidor.
- Las sesiones respeten los límites de inactividad y duración absoluta.
- Las sesiones simultáneas funcionen de forma independiente.
- Los controles CSRF, cookies y limitación de intentos estén implementados y probados.
- Los fallos de persistencia no produzcan una autenticación ficticia.
- El proyecto pueda prepararse desde un entorno limpio siguiendo la documentación.
- Las pruebas y limitaciones estén documentadas.

**Principio rector:** construir primero un sistema pequeño que funcione correctamente, comprender por qué funciona y demostrar mediante pruebas que mantiene los controles importantes ante errores y casos límite. Después, evolucionarlo de forma deliberada.
