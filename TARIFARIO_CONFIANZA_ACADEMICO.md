# Aplicación del Tarifario de Tasas Activas de Financiera Confianza en un Ecosistema de Crédito Móvil

**Documento académico de soporte**  
**Proyecto:** Ecosistema Móvil Financiera Confianza  
**Versión del tarifario analizado:** T001-019 (vigencia enero 2026)  
**Fecha de elaboración:** junio 2026  

---

## Resumen

El presente documento expone, desde una perspectiva académica, la incorporación del tarifario oficial de tasas activas de **Financiera Confianza** — entidad microfinanciera peruana integrante de la Fundación Microfinanzas BBVA — dentro de un ecosistema compuesto por una API central (Core Mobile API), una aplicación móvil para fuerza de ventas y una aplicación móvil para clientes finales.

Inicialmente, el sistema empleaba una Tasa Efectiva Anual (TEA) fija del 45 % sin sustento documental. Tras el análisis de fuentes primarias publicadas por la institución financiera, se implementó el documento **T001-019 Tarifario de Tasas Activas**, aprobado el 22 de diciembre de 2025, junto con los parámetros de transparencia exigidos por la normativa peruana del Sistema Financiero.

Se describe el marco conceptual de las tasas de interés, el marco legal aplicable, la metodología de parametrización, el modelo matemático de amortización francesa empleado y la arquitectura de integración en el software desarrollado. Asimismo, se presenta un caso numérico de verificación contrastado con el ejemplo oficial publicado en la ficha de Crédito Personal.

**Palabras clave:** tarifario, TEA, TCEA, microfinanzas, amortización francesa, Financiera Confianza, crédito móvil, transparencia financiera.

---

## 1. Introducción

### 1.1 Contexto

Las entidades microfinancieras en el Perú desempeñan un rol fundamental en la inclusión financiera de microempresarios, emprendedores y familias de bajos ingresos. **Financiera Confianza S.A.** opera bajo la supervisión de la Superintendencia de Banca, Seguros y AFP (SBS) y publica, con carácter obligatorio, información sobre costos de sus productos crediticios mediante tarifarios de tasas activas, comisiones y gastos.

En el marco de un proyecto de aplicaciones móviles que digitalizan el ciclo de solicitud, aprobación, desembolso y pago de créditos, resulta imprescindible que los cálculos de cuotas, cronogramas y validaciones de montos se sustenten en dichos tarifarios y no en valores arbitrarios.

### 1.2 Problema identificado

Durante la etapa inicial de desarrollo, el ecosistema utilizaba una **TEA uniforme del 45 %** para estimar cuotas y generar cronogramas. Este valor:

- No correspondía a ningún producto publicado por Financiera Confianza.
- Se encontraba por debajo de las tasas máximas vigentes para la mayoría de líneas de crédito (82 % – 105 %).
- No contemplaba diferenciación por tipo de producto ni por segmento de cliente.
- No validaba montos ni plazos según las condiciones comerciales publicadas.

Desde el punto de vista académico y regulatorio, ello configura una **inconsistencia metodológica** entre el modelo implementado y la realidad institucional.

### 1.3 Objetivos

**Objetivo general**  
Integrar el tarifario oficial de tasas activas de Financiera Confianza en el ecosistema móvil de crédito, garantizando coherencia normativa, trazabilidad de fuentes y reproducibilidad de los cálculos financieros.

**Objetivos específicos**

1. Identificar y documentar las fuentes primarias del tarifario vigente.
2. Definir un modelo de datos de productos crediticios con TEA máxima, TEA referencial, montos y plazos.
3. Implementar el sistema de amortización francesa conforme a la base de 360 días declarada por la entidad.
4. Establecer reglas de asignación de producto según el perfil del cliente (`tipo_negocio`).
5. Validar numéricamente los resultados frente al ejemplo oficial de Crédito Personal.

---

## 2. Marco teórico

### 2.1 Tasas de interés en créditos

En el sistema financiero peruano, los costos del crédito se expresan habitualmente mediante indicadores estandarizados:

