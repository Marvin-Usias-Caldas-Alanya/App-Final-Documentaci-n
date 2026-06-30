# Tarifario Financiera Confianza — Ecosistema Móvil

Documento de referencia para tasas, montos, plazos y cálculo de cuotas en **Core Mobile API** y **App Fuerza de Ventas**.

---

## 1. Estado anterior vs. estado actual

| Aspecto | Antes | Ahora |
|---------|-------|-------|
| TEA usada | **45% fija** (inventada) | **Tarifario oficial T001-019** |
| Producto | Genérico "Microcrédito" | Producto según `tipo_negocio` del cliente |
| Montos/plazos | Sin validación | S/ 300–20,000 y 3–36 meses (Crédito Personal oficial) |
| Fuente | Ninguna | PDF y web de Financiera Confianza |

**Conclusión:** el proyecto **no** usaba el tarifario real. Ahora sí está centralizado y aplicado.

---

## 2. Fuente oficial consultada

| Documento | Enlace |
|-----------|--------|
| Tarifario Tasas Activas T001-019 (ene. 2026) | [PDF oficial](https://confianza.pe/docs/2026/01/T001-019%20Tarifario%20de%20tasas%20activas.pdf) |
| Crédito Personal (ejemplo cuota/TEA) | [confianza.pe/persona/credito-personal](https://confianza.pe/persona/credito-personal) |
| Transparencia (ITF, mora) | [confianza.pe/confianza/transparencia](https://confianza.pe/confianza/transparencia) |

**Vigencia implementada:** `T001-019` — aprobado 22.12.2025.

---

## 3. Tasas máximas por producto (TEA % — PEN)

Publicadas en el tarifario de tasas activas:

| Código interno | Producto Financiera Confianza | TEA máxima |
|----------------|-------------------------------|------------|
| `construyendo_confianza` | Construyendo Confianza (Agua y Saneamiento) | **82.00%** |
| `creditos_inclusion` | Créditos de Inclusión (Iniciando Oficios, Palabra de Mujer, PDM) | **105.00%** |
| `creditos_personales` | Créditos Personales (Personal Clásico, Educativo, etc.) | **87.65%** |
| `creditos_pymes` | Créditos Pymes (Iniciando Confianza, Emprendiendo Mujer, etc.) | **90.00%** |
| `credito_agropecuario` | Crédito Agropecuario | **90.00%** |

> La TEA máxima es el **tope legal/comercial** del producto. La tasa final se define en evaluación crediticia y puede ser menor.

---

## 4. Parámetros globales publicados

| Parámetro | Valor | Uso en el sistema |
|-----------|-------|-------------------|
| Base anual | **360 días** | Conversión TEA → tasa mensual |
| ITF | **0.005%** | Informativo (desembolsos/pagos en producción real) |
| Tasa moratoria TNA | **12.54%** | Referencia para mora (no simulada aún en demo) |
| Sistema de cuotas | **Francés** | Core + Flutter + cronograma Supabase |

---

## 5. TEA referencial para simulación (56%)

Financiera Confianza publica este **ejemplo oficial** en Crédito Personal:

- Préstamo: **S/ 1,000.00**
- Plazo: **12 cuotas mensuales**
- Cuota: **S/ 105.81**
- Intereses totales: **S/ 262.02**
- TCEA: **57.54%**
- **TEA promedio referencial: 56.00%**

El ecosistema usa **56% como TEA referencial** para simular cuotas en solicitudes y desembolsos demo, porque es el único ejemplo numérico completo publicado en la web oficial.

La tasa final negociada con el cliente puede variar hasta la **TEA máxima** del producto.

---

## 6. Montos y plazos referenciales

Según ficha de **Crédito Personal** (aplicados como rango base a todos los productos del tarifario interno):

| Concepto | Valor |
|----------|-------|
| Monto mínimo | S/ 300 |
| Monto máximo referencial | S/ 20,000 |
| Plazo mínimo | 3 meses |
| Plazo máximo referencial | 36 meses |
| Moneda | PEN (Soles) |

> Montos superiores son posibles sujeto a evaluación crediticia y límite legal; el sistema valida el rango referencial publicado.

---

## 7. Producto por defecto en App Fuerza de Ventas

La app de asesores atiende **microempresas y negocios**, por lo tanto:

- **Producto default:** `creditos_pymes` (Créditos Pymes)
- **TEA máxima:** 90%
- **TEA referencial simulación:** 56%

### Mapeo `tipo_negocio` → producto

| tipo_negocio del cliente | Producto tarifario |
|--------------------------|-------------------|
| microempresa, pyme, negocio, comercio, servicios | Créditos Pymes |
| agropecuario, agricola | Crédito Agropecuario |
| inclusion | Créditos de Inclusión |
| personal, consumo | Créditos Personales |
| vivienda, construccion | Construyendo Confianza |
| *(vacío u otro)* | Créditos Pymes (default) |

---

## 8. Fórmula de cuota (sistema francés)

```
i = (1 + TEA)^(1/12) - 1
cuota = monto × i × (1 + i)^n / ((1 + i)^n - 1)
```

Donde:
- `TEA` = tasa efectiva anual en decimal (ej. 56% → 0.56)
- `n` = número de cuotas mensuales

### Verificación con ejemplo oficial

| Entrada | Resultado esperado | Resultado sistema |
|---------|-------------------|-------------------|
| S/ 1,000 · 12 meses · TEA 56% | ~S/ 105.81 | ~S/ 105.81 |

---

## 9. Dónde está implementado en el código

### Core Mobile API (Python)

| Archivo | Responsabilidad |
|---------|-----------------|
| `app/core/tarifario.py` | Catálogo oficial, validaciones, resolución de producto |
| `app/services/creditos_service.py` | Cuota francesa y cronograma |
| `app/services/solicitudes_service.py` | Desembolso con TEA y producto del tarifario |
| `app/routes/tarifario.py` | API `GET /tarifario` y `GET /tarifario/producto` |

### App Fuerza de Ventas (Flutter)

| Archivo | Responsabilidad |
|---------|-----------------|
| `lib/core/tarifario/tarifario_confianza.dart` | Mismo catálogo y reglas |
| `lib/core/utils/credit_calculator.dart` | Cálculo de cuota y cronograma |
| `lib/features/solicitudes/views/solicitud_view.dart` | Simulación, validación y guardado de `tea_referencial` |

### App Clientes

Lee créditos y cronograma ya generados con la TEA guardada en Supabase. No crea solicitudes.

---

## 10. Flujo de datos

```
Asesor crea solicitud (App Fuerza Ventas)
    → Resuelve producto por tipo_negocio
    → Valida monto/plazo vs tarifario
    → Calcula cuota con TEA referencial 56%
    → Guarda tea_referencial + cuota_estimada en Supabase

Core aprueba / desembolsa
    → Usa tea_referencial de la solicitud
    → Genera cronograma francés
    → Registra crédito con nombre de producto tarifario

Cliente consulta cuotas (App Clientes)
    → Muestra cronograma generado
```

---

## 11. Endpoints API del tarifario

```http
GET /tarifario
GET /tarifario/producto?tipo_negocio=pyme
GET /tarifario/producto?codigo=creditos_pymes
```

Ejemplo respuesta resumida:

```json
{
  "version": "T001-019",
  "vigencia": "2026-01",
  "producto_default": "creditos_pymes",
  "parametros_globales": {
    "dias_base_anio": 360,
    "itf_porcentaje": 0.005,
    "tasa_moratoria_tna": 12.54
  }
}
```

---

## 12. Limitaciones actuales (demo académica)

1. **ITF (0.005%)** no se descuenta en el monto desembolsado.
2. **Seguro de desgravamen** no se incluye en la cuota (en producción real sí aplica).
3. **TCEA** no se calcula (solo TEA referencial).
4. **Mora** no aplica interés moratorio automático.
5. La TEA final de cada cliente debería venir de **evaluación crediticia**; hoy se usa la referencial 56% para toda simulación demo.

---

## 13. Actualizar el tarifario en el futuro

Cuando Financiera Confianza publique un nuevo PDF:

1. Descargar desde [confianza.pe](https://confianza.pe/confianza/transparencia) → Tarifario Tasas Activas.
2. Actualizar valores en:
   - `core_mobile_api/app/core/tarifario.py`
   - `app_fuerza_ventas/lib/core/tarifario/tarifario_confianza.dart`
3. Cambiar `TARIFARIO_VERSION` y `TARIFARIO_VIGENCIA`.
4. Probar cuota con el ejemplo oficial de la web.
5. Desplegar Core en Render y publicar nueva versión de la app.

---

## 14. Referencias legales

- Ley N° 28587 — Protección al consumidor financiero.
- Resolución SBS N° 3274-2017 — Conducta de mercado.
- Resolución SBS N° 03040-2025 — Cláusulas generales de crédito (actualización 27.10.2025).

---

*Documento generado para el proyecto Financiera Confianza — Ecosistema Móvil. Tarifario basado en fuentes públicas de confianza.pe.*
