# Documentación del Sprint 6 — Havit

Integración end-to-end de **Challenge Invites** y **Perfil (edición, privacidad, foto)**, ampliación de pruebas unitarias en backend y frontend, y documentación académica del sprint. Todo el trabajo vive en la rama `development` de los repositorios `backend`, `frontend` y `raiz`.

## Índice

| Documento | Responsable |
|---|---|
| [Product Backlog](product-backlog.md) | Esteban |
| [Sprint Backlog](sprint-backlog.md) | Ale Sierra |
| [Métricas del sprint](metricas.md) | Ale Pérez |
| [Costo y tiempo](costo-tiempo.md) | Martin |
| [Pruebas: estrategia backend](pruebas/estrategia-backend.md) | Ale Macho |
| [Pruebas: módulos secundarios y ejecución](pruebas/modulos-secundarios-y-ejecucion.md) | Martin |
| [Pruebas: escenarios frontend](pruebas/escenarios-frontend.md) | Camila |
| [Pruebas: componentes frontend](pruebas/componentes-frontend.md) | Emily |
| [Pruebas: consolidado](pruebas/consolidado.md) | Ale Pérez |
| [Interacción con usuarios](interaccion-usuarios.md) | Camila (estructura; evidencia pendiente de actividad humana) |
| [Retrospectiva](retrospectiva.md) | Emily |

## Resultados de validación (reales, 2026-07-18)

- Backend: build ✅ · 11 suites / 94 tests ✅ · cobertura global 45.16 % statements.
- Frontend: typecheck ✅ (0 errores) · 7 suites / 28 tests ✅.
- Migración nueva (`2026-07-18-01-challenge-invite-indexes.sql`): idempotente; se aplica automáticamente en el próximo arranque del contenedor (la BD de Azure no es accesible desde el entorno local).
