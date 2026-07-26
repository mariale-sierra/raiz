# Cálculo de Costo y Tiempo de Desarrollo — Havit

Responsable: **Martin**

## Técnica utilizada

Se usa una **estimación bottom-up por evidencia del repositorio** (proxy de esfuerzo basado en commits e hitos de Git), combinada con tarifas de mercado local para desarrolladores junior/estudiantes. Es reproducible: cualquier miembro puede recalcular con `git log` y las tarifas declaradas.

## Supuestos declarados

1. Equipo de 7 integrantes (Esteban, Ale Sierra, Ale Pérez, Ale Macho, Martin, Camila, Emily), dedicación parcial (proyecto de curso).
2. Un commit de feature representa en promedio **1.5 horas** de trabajo efectivo (incluye diseño, prueba manual y corrección); un commit de fix/chore, **0.75 horas**. Promedio ponderado usado: **1.25 h/commit**.
3. Sprints con trabajo simultáneo en reuniones/coordinación: se añade **20 %** de overhead de gestión.
4. Tarifa de referencia: **Q60/hora** (≈ USD 7.7/h) para desarrollador junior en Guatemala; se muestra también el escenario a tarifa profesional de **Q150/hora**.

## Datos reales del repositorio

| Repositorio | Commits (histórico total al cierre del Sprint 6) |
|---|---|
| backend | 103 + commits del Sprint 6 |
| frontend | 78 + commits del Sprint 6 |
| raiz | 6 + commits del Sprint 6 |
| **Total base** | **187** commits previos + Sprint 6 |

Con ~200 commits totales tras el Sprint 6:

## Cálculo de tiempo

- Horas de desarrollo: 200 commits × 1.25 h = **250 h**
- Overhead de coordinación (20 %): 50 h
- Documentación académica (sprints 1–6, estimado directo): 40 h
- **Tiempo total estimado: ~340 horas** a lo largo de ~4 meses (mar–jul 2026)

Distribución aproximada por área (proporcional a commits): backend ≈ 52 %, frontend ≈ 40 %, infra/docs ≈ 8 %.

## Cálculo de costo

| Escenario | Tarifa | Costo |
|---|---|---|
| Costo real del equipo (junior) | Q60/h × 340 h | **Q20,400** (≈ USD 2,600) |
| Costo a precio de mercado profesional | Q150/h × 340 h | **Q51,000** (≈ USD 6,550) |

Costos de operación no laborales (reales, del proyecto): instancia Azure Database for PostgreSQL + contenedor del backend + Cloudflare R2 + API de OpenAI para moderación. Los montos exactos de facturación están en las cuentas del equipo (dato externo al repositorio); a tarifas publicadas, el orden de magnitud es de **USD 30–60/mes** para los tiers usados en desarrollo.

## Reproducibilidad

```bash
# horas ≈ commits × 1.25
cd backend  && git rev-list --count HEAD
cd frontend && git rev-list --count HEAD
cd raiz     && git rev-list --count HEAD
```

Ajustar el factor h/commit y las tarifas según el contexto; el resto del cálculo es lineal.
