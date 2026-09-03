# Estrategia de Pruebas Unitarias — Backend

Responsable: **Ale Macho**

## Framework: Jest (+ @nestjs/testing)

- **Ya estaba configurado** en el repositorio (`backend/package.json`: `jest`, `ts-jest`, preset de NestJS); no se instaló ninguna dependencia nueva.
- Razón técnica: Jest es el framework por defecto del ecosistema NestJS. `@nestjs/testing` provee `Test.createTestingModule`, que permite instanciar servicios con su inyección de dependencias real pero sustituyendo cada `Repository` de TypeORM por un mock, sin base de datos.

## Estrategia de mocks

- Cada repositorio TypeORM se sustituye con `getRepositoryToken(Entidad)` → objeto con `find/findOne/save/create/createQueryBuilder` como `jest.fn()`.
- `DataSource.transaction` se mockea entregando un `manager` falso cuyo `getRepository()` devuelve repos transaccionales espiados — esto permite verificar que **aceptar una invitación escribe la invitación y la membresía dentro de la misma transacción**.
- No se usan snapshots: todas las aserciones son de comportamiento (qué se guardó, con qué argumentos, qué excepción se lanzó).

## Alcance (lógica crítica)

| Suite | Casos |
|---|---|
| `auth.service.spec.ts` | login/registro, hash de contraseñas, tokens (preexistente, en verde) |
| `challenges.service.spec.ts` | update/remove/updateCycleDay con ownership + **nuevo**: `joinChallenge` (unión exitosa, creador no puede unirse, doble unión, challenge inexistente) |
| `challenge-invites.service.spec.ts` (**nueva**, 23 casos) | creación (feliz, autoinvitación, challenge/usuario inexistente, remitente sin permiso, destinatario ya miembro, duplicado pendiente, carrera 23505→409), aceptación (transacción, reactivación de membresía `left`, actor incorrecto, estados inválidos, expiración, inexistente), rechazo, cancelación, listados con scoping por usuario |
| `workout-log.*.spec.ts` | registro de progreso, regla un-log-por-día (preexistente) |
| `metrics.service.spec.ts` | registro de métricas (preexistente) |
| `jwt-auth.guard.spec.ts`, `assert-ownership.spec.ts`, `http-exception.filter.spec.ts` | permisos, ownership, formato de errores (preexistentes) |

Además se **repararon** dos suites que fallaban (11 tests) porque los constructores de `UsersService` y `ChallengesService` habían ganado repositorios nuevos en el Sprint 5 sin actualizar los providers de los tests.

## Resultados reales

```
Test Suites: 11 passed, 11 total
Tests:       94 passed, 94 total
```

(Ejecución local `npx jest` el 2026-07-18; punto de partida del sprint: 9 suites, 48 tests, 11 fallando.)

## Limitaciones

- Son pruebas unitarias puras: no ejercitan SQL real ni la unicidad del índice parcial en Postgres (se simula el error 23505).
- Los controladores de invitaciones/perfil se validan a través del build + Swagger; el e2e (`test:e2e`) requiere la BD de Azure, no accesible desde el entorno local de esta ejecución.
