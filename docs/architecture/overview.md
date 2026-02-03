---
title: Overview
---

## Vista general

Ally360 sigue una arquitectura en capas y separa responsabilidades por módulos.

El objetivo de esta arquitectura es que puedas:
- implementar features nuevas sin mezclar responsabilidades,
- mantener aislamiento multi-tenant por defecto,
- y sostener el rendimiento a medida que crece el tráfico.

Diagrama textual (simplificado):

```
┌─────────────────────────────────────────────┐
│            FastAPI Routers (API)            │
│  - Validación request/response (Pydantic)   │
└──────────────────────────┬──────────────────┘
                           │
┌──────────────────────────▼──────────────────┐
│                 Services (Use cases)        │
│  - Reglas de negocio                         │
│  - Orquestación (DB, MinIO, Redis, Celery)  │
└──────────────────────────┬──────────────────┘
                           │
┌──────────────────────────▼──────────────────┐
│                    CRUD / Repos             │
│  - Acceso a datos (SQLAlchemy)              │
│  - Queries siempre por tenant_id            │
└──────────────────────────┬──────────────────┘
                           │
┌──────────────────────────▼──────────────────┐
│              PostgreSQL (multi-tenant)      │
│  - Alembic migrations                        │
│  - Índices compuestos con tenant_id         │
└─────────────────────────────────────────────┘

Infra / Integraciones:
- Redis: cache + rate limiting
- Celery: trabajos asíncronos
- MinIO: objetos vía presigned URLs
```

## Principios

- **`tenant_id` en todo:** modelos, constraints e índices (cuando aplique).
- **Separación por capas:** routers sin lógica compleja; reglas en services; DB en crud.
- **Seguridad multi-tenant:** validación de pertenencia usuario↔empresa, y 403 ante mismatch.
- **Performance:** paginación en listas grandes (limit/offset o keyset), evitar N+1, usar índices.
- **Observabilidad:** logs útiles y errores consistentes.

## Convenciones que se aplican en todo el backend

- **UUIDs** como identificadores.
- Cambios de schema: **una migración Alembic por cambio de modelos**.
- Listas grandes: paginación (limit/offset o keyset) y topes (`DEFAULT_PAGE_SIZE`, `MAX_PAGE_SIZE`).
- Archivos: MinIO solo vía **presigned URLs**; metadata en DB.
- Seguridad: validación estricta y autorización por rol + empresa.

Siguiente recomendado: [Multi-tenancy](./multi-tenancy.md)
