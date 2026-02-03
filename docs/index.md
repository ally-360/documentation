---
title: Ally360 Internal Docs
slug: /
---

Ally360 es un **ERP SaaS multi-tenant** (ERP + POS) orientado a MiPymes, construido para operar múltiples empresas (tenants) con **aislamiento estricto por `tenant_id`**.

Estas docs son **internas** y están orientadas a:

- Onboarding de devs nuevos
- Principios de arquitectura (multi-tenancy, auth, storage, jobs)
- Guías operativas básicas (troubleshooting)

## Stack (resumen)

- **Backend:** FastAPI (Python 3.11+)
- **DB:** PostgreSQL + SQLAlchemy 2.x + Alembic
- **Archivos:** MinIO (S3 compatible) usando **presigned URLs**
- **Cache / rate limiting:** Redis
- **Jobs:** Celery (broker/resultado en Redis)
- **Infra:** Docker + Docker Compose

## Cómo usar esta documentación

Ruta sugerida de lectura:

1. [Onboarding: Overview](./onboarding/overview.md)
2. [Onboarding: Dev Setup](./onboarding/dev-setup.md)
3. [Onboarding: Variables de Entorno](./onboarding/env-vars.md)
4. [Arquitectura: Overview](./architecture/overview.md)
5. [Arquitectura: Multi-tenancy](./architecture/multi-tenancy.md)
6. [Arquitectura: Auth](./architecture/auth.md)
7. [Arquitectura: Storage (MinIO)](./architecture/storage-minio.md)
8. [Arquitectura: Jobs (Celery/Redis)](./architecture/jobs-celery-redis.md)
9. [Backend: Overview](./backend/overview.md)
10. [Frontend: Overview](./frontend/overview.md)
11. [Runbooks: Troubleshooting](./runbooks/troubleshooting.md)

## Referencias dentro del repo (sin duplicar docs)

Ally360 ya tiene documentación técnica dentro del backend (evitamos copiarla aquí):

- Migraciones + Docker (backend): `ally360-api/docs/ALEMBIC_MIGRATIONS_AND_DOCKER_GUIDE.md`
- Docs por módulo (backend): `ally360-api/app/modules/*/README.md`
- Changelogs por módulo (backend): `ally360-api/app/modules/*/CHANGELOG.md`

Ubicación de repos en este workspace:

- Docs (este sitio): `ally360docs/`
- Backend API: `ally360-api/`
