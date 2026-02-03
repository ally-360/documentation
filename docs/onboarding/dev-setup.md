---
title: Dev Setup (Docker)
---

Esta guía describe el flujo recomendado para levantar el backend usando Docker Compose.

Objetivo: que puedas levantar el stack, aplicar migraciones, correr seeds y validar que todo está accesible.

Antes de empezar:
- Backend: `ally360-api/`
- Variables: copiar `ally360-api/.env.example` → `ally360-api/.env`
- Referencia completa: ver [Variables de Entorno](./env-vars.md)

## Pasos

### 1) Preparar `.env`

- En `ally360-api/`, copiar `.env.example` → `.env`.
- Recomendación mínima para dev: mantener defaults, pero cambia al menos `APP_SECRET_STRING` si vas a compartir el entorno.

Puntos críticos que rompen el 80% de issues en dev:
- Presigned URLs: `MINIO_PUBLIC_HOST=localhost`, `MINIO_PUBLIC_PORT=9000`, `MINIO_PUBLIC_USE_SSL=false`
- Docker: `POSTGRES_HOST=postgres`, `REDIS_HOST=redis`, `MINIO_HOST=minio`

### 2) Levantar servicios

Desde `ally360-api/`:

```bash
docker compose up --build
```

Comandos útiles para verificar estado:

```bash
docker compose ps
```

```bash
docker compose logs -f api
```

Qué esperar:
- Se levantan contenedores para API, postgres, redis y minio.
- Si `AUTO_RUN_MIGRATIONS=true`, el contenedor puede ejecutar migraciones y seeds al arrancar (depende del entrypoint).

### 3) Aplicar migraciones (recomendado siempre)

```bash
docker compose exec api python migrate.py upgrade
```

Qué esperar:
- La base queda en el último schema definido por Alembic.

### 4) Ejecutar seeds (idempotentes)

```bash
docker compose exec api python scripts/seed_bootstrap.py
```

Qué esperar:
- Seeds de bootstrap corren sin duplicar información.

## Servicios expuestos (local)

- API: `http://localhost:8000`
- OpenAPI (Swagger): `http://localhost:8000/docs`
- PostgreSQL: `localhost:5432` (contenedor postgres)
- Redis: `localhost:6379`
- MinIO API: `http://localhost:9000`
- MinIO Console: `http://localhost:9001`

## Variables de entorno críticas (resumen)

JWT / seguridad
- `APP_SECRET_STRING`
- `ALGORITHM` (p.ej. `HS256`)

Base de datos
- `POSTGRES_USER`, `POSTGRES_PASSWORD`, `POSTGRES_DB`, `POSTGRES_HOST`

Redis
- `REDIS_HOST`

MinIO
- `MINIO_HOST`
- `MINIO_ACCESS_KEY`, `MINIO_SECRET_KEY`
- `MINIO_PUBLIC_HOST`, `MINIO_PUBLIC_PORT` (importante para presigned URLs en dev)

Email (opcional)
- `EMAIL_SMTP_SERVER`, `EMAIL_USERNAME`, `EMAIL_PASSWORD`, `EMAIL_FROM`
- `FRONTEND_URL`

Ver explicación variable por variable en: [Variables de Entorno](./env-vars.md)

## Comandos útiles

- Tests:

```bash
docker compose exec api pytest -q
```

- Crear migración:

```bash
docker compose exec api python migrate.py create "mensaje"
```

- Aplicar migraciones:

```bash
docker compose exec api python migrate.py upgrade
```

- Abrir una shell en el contenedor API (debug):

```bash
docker compose exec api bash
```

Notas:
- Cada cambio de modelos debe ir acompañado de **migración Alembic**.
- Para detalles de migraciones en producción, ver: `ally360-api/docs/ALEMBIC_MIGRATIONS_AND_DOCKER_GUIDE.md`.

## Validación rápida (sin inventar endpoints)

- Abre `http://localhost:8000/docs` y confirma que carga OpenAPI.
- Confirma que MinIO está accesible:
	- API: `http://localhost:9000`
	- Console: `http://localhost:9001`
- Si el login/selección de empresa falla, revisa la sección de multi-tenancy y la configuración JWT.

## Problemas comunes

- Presigned URLs no funcionan desde el navegador:
	- Revisar `MINIO_PUBLIC_*` (host/port/ssl).

- Cambié un modelo y no aparece la columna:
	- Falta migración Alembic (crear + aplicar).

- 403 por empresa:
	- Revisar `context-token` vs `X-Company-ID` y pertenencia usuario↔empresa.
