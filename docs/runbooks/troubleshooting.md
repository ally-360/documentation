---
title: Troubleshooting
---

Esta guía apunta a problemas frecuentes y a checks rápidos.

Comandos de apoyo:

```bash
docker compose ps
docker compose logs -f api
```

## Presigned URLs (MinIO)

Síntomas:
- URL abre pero falla con error de firma / “signature mismatch”
- URL apunta a host interno del contenedor

Acciones:
- Verificar `MINIO_PUBLIC_HOST=localhost` y `MINIO_PUBLIC_PORT=9000` en dev.
- Asegurar que las URLs se firmen con el host público (no `minio`).

Checks adicionales (cuando la URL sale bien pero el upload/download falla):
- Confirmar `MINIO_PUBLIC_USE_SSL` (dev suele ser `false`).
- Confirmar expiración (`MINIO_URL_EXPIRATION_UPLOAD`/`DOWNLOAD`).
- Confirmar límites de tamaño (`MINIO_MAX_FILE_SIZE_MB`, `MAX_FILE_SIZE`).

## Columna faltante después de cambiar modelos

Síntoma:
- Error SQL indicando columna inexistente tras agregar un campo

Acción:
- Crear y aplicar migración Alembic:

```bash
docker compose exec api python migrate.py create "mensaje"
docker compose exec api python migrate.py upgrade
```

Nota: evitar `AUTO_CREATE_TABLES_FROM_MODELS=true` para “evolucionar” schemas; es solo para bootstrap en DB vacía.

## Errores de autorización por empresa (tenant)

Síntoma:
- 403 al acceder a endpoints de negocio

Causas comunes:
- Falta `X-Company-ID` cuando se usa `access-token`.
- Token de contexto (`context-token`) corresponde a otra empresa.
- El usuario no pertenece a la empresa (membership).

Acción:
- Confirmar envío de `X-Company-ID` o uso de token `type=context`.

Checklist rápido:
- ¿Estás usando `context-token` tras seleccionar empresa?
- Si usas `access-token`: ¿`X-Company-ID` coincide con la empresa?
- ¿El usuario pertenece a esa empresa? (membership)

## Error ARRAY.contains() no implementado

Síntoma:
- Errores al usar `contains` sobre arrays

Acción:
- Usar tipos de PostgreSQL adecuados (ej. `sqlalchemy.dialects.postgresql.ARRAY`) en modelos/queries.

## Rate limiting (429 / bloqueos)

Síntoma:
- Requests empiezan a fallar tras cierto volumen

Acciones:
- Revisar `RATE_LIMIT_REQUESTS` y `RATE_LIMIT_WINDOW`.
- Confirmar conectividad a Redis (`REDIS_HOST`, `REDIS_PORT`, `REDIS_DB`).

Referencia completa de variables: ver Onboarding → Variables de Entorno.
