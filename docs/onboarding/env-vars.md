---
title: Variables de Entorno (.env)
---

Esta página documenta **todas** las variables de `.env.example` del backend (repo `ally360-api`).

Reglas:
- En local con Docker Compose, la mayoría de valores por defecto funcionan.
- En producción, **no** uses secretos por defecto.
- Cuando algo “misterioso” falla (auth, presigned URLs, seeds), casi siempre es `.env`.

## 1) Base de datos (PostgreSQL)

- `POSTGRES_USER`
  - Usuario de la base de datos.
  - Default: `ally_user`.

- `POSTGRES_PASSWORD`
  - Password del usuario de la base de datos.
  - Default: `ally_pass`.

- `POSTGRES_DB`
  - Nombre de la base de datos.
  - Default: `ally_db`.

- `POSTGRES_HOST`
  - Host de PostgreSQL.
  - En Docker Compose: típicamente `postgres` (nombre del servicio).
  - En ejecución “sin Docker”: normalmente `localhost`.

- `POSTGRES_PORT`
  - Puerto de PostgreSQL.
  - Default: `5432`.

## 2) Redis

- `REDIS_HOST`
  - Host de Redis.
  - En Docker Compose: típicamente `redis`.

- `REDIS_PORT`
  - Puerto de Redis.
  - Default: `6379`.

- `REDIS_DB`
  - Índice de DB en Redis.
  - Default: `0`.

- `REDIS_PASSWORD`
  - Password opcional.
  - Si no hay password, dejar vacío.

## 3) MinIO (conexión interna del backend)

Estas variables controlan cómo el backend se conecta a MinIO **dentro** de la red de Docker.

- `MINIO_HOST`
  - Host interno de MinIO.
  - En Docker Compose: típicamente `minio`.

- `MINIO_PORT`
  - Puerto de la API de MinIO.
  - Default: `9000`.

- `MINIO_ACCESS_KEY`
  - Access key.
  - Default: `minioadmin` (solo para dev).

- `MINIO_SECRET_KEY`
  - Secret key.
  - Default: `minioadmin` (solo para dev).

- `MINIO_BUCKET_NAME`
  - Nombre del bucket.
  - Default: `ally360`.

- `MINIO_USE_SSL`
  - Si el backend se conecta por HTTPS a MinIO (conexión interna).
  - En Docker dev normalmente `false`.

## 4) MinIO (URLs públicas para presigned URLs)

Estas variables controlan el **host/puerto/esquema** que se inserta en las presigned URLs que consumirá el frontend/cliente.

- `MINIO_PUBLIC_HOST`
  - En dev: `localhost`.
  - En prod: dominio público (por ejemplo `s3.ally360.co`).

- `MINIO_PUBLIC_PORT`
  - En dev: `9000`.
  - En prod con HTTPS: normalmente `443`.

- `MINIO_PUBLIC_USE_SSL`
  - En dev: `false`.
  - En prod: `true`.

Notas importantes:
- Si el backend firma con `MINIO_PUBLIC_HOST=minio` (host interno), el navegador/cliente no podrá acceder a la URL desde fuera del network de Docker.
- Puertos “limpios” recomendados:
  - HTTPS prod: puerto `443` → URL sin `:443`.
  - HTTP dev: puerto `9000` → URL con `:9000`.

## 5) MinIO (expiraciones y límites)

- `MINIO_URL_EXPIRATION_UPLOAD`
  - Tiempo de expiración (segundos) para presigned URL de **subida**.
  - Default: `900` (15 min).

- `MINIO_URL_EXPIRATION_DOWNLOAD`
  - Tiempo de expiración (segundos) para presigned URL de **descarga**.
  - Default: `3600` (1h).

- `MINIO_MAX_FILE_SIZE_MB`
  - Límite recomendado de tamaño máximo de archivo (MB) para subir.
  - Default: `20`.

## 6) JWT y sesiones

- `APP_SECRET_STRING`
  - Secreto para firmar JWT.
  - En producción debe ser fuerte y privado.

- `ALGORITHM`
  - Algoritmo de firma.
  - Default: `HS256`.

- `ACCESS_TOKEN_EXPIRE_MINUTES`
  - Minutos de expiración del access token.
  - Default: `30`.

- `REFRESH_TOKEN_EXPIRE_DAYS`
  - Días de expiración del refresh token.
  - Default: `7`.

## 7) Rate limiting

- `RATE_LIMIT_REQUESTS`
  - Cantidad de requests permitidos en una ventana.
  - Default: `100`.

- `RATE_LIMIT_WINDOW`
  - Ventana en segundos.
  - Default: `60`.

## 8) Límites de request / uploads

- `MAX_FILE_SIZE`
  - Tamaño máximo permitido (bytes).
  - Default: `52428800` (50MB).

Nota: este límite puede existir además de límites específicos de MinIO/presigned.

## 9) Paginación

- `DEFAULT_PAGE_SIZE`
  - Tamaño de página por defecto.
  - Default: `20`.

- `MAX_PAGE_SIZE`
  - Tamaño de página máximo permitido.
  - Default: `100`.

## 10) Email

- `EMAIL_SMTP_SERVER`
  - Host del servidor SMTP.

- `EMAIL_SMTP_PORT`
  - Puerto SMTP.
  - Default: `587`.

- `EMAIL_USE_TLS`
  - Usar TLS.
  - Default: `true`.

- `EMAIL_USERNAME`
  - Usuario SMTP.

- `EMAIL_PASSWORD`
  - Password SMTP (en Gmail suele ser app password).

- `EMAIL_FROM`
  - Dirección “From”.

- `EMAIL_FROM_NAME`
  - Nombre visible del remitente.

- `FRONTEND_URL`
  - URL base del frontend (usada en links de email, etc.).
  - Default dev: `http://localhost:3000`.

## 11) Entorno y debug

- `ENVIRONMENT`
  - Ambiente: `development`, `staging`, `production`.
  - Default: `development`.

- `DEBUG`
  - Flag de debug.
  - En producción debe ser `false`.

## 12) Bootstrap (solo desarrollo)

- `AUTO_CREATE_TABLES_FROM_MODELS`
  - Si `true`, crea tablas faltantes desde modelos al iniciar.
  - **Úsalo solo** en DB vacía y dev. Para evolución del schema usar Alembic.

- `AUTO_SEED_DEFAULTS`
  - Si `true`, corre seeds al iniciar (dev).

## 13) Docker entrypoint

- `AUTO_RUN_MIGRATIONS`
  - Si `true`, el entrypoint corre `migrate.py upgrade` y luego `scripts/seed_bootstrap.py`.
  - Recomendado para Docker.

## Troubleshooting (rápido)

- Presigned URL firma pero no abre:
  - Revisar `MINIO_PUBLIC_HOST`, `MINIO_PUBLIC_PORT`, `MINIO_PUBLIC_USE_SSL`.

- 401 frecuentes:
  - Revisar `APP_SECRET_STRING` (que no cambie entre instancias), expiraciones, reloj del sistema.

- 403 por tenant:
  - Revisar uso de `context-token` vs `X-Company-ID` + membership.
