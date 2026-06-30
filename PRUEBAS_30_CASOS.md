# Pruebas — 30 casos académicos (Banco Andino / Microempresa)

## Producto y tarifario

| Parámetro | Valor |
|-----------|-------|
| Producto | Crédito Empresarial Microempresa |
| TEA sin desgravamen | 43,92% |
| TEA con desgravamen | 40,92% |
| Monto | S/ 1 000 – 20 000 |
| Plazo | 3 – 36 meses |

### Verificación caso 1

- Monto: S/ 1 000  
- Plazo: 12 meses  
- TEA: 43,92%  
- **Cuota esperada: S/ 100,95**

Endpoint Core: `GET /originacion/verificar-caso-1`

## Flujo E2E

1. **App Clientes** — Login con DNI → Nueva solicitud → estado `enviado`, canal `cliente_app`
2. **Cartera FVentas** — Aparece como `NUEVA_SOLICITUD`
3. **Gestión originación** — GPS → Pre-eval → Buró → Firma → Comité
4. **Comité** — Aprobar / Condicionar / Rechazar → Desembolso
5. **App Clientes** — Ver crédito, cronograma y movimientos

## Buró simulado (último dígito DNI)

| Dígito | Calificación | Resultado típico |
|--------|--------------|------------------|
| 0,1,3,6,9 | NORMAL | Procede |
| 2,8 | CPP | Condicionar |
| 5 | DEFICIENTE | Condicionar |
| 4 | DUDOSO | Rechazar (casos 29-30) |
| 7 | PERDIDA + inhabilitados | Rechazar (caso 28) |

Endpoint: `POST /originacion/buro` body `{"numero_documento":"40118120"}`

## DNIs de prueba (extracto)

| Caso | DNI | Monto | Resultado |
|------|-----|-------|-----------|
| 1 | 40118120 | 1 000 | Aprobado |
| 25 | 41552052 | 8 000 | Condicionado |
| 28 | 43337037 | 5 000 | Rechazado (PERDIDA) |
| 29 | 41884084 | 14 000 | Rechazado (DUDOSO) |
| 30 | 43334034 | 12 000 | Rechazado (DUDOSO) |

## Credenciales demo

- Cliente: `{DNI}@confianza.local` / contraseña según seed Supabase (típ. `123456`)
- Asesor código `1001`: `asesor001@confianza.local`
- Core Web: `admin` / `admin123`

## Checklist Play Store

- [x] Permiso INTERNET en manifest release
- [x] Etiquetas legibles de app
- [x] HTTPS por defecto + excepción dev LAN
- [x] Bloqueo 5 intentos login (secure storage)
- [x] Permiso ubicación solo FVentas (GPS visita)

## Comandos de verificación

```bash
# Core — caso 1
curl http://localhost:8000/originacion/verificar-caso-1

# Flutter analyze
cd app_clientes && flutter analyze
cd app_fuerza_ventas && flutter analyze
```
