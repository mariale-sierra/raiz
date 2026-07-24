# Sprint Backlog — Sprint 6

Responsable del documento: **Ale Sierra**

Todas las tareas de este sprint, organizadas por responsable, con su estado final real. La integración se realizó sobre la rama `development` de cada repositorio (`backend`, `frontend`, `raiz`).

## Ale Sierra
| Tarea | Repositorio | Estado |
|---|---|---|
| Limpieza de código (imports muertos en `workout-log.service`, `challenges.controller`; tipado de errores) | backend | ✅ Completada |
| Lógica de negocio de invitaciones (`ChallengeInvitesService`: duplicados, autoinvitación, permisos, estados, transacción al aceptar, manejo de carrera 23505) | backend | ✅ Completada |
| Migración de índices para listados de invitaciones (`2026-07-18-01-challenge-invite-indexes.sql`) | backend | ✅ Completada |
| Lógica de actualización de perfil y privacidad (`UsersService`: upsert de `user_profiles`, PATCH parcial, normalización, perfil público con privacidad) | backend | ✅ Completada |
| Sprint Backlog (este documento) | raiz | ✅ Completada |

## Esteban
| Tarea | Repositorio | Estado |
|---|---|---|
| Reorganización/estructura: registro de módulo de invites en `AppModule`, rutas nuevas en Expo Router, reestructura de pantalla de perfil preparada para futuras secciones | backend / frontend | ✅ Completada |
| Integración frontend de invitaciones (`invite.service.ts`, `useInvites`, invalidación de caché de progreso al aceptar) | frontend | ✅ Completada |
| Integración frontend de perfil (`user.service.ts`: getMyProfile/update/photo/search/public; conexión de formularios; persistencia al reabrir) | frontend | ✅ Completada |
| Corrección de errores de typecheck preexistentes (tone `inverse`, `active-all`, `useMetricsScreen`) | frontend | ✅ Completada |
| Espera/reintento de BD en el runner de migraciones + documentación del arranque en `docker-compose.yml` | backend / raiz | ✅ Completada |
| Product Backlog | raiz | ✅ Completada |

## Ale Pérez
| Tarea | Repositorio | Estado |
|---|---|---|
| Endpoints de invitaciones (crear, received, sent, pending, accept, decline, cancel) con DTO de respuesta seguro | backend | ✅ Completada |
| Endpoints de perfil (`GET/PATCH /users/me/profile`, `PATCH /users/me/profile/photo`, `GET /users/:id/profile`, `GET /users/search`) | backend | ✅ Completada |
| Métricas del sprint | raiz | ✅ Completada (con dependencia externa documentada: datos de Jira no disponibles en el workspace) |
| Consolidado académico de pruebas unitarias | raiz | ✅ Completada |

## Martin
| Tarea | Repositorio | Estado |
|---|---|---|
| Validaciones de DTOs (UUIDs, longitudes, idiomas permitidos, URL https de foto, whitelist global) | backend | ✅ Completada |
| Documentación Swagger de invitaciones y perfil (códigos de error, ejemplos, estados) | backend | ✅ Completada |
| Pruebas de módulos secundarios (ejercicios, perfil de usuarios) y ejecución de la suite completa con evidencia | backend | ✅ Completada |
| Cálculo de costo y tiempo | raiz | ✅ Completada |

## Ale Macho
| Tarea | Repositorio | Estado |
|---|---|---|
| Reparación de suites rotas por DI desactualizada (`challenges.service.spec`, providers faltantes) | backend | ✅ Completada |
| Pruebas de lógica crítica: ciclo de vida completo de invitaciones (23 casos) y `joinChallenge` | backend | ✅ Completada |
| Estrategia técnica de pruebas backend (documento) | raiz | ✅ Completada |

## Camila
| Tarea | Repositorio | Estado |
|---|---|---|
| Pantalla de invitaciones (recibidas/enviadas, estados vacío/carga/error, pull-to-refresh) | frontend | ✅ Completada |
| Flujo de invitar usuarios desde el detalle del challenge (búsqueda + confirmación) | frontend | ✅ Completada |
| Pantalla "Editar perfil" y reestructura de la pantalla principal de perfil | frontend | ✅ Completada |
| Escenarios funcionales de prueba del frontend (documento con resultados) | raiz | ✅ Completada |
| Interacción con usuarios | raiz | ⚠️ Estructura creada; la evidencia depende de actividad humana aún no realizada (ver documento) |

## Emily
| Tarea | Repositorio | Estado |
|---|---|---|
| Componentes reutilizables: `FormField`, variante de éxito del sistema de notificaciones, avatar con foto | frontend | ✅ Completada |
| Confirmaciones y feedback en invitaciones (popup antes de enviar/cancelar, mensajes de éxito/error, prevención de doble envío, botones con loading) | frontend | ✅ Completada |
| Pruebas de componentes reutilizables (Button, FormField, ConfirmationPopup, InviteCard, store de notificaciones) | frontend | ✅ Completada |
| Retrospectiva del sprint | raiz | ✅ Completada |

## Notas
- No se usaron deadlines como criterio: todas las tareas se integraron de una vez sobre `development`.
- Los resultados reales de pruebas están en `pruebas/` (mismos números que la ejecución final registrada allí).