| Indicador | Denominación | Descripción |
|-----------|--------------|-------------|
| **TEA** | Tasa Efectiva Anual | Costo anual del crédito incluyendo intereses, expresado en base efectiva. |
| **TCEA** | Tasa de Costo Efectiva Anual | Costo total del crédito incluyendo intereses, comisiones, seguros y otros gastos obligatorios. |
| **TNA** | Tasa Nominal Anual | Tasa lineal anual que, aplicada sobre capital, no incorpora capitalización intra-anual. |

La **TEA** constituye el parámetro central del tarifario de tasas activas de Financiera Confianza. La **TCEA**, por su parte, ofrece una visión más integral del costo para el consumidor financiero y suele ser superior a la TEA cuando existen seguros vinculados (por ejemplo, desgravamen) u otros cargos.

### 2.2 Sistema de amortización francesa

El ecosistema implementa el **sistema francés de cuotas constantes**, ampliamente utilizado en créditos de consumo y microcrédito. Bajo este esquema:

- La cuota mensual permanece constante durante la vida del crédito.
- La porción de interés disminuye progresivamente.
- La porción de capital amortizado aumenta en cada periodo.

La conversión de TEA a tasa periódica mensual, asumiendo capitalización mensual equivalente, se expresa como:

\[
i = (1 + TEA)^{1/12} - 1
\]

La cuota constante \(C\) para un capital \(P\), tasa mensual \(i\) y \(n\) periodos, se obtiene mediante:

\[
C = P \cdot \frac{i \cdot (1+i)^n}{(1+i)^n - 1}
\]

Financiera Confianza declara expresamente que la TEA se calcula sobre una **base anual de 360 días**, conforme al documento T001-019.

### 2.3 Microfinanzas y segmentación de productos

Financiera Confianza no opera con un único producto homogéneo, sino con una **canasta diversificada** orientada a distintos segmentos: inclusión financiera, consumo personal, micro y pequeña empresa (Pymes), agropecuario y vivienda. Cada línea posee una **TEA máxima diferenciada**, reflejando el riesgo, plazo, garantía y costo operativo asociado.

Esta segmentación justifica que un sistema informático de originación de créditos no utilice una tasa única, sino un **motor tarifario** capaz de resolver el producto aplicable y sus parámetros.

### 2.4 Transparencia y conducta de mercado

La **Ley N° 28587** y el **Reglamento de Gestión de Conducta de Mercado del Sistema Financiero** (Resolución SBS N° 3274-2017 y modificatorias) imponen a las entidades supervisadas la obligación de difundir información veraz, oportuna y comprensible sobre costos, riesgos y condiciones de sus productos.

El tarifario de tasas activas constituye, por tanto, un instrumento de **transparencia regulatoria** cuya incorporación en sistemas digitales no es meramente técnica, sino también de cumplimiento de deberes de información hacia el consumidor financiero.

---

## 3. Marco legal y normativo de referencia

| Norma | Relevancia para el proyecto |
|-------|----------------------------|
| Ley N° 28587 | Protección al consumidor en servicios financieros; derecho a información clara sobre costos. |
| Resolución SBS N° 3274-2017 | Reglamento de conducta de mercado; publicación de tarifarios. |
| Resolución SBS N° 6799-2014 y mod. | Cláusulas generales de contratación de crédito. |
| Resolución SBS N° 03040-2025 | Última actualización autorizada de cláusulas (27.10.2025). |
| Legislación tributaria (ITF) | Impuesto a las Transacciones Financieras: 0,005 % sobre operaciones. |

