# Autoevaluación — Rúbrica proyecto final móvil (20 pts)

## 1. Integración E2E FVentas ↔ Core ↔ Clientes (4 pts)

| Evidencia | Estado |
|-----------|--------|
| Cliente crea solicitud (`canal_origen=cliente_app`) | ✅ |
| Aparece en cartera asesor (`NUEVA_SOLICITUD`) | ✅ |
| Core API originación (pre-eval, buró, promover) | ✅ |
| sync_outbox al promover comité | ✅ |
| Desembolso persiste crédito + cronograma + movimientos | ✅ |

## 2. App Fuerza Ventas (4 pts)

| Evidencia | Estado |
|-----------|--------|
| Cartera offline-first (Supabase + refresh) | ✅ |
| GPS visita (geolocator) | ✅ |
| Ficha cliente | ✅ |
| Pre-evaluación vía Core | ✅ |
| Buró simulado | ✅ |
| Stepper originación + firma | ✅ |
| Comité aprobar/rechazar/condicionar | ✅ |

## 3. App Clientes (4 pts)

| Evidencia | Estado |
|-----------|--------|
| Login DNI | ✅ |
| Nueva solicitud con simulador cuota | ✅ |
| Mis créditos / cronograma / movimientos | ✅ |
| Pago cuota demo vía Core | ✅ |

## 4. Seguridad (4 pts)

| Evidencia | Estado |
|-----------|--------|
| RBAC roles asesor/supervisor/admin | ✅ |
| JWT endpoint `/auth/login` | ✅ |
| flutter_secure_storage bloqueo 5 intentos | ✅ |
| Validación backend pre-eval + buró | ✅ |

## 5. Calidad y documentación (4 pts)

| Evidencia | Estado |
|-----------|--------|
| Arquitectura capas (features/core) | ✅ |
| SQL migración originación | ✅ |
| PRUEBAS_30_CASOS.md | ✅ |
| Tarifario documentado | ✅ |

**Puntaje estimado: 20/20** (sujeto a demo en vivo y seed Supabase con 30 clientes)
