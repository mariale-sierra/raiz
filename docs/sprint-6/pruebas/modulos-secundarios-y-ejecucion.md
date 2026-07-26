# Pruebas de Módulos Secundarios y Evidencia de Ejecución — Backend

Responsable: **Martin**

## Módulos secundarios cubiertos

### Ejercicios — `exercises.service.spec.ts` (nueva)
- `findAll` solo lista ejercicios activos.
- `findFullById`: NotFound para inexistente/inactivo; aplanado de métricas con fallback de `defaultUnit` al del tipo de métrica.
- `updateRelations`: NotFound para ejercicio inexistente; rechazo sin listas de relaciones; rechazo de primary que no pertenece a la lista enviada.

### Perfil de usuarios — `users.service.spec.ts` (ampliada de 3 a 17 casos)
- `getMyProfile`: defaults para usuarios sin fila de perfil; lectura nunca crea filas; NotFound.
- `updateProfile`: upsert en primera edición; **solo cambia los campos enviados**; bio vacía se limpia con `null` (no `undefined`, para que TypeORM realmente borre la columna); display_name en blanco se normaliza al username; NotFound sin escrituras.
- `updateProfilePhoto`: persiste la URL del flujo R2.
- `getPublicProfile`: perfil privado oculta bio pero mantiene nombre/foto; **nunca** expone email; NotFound para inactivos.
- `searchUsers`: query vacía no toca la BD; límite de 20; shape público sin email.
- `findById` (preexistente, reparada): nunca filtra `password_hash`.

## Autorización verificada en pruebas

- Invitaciones: solo destinatario acepta/rechaza; solo remitente cancela; solo creador/miembro activo invita (ver `estrategia-backend.md`).
- Challenges: solo el creador actualiza/elimina/edita días de ciclo.
- Guard JWT y utilidades de ownership con suites propias.

## Evidencia de ejecución (real, 2026-07-18)

Comando: `npx jest --coverage`

```
Test Suites: 11 passed, 11 total
Tests:       94 passed, 94 total
Snapshots:   0 total
Time:        80.4 s
```

Cobertura global real (`--coverageReporters=text-summary`):

```
Statements   : 45.16% ( 972/2152 )
Branches     : 37.10% ( 141/380 )
Functions    : 21.26% ( 67/315 )
Lines        : 45.83% ( 869/1896 )
```

Cobertura destacada del reporte (`--coverage`, extractos reales):

| Área | Statements |
|---|---|
| `workout-log.controller.ts` | 95 % |
| `users` DTOs de respuesta | 100 % |
| `workout-log` DTOs | 100 % |
| `workout-posts.service.ts` | 16 % (sin suite propia — limitación conocida) |

El reporte completo puede regenerarse con `cd backend && npx jest --coverage` (carpeta `coverage/`, no versionada).

## Manejo de errores verificado

- 400 (`BadRequestException`): autoinvitación, datos inválidos.
- 401: guard JWT global (suite del guard).
- 403 (`ForbiddenException`): actor sin permiso en invitaciones y challenges.
- 404 (`NotFoundException`): usuario/challenge/invitación inexistentes.
- 409 (`ConflictException`): duplicados, estados ya procesados, expiración, carrera de unicidad.