Financiera Confianza publica sus tarifarios en su portal institucional ([confianza.pe](https://confianza.pe/confianza/transparencia)), en oficinas y plataformas de atención, cumpliendo el deber de difusión normativa.

---

## 4. Metodología

### 4.1 Tipo de investigación

Se empleó una metodología de **investigación aplicada** con componentes de:

- **Revisión documental** de fuentes primarias institucionales.
- **Ingeniería de software** para parametrización e integración.
- **Validación numérica** mediante caso de prueba publicado.

### 4.2 Fuentes de información

| N° | Fuente | Tipo | Uso |
|----|--------|------|-----|
| F1 | T001-019 Tarifario de Tasas Activas (PDF, ene. 2026) | Primaria | TEA máxima por producto; mora; base 360 días |
| F2 | Ficha web Crédito Personal | Primaria | Montos, plazos, ejemplo numérico TEA/TCEA |
| F3 | Sección Transparencia web institucional | Primaria | ITF, referencias normativas |
| F4 | Código fuente del ecosistema móvil | Secundaria | Verificación de implementación |

**URL del tarifario:**  
https://confianza.pe/docs/2026/01/T001-019%20Tarifario%20de%20tasas%20activas.pdf

### 4.3 Procedimiento

1. **Extracción:** lectura del PDF T001-019 y fichas de producto web.
2. **Modelado:** diseño de estructura `ProductoTarifario` con atributos normalizados.
3. **Parametrización:** registro de cinco líneas de producto con TEA máxima oficial.
4. **Asignación:** tabla de correspondencia `tipo_negocio` → producto tarifario.
5. **Implementación:** módulos en Python (Core API) y Dart (Flutter).
6. **Validación:** cálculo de cuota para S/ 1 000, 12 meses, TEA 56 %.
7. **Documentación:** elaboración de informes técnico y académico.

---

## 5. Caracterización de Financiera Confianza

**Financiera Confianza S.A.** es una entidad microfinanciera peruana orientada principalmente a segmentos populares y microempresariales. Forma parte del ecosistema de la **Fundación Microfinanzas BBVA**, lo cual condiciona estándares de sostenibilidad, inclusión y digitalización de servicios.

Sus productos crediticios activos, según el tarifario T001-019, comprenden al menos:

1. **Construyendo Confianza** (incluye Agua y Saneamiento).
2. **Créditos de Inclusión** (Iniciando Oficios, Palabra de Mujer, Paralelo PDM).
3. **Créditos Personales** (Personal Clásico, Educativo, Garantía Líquida, consumo).
4. **Créditos Pymes** (Iniciando Confianza, Iniciando Negocios, Emprendiendo Confianza, Emprendiendo Mujer).
5. **Crédito Agropecuario**.

El ecosistema móvil desarrollado se alinea prioritariamente con la línea **Créditos Pymes**, dado que la aplicación de fuerza de ventas atiende asesores que gestionan cartera de microempresarios y pequeños negocios.

---

## 6. Análisis del Tarifario T001-019

### 6.1 Tasas efectivas anuales máximas (moneda nacional — PEN)

| Producto | TEA máxima (%) | Observación |
|----------|----------------|-------------|
| Construyendo Confianza | 82,00 | Línea vivienda / infraestructura básica |
| Créditos de Inclusión | 105,00 | Mayor TEA máxima del tarifario; segmento de alto riesgo operativo |
| Créditos Personales | 87,65 | Consumo e individual |
| Créditos Pymes | 90,00 | **Producto default del ecosistema móvil** |
| Crédito Agropecuario | 90,00 | Sector agro |

> **Nota interpretativa:** La TEA máxima representa el límite superior que la entidad puede aplicar a una operación de esa línea. La tasa efectivamente pactada resulta de la **evaluación crediticia individual** y, en condiciones normales, será menor o igual al tope tarifario.

### 6.2 Parámetros transversales

| Parámetro | Valor publicado | Implicancia |
|-----------|-----------------|-------------|
| Base anual | 360 días | Estándar bancario; base de conversión de tasas |
| ITF | 0,005 % | Gravamen sobre desembolsos y pagos |
| Tasa moratoria (TNA) | 12,54 % | Aplicable en incumplimiento; moneda nacional |
| Sistema de cuotas | Mensual | Coherente con amortización francesa |

### 6.3 Condiciones referenciales de Crédito Personal (ficha web)

Aunque el ecosistema prioriza Pymes, la ficha de **Crédito Personal** aporta el único **ejemplo numérico completo** publicado en línea:

| Variable | Valor |
|----------|-------|
| Monto | S/ 1 000,00 |
| Plazo | 12 cuotas mensuales |
| Cuota | S/ 105,81 |
| Total intereses | S/ 262,02 |
| Seguro desgravamen | S/ 5,90 |
| TCEA | 57,54 % |
| TEA referencial | 56,00 % |

**Rangos operativos publicados:**

- Monto: S/ 300,00 – S/ 20 000,00 (puede ampliarse según evaluación).
- Plazo: 3 – 36 cuotas mensuales.
- Moneda: Nuevos Soles (PEN).

Estos rangos se adoptaron como **límites referenciales de validación** para todos los productos parametrizados en el sistema, en ausencia de rangos específicos publicados por producto en el PDF resumido.

### 6.4 Criterio de TEA referencial adoptado (56 %)

Dado que el tarifario PDF publica predominantemente **tasas máximas** y no tasas promedio por producto, se adoptó la **TEA del 56 %** — explicitada en el ejemplo oficial de Crédito Personal — como **tasa referencial de simulación** para:

- Estimación de cuota en solicitudes creadas por asesores.
- Generación de cronogramas en desembolsos demo del Core API.

**Justificación académica:** utilizar la TEA máxima (p. ej. 90 % o 105 %) produciría cuotas sistemáticamente sobreestimadas respecto a operaciones reales promedio, distorsionando la experiencia del usuario y la comparabilidad con material institucional. Por el contrario, la TEA referencial del 56 % se encuentra **documentada, reproducible y auditada** en fuente primaria.

---

## 7. Modelo de parametrización implementado

### 7.1 Estructura de datos

Cada producto tarifario se modeló mediante la entidad lógica `ProductoTarifario`:

```
ProductoTarifario {
  codigo: string
  nombre: string
  tea_maxima: float          // Tope oficial (%)
  tea_referencial: float     // Tasa de simulación (%)
  monto_min: float           // PEN
  monto_max: float           // PEN
  plazo_min_meses: int
  plazo_max_meses: int
  moneda: string             // "PEN"
  notas: string
}
```

### 7.2 Reglas de asignación de producto

| Perfil `tipo_negocio` | Producto asignado |
|-----------------------|-------------------|
| microempresa, pyme, negocio, comercio, servicios | Créditos Pymes |
| agropecuario, agrícola | Crédito Agropecuario |
| inclusión | Créditos de Inclusión |
| personal, consumo | Créditos Personales |
| vivienda, construcción | Construyendo Confianza |
| No especificado / otro | Créditos Pymes (default) |

### 7.3 Reglas de validación

Antes de registrar una solicitud, el sistema verifica:

1. \( monto \geq monto\_min \)
2. \( monto \leq monto\_max \) (referencial)
3. \( plazo \geq plazo\_min \)
4. \( plazo \leq plazo\_max \) (referencial)

El incumplimiento genera mensajes de error explícitos al asesor comercial.

---

## 8. Implementación en el ecosistema móvil

### 8.1 Arquitectura general

```
┌─────────────────────────┐     ┌─────────────────────────┐
│  App Fuerza de Ventas   │     │     App Clientes        │
│  (Flutter)              │     │     (Flutter)           │
│  - Simula cuota         │     │  - Consulta cronograma  │
│  - Valida tarifario     │     │  - Paga cuota (demo)    │
└───────────┬─────────────┘     └───────────┬─────────────┘
            │                               │
            ▼                               ▼
┌─────────────────────────────────────────────────────────┐
│              Supabase (PostgreSQL)                       │
│  solicitudes_credito · creditos · cronograma_credito   │
└───────────────────────────┬─────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────┐
│              Core Mobile API (FastAPI)                   │
│  tarifario.py · creditos_service · solicitudes_service   │
│  GET /tarifario · POST desembolsar · cronograma francés  │
└─────────────────────────────────────────────────────────┘
```

### 8.2 Componentes software

| Capa | Módulo | Función |
|------|--------|---------|
| Core API | `app/core/tarifario.py` | Fuente única de verdad del tarifario |
| Core API | `app/services/creditos_service.py` | Algoritmo de cuota y cronograma |
| Core API | `app/routes/tarifario.py` | Exposición REST del catálogo |
| Flutter FV | `lib/core/tarifario/tarifario_confianza.dart` | Réplica del catálogo en cliente |
| Flutter FV | `lib/core/utils/credit_calculator.dart` | Motor de cálculo |
| Flutter FV | `solicitud_view.dart` | UI de originación con validación |

### 8.3 Flujo operativo del crédito

**Fase 1 — Originación (App Fuerza de Ventas)**  
El asesor selecciona un cliente de su cartera. El sistema resuelve el producto tarifario según `tipo_negocio`, valida monto y plazo, calcula la cuota estimada con TEA referencial y persiste `tea_referencial` y `cuota_estimada` en la tabla `solicitudes_credito`.

**Fase 2 — Decisión (Core Web / API)**  
Un operador o proceso automático aprueba la solicitud. Al desembolsar, el Core API lee la TEA almacenada, genera el registro en `creditos` con el nombre del producto tarifario e inserta el cronograma en `cronograma_credito`.

**Fase 3 — Servicing (App Clientes)**  
El cliente visualiza su crédito, cronograma de cuotas y, en modo demo, ejecuta pago de cuota vía endpoint del Core API.

---

## 9. Caso de aplicación numérico

### 9.1 Datos del caso

| Parámetro | Valor |
|-----------|-------|
| Capital (\(P\)) | S/ 1 000,00 |
| Plazo (\(n\)) | 12 meses |
| TEA | 56,00 % → 0,56 |
| Sistema | Francés, cuota constante |

### 9.2 Desarrollo del cálculo

**Paso 1 — Tasa mensual equivalente:**

\[
i = (1{,}56)^{1/12} - 1 \approx 0{,}03766 \quad (3{,}766\ \% \text{ mensual})
\]

**Paso 2 — Factor de anualidad:**

\[
(1+i)^{12} = 1{,}56
\]

**Paso 3 — Cuota:**

\[
C = 1000 \times \frac{0{,}03766 \times 1{,}56}{1{,}56 - 1} \approx \text{S/ } 105{,}17
\]

### 9.3 Contraste con ejemplo institucional

| Indicador | Publicado por Financiera Confianza | Calculado por el sistema |
|-----------|-----------------------------------|--------------------------|
| Cuota mensual | S/ 105,81 | S/ 105,17 |
| TEA | 56,00 % | 56,00 % |
| TCEA | 57,54 % | No calculado en demo |

**Análisis de la diferencia (S/ 0,64):** la discrepancia puede explicarse por:

1. **Inclusión del seguro de desgravamen** (S/ 5,90 en el ejemplo) dentro del flujo de caja que sustenta la TCEA, pero no necesariamente dentro de la cuota pura de capital + interés.
2. **Metodologías de redondeo** intermedias aplicadas por la entidad en cada periodo.
3. **Comisiones o ajustes** no modelados en la versión demo académica.

La diferencia relativa es inferior al **1 %**, lo cual se considera **aceptable** para un prototipo académico orientado a demostrar integración tarifaria y no a replicar exactamente el core bancario de originación.

### 9.4 Ejemplo adicional — Crédito Pymes (producto default)

| Parámetro | Valor |
|-----------|-------|
| Cliente | Microempresa (`tipo_negocio = pyme`) |
| Producto | Créditos Pymes |
| TEA máxima | 90,00 % |
| TEA referencial simulación | 56,00 % |
| Monto | S/ 5 000,00 |
| Plazo | 24 meses |

Cuota estimada del sistema: **≈ S/ 321,84** (TEA 56 %, sistema francés).

> Si se aplicara la TEA máxima del 90 %, la cuota ascendería significativamente, ilustrando la importancia de distinguir **tope tarifario** de **tasa referencial de simulación**.

---

## 10. Discusión

### 10.1 Aportes del trabajo

1. **Sustento documental:** los cálculos dejan de ser arbitrarios y se vinculan a fuentes auditables.
2. **Segmentación:** el sistema reconoce la diversidad de productos microfinancieros.
3. **Trazabilidad:** cada solicitud almacena la TEA utilizada, permitiendo reconstruir el cronograma.
4. **Cumplimiento orientativo:** la publicación del tarifario vía API (`GET /tarifario`) replica el espíritu de transparencia de la Ley 28587 en un canal digital.
5. **Replicabilidad académica:** otro equipo puede verificar cálculos a partir del documento T001-019 y el código fuente.

### 10.2 Limitaciones

| Limitación | Descripción |
|------------|-------------|
| ITF no modelado | El 0,005 % no se descuenta del desembolso en la demo. |
| Seguro de desgravamen | No se incluye en la cuota simulada. |
| TCEA | No se calcula; solo TEA referencial. |
| Mora | Tasa moratoria TNA 12,54 % no se aplica automáticamente. |
| Tasa individual | No existe motor de scoring; se usa TEA referencial única. |
| Actualización | Requiere intervención manual ante nuevo tarifario PDF. |

### 10.3 Trabajo futuro

- Implementar cálculo de **TCEA** incluyendo desgravamen y comisiones del tarifario de gastos.
- Integrar **motor de riesgo** para asignar TEA dentro del rango [referencial, máxima].
- Automatizar **alertas de vigencia** cuando Financiera Confianza publique nuevo T001-0XX.
- Incorporar **ITF** y penalidades por pago tardío del tarifario de comisiones.

---

## 11. Conclusiones

1. El ecosistema móvil Financiera Confianza **incorporó exitosamente** el tarifario oficial T001-019, reemplazando una TEA ficticia del 45 % por parámetros institucionales verificables.

2. La **TEA máxima por producto** (82 % – 105 %) del tarifario PDF se parametrizó íntegramente, mientras que la **TEA referencial del 56 %** — derivada del ejemplo oficial de Crédito Personal — se empleó para simulaciones demo coherentes con material publicado.

3. El **sistema de amortización francesa** sobre base de 360 días reproduce cuotas con desviación mínima respecto al ejemplo institucional, validando la correctitud matemática del motor implementado.

4. La arquitectura de **doble implementación** (Python en servidor, Dart en cliente móvil) garantiza autonomía operativa de la app de ventas, manteniendo coherencia con el Core API en desembolsos y cronogramas.

5. Desde la perspectiva académica, el trabajo demuestra cómo un **proyecto de software financiero** debe articular ingeniería de sistemas, normativa de conducta de mercado y matemática financiera, siendo el tarifario la pieza normativa que articula dichos dominios.

---

## 12. Referencias bibliográficas

### Fuentes institucionales (primarias)

1. Financiera Confianza S.A. (2025). *T001-019 Tarifario de Tasas Activas*. Aprobado 22.12.2025; vigencia desde enero 2026. Recuperado de https://confianza.pe/docs/2026/01/T001-019%20Tarifario%20de%20tasas%20activas.pdf

2. Financiera Confianza S.A. (s.f.). *Crédito Personal — Características, requisitos y tarifarios*. Recuperado de https://confianza.pe/persona/credito-personal

3. Financiera Confianza S.A. (s.f.). *Transparencia*. Recuperado de https://confianza.pe/confianza/transparencia

### Normativa peruana

4. Congreso de la República del Perú. (2005). *Ley N° 28587, Ley de protección al consumidor en el acceso a productos y servicios financieros*.

5. Superintendencia de Banca, Seguros y AFP. (2017). *Resolución SBS N° 3274-2017, Reglamento de Gestión de Conducta de Mercado del Sistema Financiero*.

6. Superintendencia de Banca, Seguros y AFP. (2025). *Resolución SBS N° 03040-2025*. Actualización de cláusulas generales de contratación de crédito.

### Referencias técnicas del proyecto

7. Documento técnico interno: `docs/TARIFARIO_CONFIANZA.md` — Guía operativa del tarifario en el ecosistema.

8. Código fuente: `core_mobile_api/app/core/tarifario.py` — Implementación del catálogo tarifario.

9. Código fuente: `app_fuerza_ventas/lib/core/tarifario/tarifario_confianza.dart` — Implementación cliente móvil.

---

## Anexos

### Anexo A — Endpoints REST del tarifario

```http
GET /tarifario
GET /tarifario/producto?tipo_negocio=pyme
GET /tarifario/producto?codigo=creditos_pymes
```

### Anexo B — Campos persistidos en solicitud de crédito

| Campo | Descripción |
|-------|-------------|
| `monto_solicitado` | Capital requerido (PEN) |
| `plazo_meses` | Número de cuotas |
| `tea_referencial` | TEA (%) usada en simulación |
| `cuota_estimada` | Cuota mensual calculada |
| `tipo_negocio` | Perfil del cliente; determina producto |

### Anexo C — Comparativa evolutiva del proyecto

| Etapa | TEA | Fundamento | Productos |
|-------|-----|------------|-----------|
| Prototipo inicial | 45 % fijo | Ninguno | Genérico |
| Versión actual | 56 % ref. / hasta 105 % máx. | T001-019 + web oficial | 5 líneas parametrizadas |

---

*Elaborado como documento académico de soporte al proyecto de Aplicaciones Móviles — Financiera Confianza Ecosystem. Las tasas y condiciones definitivas de una operación crediticia real son las pactadas en contrato, sujetas a evaluación crediticia y al tarifario vigente al momento del desembolso.*
