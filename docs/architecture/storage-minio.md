---
title: Storage (MinIO)
---

## Principio

Los archivos se manejan **solo** con **presigned URLs** (subida/descarga). No se expone MinIO como servidor de archivos directo.

## Bucket y prefijos

- Bucket único por entorno (p.ej. `ally360`).
- Prefijo recomendado:

```
ally360/{tenant_id}/{module}/{yyyy}/{mm}/{dd}/{uuid}
```

## Variables de entorno relevantes

- Conexión interna (backend → MinIO, dentro de Docker):
  - `MINIO_HOST`, `MINIO_PORT`
  - `MINIO_ACCESS_KEY`, `MINIO_SECRET_KEY`
  - `MINIO_BUCKET_NAME`
  - `MINIO_USE_SSL`

- URLs públicas (cliente → MinIO usando presigned URLs):
  - `MINIO_PUBLIC_HOST`, `MINIO_PUBLIC_PORT`, `MINIO_PUBLIC_USE_SSL`

- Expiraciones / límites:
  - `MINIO_URL_EXPIRATION_UPLOAD`, `MINIO_URL_EXPIRATION_DOWNLOAD`
  - `MINIO_MAX_FILE_SIZE_MB`, `MAX_FILE_SIZE`

En dev, las presigned URLs deben generarse con el **host público** (`MINIO_PUBLIC_HOST`/`MINIO_PUBLIC_PORT`), no con el nombre interno del contenedor.

Referencia completa de `.env`: ver [Variables de Entorno](../onboarding/env-vars.md)

## Troubleshooting rápido

- Presigned URL inaccesible o “signature mismatch”:
  - Confirmar `MINIO_PUBLIC_HOST=localhost` y `MINIO_PUBLIC_PORT=9000` (en dev).
  - Verificar que el backend firme usando el host público.

- La URL sale con `http://minio:9000/...`:
  - Estás firmando con host interno. Ajustar `MINIO_PUBLIC_*`.

- 403/AccessDenied al usar la URL:
  - Verificar expiración, método HTTP (PUT vs GET) y headers requeridos.

- Uploads fallan por tamaño:
  - Revisar `MINIO_MAX_FILE_SIZE_MB` y `MAX_FILE_SIZE`.
