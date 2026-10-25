# Autenticación Full Stack Evolutiva

Módulo de aprendizaje para diseñar e implementar autenticación web de extremo a extremo. Es el primer proyecto de una biblioteca progresiva de capacidades Full Stack reutilizables.

> **Estado:** planificación. La implementación no ha comenzado; este repositorio aún no contiene código ni instrucciones de instalación o ejecución.

## Objetivo

Construir primero una autenticación fundamental, funcional y verificable, y ampliar sus capacidades por etapas. El proyecto busca aprender frontend, backend, persistencia, seguridad y pruebas sin añadir complejidad antes de que sea necesaria.

## Alcance inicial

La primera versión contempla:

- Registro con correo electrónico y contraseña.
- Inicio y cierre de sesión.
- Hash de contraseñas con Argon2id.
- Sesiones del lado del servidor, persistidas en PostgreSQL.
- Consulta de la identidad autenticada y un endpoint protegido.
- Interfaz web conectada a una API REST.
- Validaciones, manejo de errores y pruebas automatizadas.

Rutas previstas:

| Método | Ruta | Propósito |
|---|---|---|
| `POST` | `/api/auth/register` | Crear una cuenta |
| `POST` | `/api/auth/login` | Iniciar sesión |
| `POST` | `/api/auth/logout` | Cerrar la sesión actual |
| `GET` | `/api/auth/me` | Consultar la identidad autenticada |
| `GET` | `/api/protected` | Ejemplo de recurso protegido |

La API será la autoridad de autenticación y acceso; ocultar una página en el frontend no protege sus datos.

## Seguridad prevista

- No almacenar contraseñas en texto plano.
- Validar las solicitudes en el backend.
- Proteger y expirar las sesiones en el servidor.
- Configurar cookies seguras y protección CSRF.
- Limitar los intentos de inicio de sesión.
- Fallar de forma segura si no se puede verificar una sesión o persistir una operación.

Los mecanismos y parámetros técnicos aún pendientes se decidirán y probarán antes de implementar las partes correspondientes.

## Stack previsto

| Área | Tecnologías |
|---|---|
| Frontend | React, TypeScript, Vite y Tailwind CSS |
| Backend | Node.js, TypeScript y Express |
| Validación | Zod |
| Base de datos | PostgreSQL y Prisma |
| Pruebas | Vitest y Supertest |
| API | OpenAPI |

Este stack es una selección de planificación, no una lista de dependencias instaladas. Las versiones y algunas herramientas complementarias se verificarán antes de usarlas.

## Evolución

El módulo de autenticación podrá incorporar posteriormente recuperación de cuentas, verificación de correo, MFA, integración con roles y permisos, y soporte multi-organización. Estas capacidades no forman parte de la primera versión.

El programa de aprendizaje contempla también perfiles de usuario, gestión de datos, estructura de navegación, autorización y búsqueda con filtros y paginación.

## Estado y siguiente paso

El [plan de trabajo v1.0](docs/plan_trabajo.md) está listo para revisión y aprobación final. La implementación y la instalación de dependencias aún no han comenzado. Tras aprobar el plan, el siguiente paso será cerrar las decisiones técnicas que condicionan la primera implementación —como la biblioteca de sesiones, CSRF, migraciones y parámetros de hashing— y después preparar el entorno de desarrollo.

Las funcionalidades se incorporarán por etapas, con pruebas y criterios de aceptación. La versión inicial se considerará lista cuando el flujo de autenticación, la persistencia, los controles de seguridad y la documentación se hayan verificado.
