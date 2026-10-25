# B6 — Despliegue seguro y CI/CD: infraestructura (Sprint 10)

Responsable: Esteban de la Peña (el bloque estaba asignado a Ale Pérez; se intercambió) · 2026-10-25. El documento principal, con el pipeline, las pruebas y la evidencia, está en `backend/docs/sprint-10/B6-despliegue-seguro-cicd.md`.

## 1. Resumen

Hasta ahora `docker-compose.yml`, que es lo que la VM levanta en cada deploy, construía el Dockerfile del backend y sobrescribía el comando con `npm run dev`. Eso significaba que producción corría `nest start --watch` como root, con devDependencies. Ahora el servicio `backend` construye el target **`prod`** del Dockerfile multi-etapa de B6 (sin root, solo dependencias de producción, `HEALTHCHECK` en `/health`) y usa su `CMD` (`npm start`). `docker-compose.local.yml` usa el target **`dev`** (`npm run dev`).

**Criterio: CUMPLIDO en local / PARCIAL en la VM.** Los dos flujos se validaron con una copia aislada de este compose y un Postgres local. En la VM falta el primer deploy, que llega con el merge a `main` (necesita autorización).

## 2. Decisiones técnicas

- **Target por variable** `${BACKEND_TARGET:-prod}`: producción no necesita configurar nada, y `BACKEND_TARGET=dev` sirve para depurar contra Azure con `nest --watch`.
- **Sin `command:`** en el compose base: el comando lo define la imagen. Así `prod` y `dev` no pueden quedar cruzados (la imagen `prod` no trae `@nestjs/cli`, así que `npm run dev` ahí no arranca).
- **El deploy del backend actualiza también este repo** (`git merge --ff-only origin/main` en `/home/azureuser/raiz`) antes de bajar nada. Si el checkout de la VM no puede avanzar, el deploy se aborta y el stack viejo sigue arriba.
- **Build antes del corte:** `docker-compose build backend` mientras el contenedor viejo sigue sirviendo, y después `down`/`up -d`. Medido localmente: el corte baja de 35.3 s a 8.8 s.

## 3. Archivos modificados

| Archivo | Cambio |
|---|---|
| `docker-compose.yml` | `backend.build` → `{context: ../backend, target: ${BACKEND_TARGET:-prod}}`; se quita `command: npm run dev`; comentarios actualizados (health check en `/health`, `COPY certs`). |
| `docker-compose.local.yml` | `backend.build.target: dev`. |
| `README.md` | Explica los dos targets y `BACKEND_TARGET`. |
| `docs/sprint-10/B6-despliegue-seguro-cicd.md` | Este documento. |

## 4. Configuración e infraestructura

- `.env` de la VM: **sin cambios**. `BACKEND_TARGET` no se define (queda `prod`).
- VM, una sola vez: `/home/azureuser/raiz` tiene que estar en `main` y limpio (`git status`), porque el deploy ahora lo actualiza.
- Orden de merge a `main`: primero **raiz**, después **backend**.
- `docker-compose v1` en la VM: `build: {context, target}` es sintaxis del formato de archivo 3.4+ (este usa `version: '3.8'`), y docker-compose v1 la soporta. No se pudo probar con v1 en esta máquina (tiene compose v5); el primer deploy lo confirma.
- Los nombres de contenedor (`havit-backend`, `havit-pgbouncer`, `havit-redis`, `havit-caddy`) no cambian. El deploy los sigue limpiando con `docker rm -f`.

## 5. Cómo se probó

Se hizo una copia en un directorio temporal (`raiz/` + symlink a `backend/`), con los `container_name` renombrados para no chocar con los contenedores locales del equipo y un `.env` falso apuntando a un Postgres local. **No se usó Azure.**

```text
$ docker compose -f docker-compose.yml -f docker-compose.local.yml config   -> backend.build.target: dev
$ docker compose config                                                   -> backend.build.target: prod

dev:local   5 servicios arriba; PID 1 = "npm run dev"; id = uid=1000(node); /health/ready {"database":"up","redis":"up"}; https vía Caddy /health 200
prod        PID 1 = "npm start"; id = uid=1000(node); HEALTHCHECK healthy; 2 redeploys con datos intactos; migraciones "nothing to apply"
```

La salida completa está en `backend/docs/sprint-10/evidencias-b6/compose-dev.txt` y `compose-prod-deploy.txt`.

## 6. Evidencia

`backend/docs/sprint-10/evidencias-b6/` (sección 6 del documento principal).

## 7. Pendientes y riesgos

- Primer deploy real en la VM, que depende del merge a `main` de raiz y de backend.
- Queda un downtime de ~9 s, porque `down` reinicia también Caddy, PgBouncer y Redis (workaround de compose v1). Las alternativas están en el documento principal.
- B3: se mantuvieron sin cambios `caddy`, los volúmenes de certificados, `BACKEND_BIND_ADDR`, `TRUST_PROXY` y `DB_SSL_CA_PATH`.

## 8. Cómo revertir

`git revert` del commit de B6 en este repo (vuelve a `build: ../backend` + `command: npm run dev`) y redeploy. Para salir del paso sin revertir: `docker-compose rm -sf backend && BACKEND_TARGET=dev docker-compose up -d --build backend` (primero `rm`: compose v1 truena si recrea el contenedor en el lugar) en la VM.
