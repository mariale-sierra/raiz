# B3 — HTTPS y seguridad del transporte: infraestructura (Sprint 10)

Responsable: Ale Pérez · 2026-10-23. Documento principal, con todas las pruebas, la evidencia y el plan de transición: `backend/docs/sprint-10/B3-https-transporte.md`.

## 1. Resumen

`docker-compose.yml` agrega Caddy como proxy HTTPS delante de `backend`: certificado automático (Let's Encrypt) para `API_DOMAIN`, redirección 308 HTTP→HTTPS y `X-Forwarded-For` controlado por el proxy. PgBouncer pasa de `require` a `verify-full` contra las raíces de Azure. El 3000 queda publicado durante la transición y se cierra con `BACKEND_BIND_ADDR=127.0.0.1`.
**Criterio: PARCIAL.** Configuración validada en un stack local equivalente; faltan la etiqueta DNS, el NSG (80/443), el despliegue y el cierre del 3000.

> **Actualización 2026-10-27.** La etiqueta DNS, los puertos 80/443 y el despliegue ya están. `https://havit-api.canadacentral.cloudapp.azure.com` responde con un certificado válido de Let's Encrypt (vence el 2027-01-24) y HTTP redirige con 308. Solo falta **cerrar el 3000**, que sigue respondiendo desde fuera (`http://20.63.84.1:3000/health` → 200). Se espera a que haya un APK HTTPS distribuido. Evidencia en `backend/docs/sprint-10/evidencias-b3/verificacion-produccion-2026-10-27.txt`.
>
> **Para cerrarlo (no requiere cambiar código), cuando el APK nuevo esté repartido:**
> 1. En la VM, agregar `BACKEND_BIND_ADDR=127.0.0.1` en `/home/azureuser/raiz/.env`. El `.env` no lo sobrescribe el deploy, así que el valor se conserva.
> 2. `cd /home/azureuser/raiz && docker-compose up -d`. El health check del deploy usa `localhost:3000` y sigue funcionando.
> 3. Quitar la regla de entrada del 3000 en el NSG de Azure, como segunda barrera.
> 4. Comprobar desde fuera: `curl -m 10 http://20.63.84.1:3000/health` debe fallar por tiempo de espera o conexión rechazada, y `https://…/health` debe seguir respondiendo 200.
>
> No se cambió el valor por defecto del compose a `127.0.0.1`. Si se hiciera, el siguiente merge a `main` cerraría el 3000 sin aviso y dejaría sin servicio a los APK HTTP que todavía están instalados.

## 2. Decisiones técnicas

- Caddy en lugar de Nginx+certbot, Cloudflare o Front Door: renovación y redirección automáticas, sin dominio propio ni costo extra.
- Sin `encode gzip` (BREACH; el API nunca comprimió).
- Sin `trusted_proxies` en Caddy: reemplaza el `X-Forwarded-For` del cliente. Nest confía solo en saltos privados (`TRUST_PROXY=loopback,uniquelocal`).
- `API_DOMAIN: ${API_DOMAIN:-localhost}` y no `:-`: un valor vacío **sí** cuenta como definido para Caddy y rompe el Caddyfile (encontrado en las pruebas).
- 3000 en `${BACKEND_BIND_ADDR:-0.0.0.0}` para no dejar sin servicio a los APK HTTP instalados. Con `127.0.0.1` el health check del deploy sigue funcionando.
- PgBouncer `verify-full` + `SERVER_TLS_CA_FILE` montado desde `../backend/certs` (raíces públicas, sin secretos).

## 3. Archivos modificados

| Archivo | Cambio |
|---|---|
| `caddy/Caddyfile` (nuevo) | Sitio `{$API_DOMAIN:localhost}` → `reverse_proxy backend:3000`. |
| `docker-compose.yml` | Servicio `caddy` + volúmenes `caddy_data`/`caddy_config`; `backend`: `BACKEND_BIND_ADDR`, `TRUST_PROXY`, `DB_SSL_CA_PATH`; `pgbouncer`: `verify-full`, CA montado. |
| `.env.example` | Variables nuevas documentadas. |

## 4. Configuración e infraestructura

`raiz/.env` en la VM: `API_DOMAIN=havit-api.<región>.cloudapp.azure.com`; más adelante `BACKEND_BIND_ADDR=127.0.0.1`. Nada más: `TRUST_PROXY`, `DB_SSL_CA_PATH` y `verify-full` ya vienen por defecto.

**Orden:** desplegar **backend antes que raiz** (raiz monta y usa `backend/certs/azure-postgres-root-cas.pem`; si no existe, el backend no arranca y PgBouncer no carga el CA).

**Manual (Azure):** etiqueta DNS `havit-api` en la IP pública `20.63.84.1`; NSG: permitir TCP 80 y 443 desde Internet; al final, quitar la regla del 3000. Pasos exactos y checklist de cierre de puertos: documento principal, 4.5 y 4.6.

**Volumen `caddy_data`:** guarda los certificados. Nunca usar `docker-compose down -v`.

## 5. Pruebas

- `docker compose config` sobre `docker-compose.yml` y sobre `docker-compose.yml + docker-compose.local.yml`: válido. Interpolación comprobada (`BACKEND_BIND_ADDR=127.0.0.1` → `host_ip: 127.0.0.1`).
- `caddy validate` / `caddy adapt` con `caddy:2.10.2-alpine`: válido.
- Stack local equivalente (Caddy + backend + PgBouncer `verify-full` + Postgres TLS): redirección 308, TLS verificado, testssl.sh sin hallazgos, limitador por IP correcto y PgBouncer que rechaza CA ajena y nombre incorrecto. Ver `backend/docs/sprint-10/evidencias-b3/`.
- No ejecutado: contra la VM real y el dominio (sin acceso a Azure).

## 6. Evidencia

`backend/docs/sprint-10/evidencias-b3/` (el stack de prueba está en `stack-verificacion-local.compose.yml`).

## 7. Pendientes y riesgos

- DNS + NSG + deploy + emisión del certificado (manual).
- Cierre del 3000 coordinado con B8 (APK HTTPS distribuido primero).
- B6: se agregó un servicio al compose; Dockerfile y workflows no se tocaron. El health check del deploy sigue en `http://localhost:3000/`.
- docker-compose v1 en la VM: la sintaxis usada (`${VAR:-default}` en `ports`, `volumes` nombrados, `443:443/udp`) está dentro del formato 3.8, pero solo se validó con Docker Compose v2 en local. Revisar el primer `docker-compose up` en la VM.

## 8. Rollback

- Proxy: `docker-compose stop caddy` o `git revert` del commit de B3 + deploy; mientras el 3000 siga publicado el API responde por HTTP.
- 3000 cerrado demasiado pronto: quitar `BACKEND_BIND_ADDR` + redeploy + restaurar la regla del NSG.
- PgBouncer: `PGBOUNCER_SERVER_TLS_SSLMODE=require` (cifra sin verificar; solo emergencia).
- `TRUST_PROXY=false` vuelve al comportamiento anterior del limitador.
