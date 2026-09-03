# Pruebas Unitarias — Consolidado Académico

Responsable de la consolidación: **Ale Pérez**
(Estrategia backend: Ale Macho · Módulos secundarios y ejecución: Martin · Escenarios frontend: Camila · Componentes frontend: Emily)

## Frameworks seleccionados y justificación

| Capa | Framework | Justificación |
|---|---|---|
| Backend (NestJS) | Jest + @nestjs/testing | Integración nativa con el contenedor de DI de Nest; permite sustituir repositorios TypeORM por mocks sin base de datos. Ya estaba configurado; no se instalaron dependencias. |
| Frontend (Expo/RN) | Jest (jest-expo) + React Native Testing Library | Preset oficial de Expo; RNTL prueba interacción real (press, changeText, accesibilidad) en lugar de detalles de implementación. Solo se agregó la peer dependency faltante `test-renderer`. |

## Estrategia

- Unitarias de comportamiento con dependencias simuladas; sin snapshots.
- Backend: casos exitosos, datos inválidos, recursos inexistentes, permisos (403), estados límite (expiración, dobles procesamientos) y excepciones traducidas a códigos HTTP correctos, incluida la condición de carrera de unicidad (23505 → 409).
- Frontend: estados normal/éxito/error/carga/deshabilitado, callbacks, prevención de doble envío y confirmaciones.

## Alcance

- Backend: 11 suites — auth, challenges (incl. join), **challenge-invites (nueva, 23 casos)**, users/perfil (ampliada), **exercises (nueva)**, workout-log (servicio y controller), metrics, guard JWT, ownership, filtro de excepciones.
- Frontend: 7 suites — Button, FormField, ConfirmationPopup, InviteCard, store de notificaciones, servicio de invitaciones, paridad i18n.

## Evidencia de ejecución automatizada (real, 2026-07-18)

| Repo | Suites | Tests | Resultado |
|---|---|---|---|
| backend | 11 | 94 | ✅ todos en verde |
| frontend | 7 | 28 | ✅ todos en verde |
| **Total** | **18** | **122** | ✅ |

Cobertura global backend (text-summary real): Statements 45.16 %, Lines 45.83 %. Frontend: los módulos nuevos alcanzan 100 % (invite.service); la métrica global de `services/` es baja por código preexistente sin suite.

## Limitaciones y conclusiones

- No hay pruebas e2e ejecutadas en este sprint: requieren acceso de red a la BD de Azure, no disponible desde el entorno de ejecución local.
- `workout-posts.service` sigue sin suite propia (16 % de cobertura) — primer candidato del siguiente sprint.
- Conclusión: la base de pruebas pasó de 48 tests (con 11 rotos) a 122 en verde entre ambos repos, y las dos funcionalidades nuevas del sprint nacieron con suite completa de su lógica de negocio. El punto débil sigue siendo la cobertura del código heredado, no la del código nuevo.
