# Product Backlog — Havit

Responsable: **Esteban**

Backlog reconstruido a partir del historial real de Git de los tres repositorios (`backend`, `frontend`, `raiz`). Cada ítem indica el sprint en el que se completó, verificable con `git log` en el repositorio correspondiente.

## Convención de sprints (según fechas del historial)

| Sprint | Periodo aproximado | Enfoque |
|---|---|---|
| Sprint 1 | mar 2026 – 9 abr 2026 | Infraestructura, Docker, esqueleto de pantallas, auth |
| Sprint 2 | 10 abr – 22 abr 2026 | Ejercicios, rutinas, workout logs, creación de challenges |
| Sprint 3 | 23 abr – 12 may 2026 | Integración frontend-backend, progreso, Cloudflare R2, cámara |
| Sprint 4 | 13 may – 27 may 2026 | Workout posts, moderación OpenAI, ciclos, correcciones |
| Sprint 5 | jul 2026 (semana 1-3) | Hardening, seguridad, runner de migraciones SQL, suite base de tests, fixes de integración de challenges |
| Sprint 6 | 17–18 jul 2026 | Challenge invites E2E, perfil (edición/privacidad/foto), pruebas unitarias ampliadas, documentación |

## Ítems del backlog y sprint de cierre

### Infraestructura y plataforma
- Dockerización del backend y `docker-compose` en `raiz` — **Sprint 1** (`raiz`: "iniciar docker", "docker yml"; `backend`: "dockerfile", "deploy").
- Migración de auth de Supabase a JWT propio — **Sprint 1** (`backend`: "pasar auth de supabase a JWT").
- Runner de migraciones SQL versionadas (`database/init|migrations|seeds` + `schema_migrations`) — **Sprint 5** (`backend`: "harden(F0-F1)…", docs de CLAUDE.md).
- Espera/reintento de conexión a BD antes de migrar en cada arranque — **Sprint 6**.

### Autenticación y usuarios
- Registro/login con JWT, guard global deny-by-default — **Sprint 1** (base) y **Sprint 5** (hardening, `@Public()`, ownership).
- Perfil del usuario (`GET /users/me`, `GET /users/me/challenges`) — **Sprint 2–3**.
- Edición de perfil (nombre, bio, idioma), privacidad y foto vía R2 — **Sprint 6**.
- Perfil público de otros usuarios respetando privacidad + búsqueda de usuarios — **Sprint 6**.

### Challenges
- CRUD de challenges, join/leave/complete, restricción creador — **Sprint 2–4**.
- Días de ciclo (workout/descanso), rutinas por día — **Sprint 4**.
- Progreso diario, `current_day`, resumen y validación un-log-por-día — **Sprint 3–4**.
- Persistencia de categorías/rutinas al crear y fix de cálculo de progreso — **Sprint 5** (PR #3 backend).
- Invitaciones a challenges (crear, listar recibidas/enviadas/pendientes, aceptar, rechazar, cancelar; transacción al aceptar) — **Sprint 6**.

### Ejercicios, rutinas y métricas
- Módulo de ejercicios con seed inicial y relaciones (categorías, ubicaciones, partes del cuerpo) — **Sprint 2** y **Sprint 4** ("feat: agregar relaciones de ejercicios").
- Rutinas con sets y targets — **Sprint 2** ("Sets y targets para routines").
- Workout logs + métricas por ejercicio — **Sprint 2–3**.

### Social y contenido
- Subida de imágenes con URLs firmadas de Cloudflare R2 — **Sprint 3** ("add cloudflare", "fix signedurl").
- Workout posts con mosaico por challenge — **Sprint 4**.
- Moderación de imágenes con OpenAI antes de publicar — **Sprint 4**.

### Frontend (app Expo/React Native)
- Sistema base de UI (componentes, layouts, tema) y estructura de rutas — **Sprint 1**.
- Pantallas de login/registro con fondo y flujo de sesión persistente (SecureStore) — **Sprint 1** (base), **Sprint 5** (SecureStore centralizado).
- Flujo de creación de challenge (multi-pantalla, builder store) — **Sprint 2**.
- Home con progreso, tabs de challenges, búsqueda — **Sprint 3**.
- Cámara y flujo de evidencia fotográfica — **Sprint 3**.
- Pantalla de fotos por challenge (mosaico), popups y botones — **Sprint 4**.
- i18n en/es con test de paridad — **Sprint 3** (base) y **Sprint 5** (test de paridad).
- Alineación Home/Challenges y fix del botón Join — **Sprint 5** (PR #2 frontend).
- Pantalla de invitaciones (recibidas/enviadas), flujo de invitar desde el detalle del challenge, confirmaciones y toasts de éxito — **Sprint 6**.
- Pantalla "Editar perfil" (foto, nombre, bio, idioma, privacidad) y reestructura de la pantalla de perfil — **Sprint 6**.

### Calidad
- Suite de seguridad/ownership backend (48 tests iniciales) — **Sprint 5** ("test(F7)").
- Ampliación de pruebas backend (invitaciones, perfil, ejercicios, join de challenges) y reparación de suites rotas — **Sprint 6**.
- Pruebas de componentes frontend (Jest + React Native Testing Library) — **Sprint 6**.
- Documentación académica del sprint (backlogs, métricas, costos, retrospectiva) — **Sprint 6**.

> Nota de verificación: los sprints 1–5 se infieren de las fechas de commit reales; no existe en el workspace una exportación de Jira que permita mapear ítems a sprints con otra granularidad.
