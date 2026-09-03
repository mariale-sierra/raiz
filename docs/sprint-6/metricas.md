# Métricas del Sprint — Sprint 6

Responsable: **Ale Pérez**

## Fuentes de datos disponibles

Se buscó en todo el workspace evidencia cuantitativa para este informe:

- ✅ Historial de Git de los tres repositorios (`backend`: 103 commits previos al sprint, `frontend`: 78, `raiz`: 6).
- ✅ Resultados reales de las suites de pruebas (ver `pruebas/consolidado.md`).
- ❌ **No existe** en el workspace ninguna exportación de Jira (CSV/JSON), captura de burndown, ni integración accesible con un tablero. Esta es una dependencia externa: si el equipo exporta el tablero de Jira, las secciones marcadas ⚠️ pueden completarse con números de story points.

## Alcance completado (medible en el repositorio)

| Métrica | Valor real |
|---|---|
| Funcionalidades nuevas end-to-end | 2 (Challenge Invites completo; Perfil: edición + privacidad + foto) |
| Endpoints backend nuevos | 12 (7 de invitaciones, 5 de perfil/búsqueda) |
| Migraciones de BD nuevas | 1 (índices de invitaciones) |
| Pantallas frontend nuevas | 3 (Invitaciones, Invitar usuarios, Editar perfil) + reestructura de la pantalla Perfil |
| Suites de prueba backend | 11 suites (antes: 9, de las cuales 2 estaban rotas) |
| Tests backend | 94 (antes: 48 en verde, 11 fallando por DI desactualizada) |
| Suites de prueba frontend | 7 (antes: 1) |
| Tests frontend | 28 (antes: 2 de paridad i18n) |
| Documentos académicos | 9 (backlogs, métricas, costo, 4 de pruebas, retrospectiva, interacción) |

## Burndown ⚠️

No es posible reconstruir un burndown fiable sin los story points y fechas de transición del tablero de Jira (dato externo no disponible en el workspace). El trabajo del sprint se ejecutó como una integración continua única sobre `development`, por lo que el burndown real sería una caída casi vertical al cierre.

## Velocidad ⚠️

Sin story points históricos no se puede calcular velocidad en puntos ni compararla con sprints anteriores o con el semestre pasado. Como aproximación verificable con Git: los sprints 2–4 promediaron ~30 commits por sprint por repositorio activo; el Sprint 5 concentró el hardening en 4 merges grandes; el Sprint 6 entrega su alcance en commits atómicos por responsable (ver historial de `development`).

## Discusión de resultados

- Lo más valioso del sprint no es solo la funcionalidad nueva sino la recuperación de la base de calidad: la suite backend estaba parcialmente rota (11 tests fallando por proveedores de DI desactualizados tras el fix de challenges del Sprint 5) y quedó en verde, casi duplicando el número de tests.
- Invitaciones se implementó con protecciones reales de concurrencia (transacción + bloqueo pesimista + índice único parcial), no solo el camino feliz.
- El frontend ganó su primera suite de pruebas de componentes; antes solo existía el test de paridad i18n.
- Deuda pendiente: el lint del backend arrastra ~80 errores `no-unsafe-*` preexistentes en módulos antiguos (auth/challenges/workout-log); no se resolvieron porque exigen un refactor de tipado amplio fuera del alcance del sprint.

## Calificación del sprint: **8 / 10**

Justificación: se completó el 100 % del alcance funcional comprometido (invitaciones E2E, perfil E2E, pruebas, documentación) con validación real (build, typecheck, 122 tests en verde entre ambos repos). Se descuentan 2 puntos por: (1) la deuda de lint preexistente que sigue sin resolverse, y (2) la falta de datos de gestión (Jira) que impide medir velocidad y burndown, un problema de proceso del equipo, no del código.

## Acciones concretas para mejorar

1. Exportar el tablero de Jira (CSV) al cierre de cada sprint y versionarlo en `raiz/docs/<sprint>/jira/` para que las métricas sean calculables.
2. Definir story points por tarea en el backlog del sprint desde el día 1.
3. Agregar un gate de CI que ejecute `npm test` y `tsc --noEmit` en cada PR para que las suites no vuelvan a romperse silenciosamente.
4. Reducir la deuda de lint del backend en incrementos fijos (p. ej. 15 errores por sprint).
