---
title: Jobs (Celery + Redis)
---

## Para qué usamos jobs

Celery permite mover trabajo pesado o no-crítico fuera del request/response:

- Envío de emails (verificación, invitaciones)
- Procesamiento de archivos (antivirus, thumbnails, parsing)
- Reportes
- Tareas programadas

## Redis: roles típicos

- **Broker/result backend** para Celery
- **Cache** de catálogos y permisos por tenant
- **Rate limiting** por tenant

Variables relacionadas (ver detalle en onboarding):
- Redis: `REDIS_HOST`, `REDIS_PORT`, `REDIS_DB`
- Rate limit: `RATE_LIMIT_REQUESTS`, `RATE_LIMIT_WINDOW`

## Recomendaciones

- Diseñar tareas idempotentes cuando sea posible.
- Evitar pasar objetos grandes; preferir IDs y re-consulta con `tenant_id`.
- Loggear `tenant_id` y `correlation_id` cuando aplique.

Checklist cuando una tarea “parece no correr”:
- ¿Está corriendo el worker de Celery en tu `docker compose`?
- ¿Redis está arriba y accesible (host/puerto correctos)?
- ¿Los logs del worker muestran encolado/ejecución?
