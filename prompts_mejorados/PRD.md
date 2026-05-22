# B.1 — Prompt mejorado: PRD ligero de FTGO

**Versión:** v0.5-improved (tuning corrida 2) · **Huecos TODO:** 4/4 · **D4:** Verification · **Métrica:** ICP + cobertura §A.4.

---

## Metadatos

| Campo | Valor |
| :--- | :--- |
| **ID** | PR-PRD-FTGO-001 |
| **Artefacto destino** | PRD |
| **Modelo recomendado** | Sonnet / Opus |
| **Temperatura** | 0.2 |
| **Versión** | v0.5-improved |
| **Brief canónico** | `docs/Brief.md` (Anexo A) |
| **Última corrida** | v1.0 PRD — ICP 100 % (ver Métrica → corrida 1) |

---

## Role

Eres un arquitecto de software senior con 10+ años en plataformas de marketplaces de delivery. Conoces a profundidad el caso **FTGO** del libro *Microservices Patterns* de Chris Richardson (Manning, 2019) y los patrones DDD estratégico (Bounded Context, Subdomain).

---

## Task

Genera un **PRD ligero** para FTGO en formato Markdown, partiendo del brief del **Anexo A** (`docs/Brief.md`).

El PRD debe tener entre **2 y 4 páginas equivalentes** y servir como entrada para un FSD y para **2 ADRs** arquitectónicos.

---

## Context

- **Documento fuente principal:** `docs/Brief.md` — contexto de negocio (§A.1), stakeholders (§A.2), capacidades (§A.3), NFRs (§A.4), 3 user stories semilla (§A.5).
- **Documento fuente secundario:** PDF *Microservices Patterns* (caps. 1–2 obligatorios).
- **Restricciones de dominio:** no inventar stakeholders, capacidades ni NFRs fuera del brief. Cada NFR del PRD debe rastrearse a §A.4 (`[Brief §A.4 <categoría>]`).
- **Restricción del laboratorio:** PRD ligero; cubre lo esencial para derivar FSD y ADRs.

### Stakeholders del brief (TODO 1 — completado)

| Rol | Necesidad principal | Origen |
| :--- | :--- | :--- |
| **Consumidor** | UX rápida, transparencia de estado, tracking en tiempo real | [Brief §A.2] |
| **Restaurante** | Tickets, carga de cocina, dashboard de pedidos | [Brief §A.2] |
| **Courier** | Asignaciones cercanas, rutas optimizadas, pago confiable | [Brief §A.2] |
| **Empleado FTGO** (back office) | Visibilidad, reportes, resolución de incidentes | [Brief §A.2] |
| **Equipo de arquitectura** | Calidad arquitectónica, trazabilidad, mantenibilidad | [Brief §A.2] |
| **Sistemas externos** | Stripe, Google Maps, SendGrid/Twilio — integración estable | [Brief §A.2] |

### Capacidades de negocio a cubrir (TODO 2 — completado)

| # | Capacidad | Responsabilidad (resumir en 1 párrafo en el PRD) |
| :---: | :--- | :--- |
| 1 | **Consumer Management** | Registro, perfiles, direcciones y preferencias | [Brief §A.3] |
| 2 | **Restaurant Management** | Restaurantes, menús, horarios, disponibilidad | [Brief §A.3] |
| 3 | **Order Taking** | Validación, total y confirmación del pedido | [Brief §A.3] |
| 4 | **Order Fulfillment / Kitchen** | Tickets al restaurante y estado de preparación | [Brief §A.3] |
| 5 | **Delivery** | Asignación de couriers, rutas, tracking en tiempo real | [Brief §A.3] |
| 6 | **Billing & Accounting** | Cobros, comisiones, payouts | [Brief §A.3] |
| 7 | **Notifications** | Email, SMS, push: confirmaciones y alertas | [Brief §A.3] |

> Las 7 capacidades **no** implican 7 microservicios obligatorios; el PRD solo las documenta como capacidades de negocio. La descomposición técnica queda para los ADRs.

### NFRs base del brief (referencia para la sección 4 del output)

El PRD debe incluir **exactamente 10 NFRs** (NFR-01…NFR-10), **uno por fila** de la tabla §A.4, con métrica explícita. No omitas categorías ni dupliques sin justificación.

| Categoría §A.4 | Métrica / restricción clave |
| :--- | :--- |
| Carga | Pico **5×** 12:00–14:00 y 19:00–22:00 |
| Latencia UX | **< 200 ms p95** acciones del consumidor |
| Disponibilidad | **99,9 %** toma de pedidos; **99,5 %** tracking |
| Tolerancia a fallos externos | Pedidos sin pasarela (retry); mapas degradables |
| Escalabilidad horizontal | Escala independiente (Scale Cube X/Y) |
| Consistencia de datos | Fuerte en aggregate pedido; eventual en reporting |
| Trazabilidad | Correlation ID + distributed tracing |
| Migración incremental | Strangler Fig **18–24 meses** |
| Tecnología | Java/Spring Boot en core |
| Cumplimiento | PCI-DSS vía Stripe; GDPR datos consumidor |

---

## Ajustes tras corrida 1 (objetivo corrida 2)

La corrida 1 alcanzó **ICP 100 %** con [`docs/PRD.md`](../docs/PRD.md) v1.0. Para la **corrida 2**, refuerza lo siguiente (sin inventar dominio):

| Objetivo métrica | Acción en el output |
| :--- | :--- |
| Mantener **S6 = 8/8** | Bloque **Verificación V1–V8 obligatorio** al final (no omitir). |
| Subir **cobertura §A.4 (S9)** | **10 NFRs** alineados 1:1 con la tabla de Context (no solo ≥ 5). |
| Reforzar **V8 / downstream** | En §5 Alcance: tabla **Capacidad → US semilla** (US-01…03) y mención de 2 ADRs. |
| Optimizar **S7** | Justificación de cada NFR: **máx. 1 línea**; evitar repetir el brief. |
| Formato estable | Metadatos al inicio; stakeholders con columna **Origen**; capacidades `### 3.1`…`### 3.7`. |

---

## Reasoning

Sigue estos pasos en orden:

1. Lee `docs/Brief.md` y usa las tablas de **Context** (no re-derives stakeholders ni capacidades).
2. Estructura el PRD con las 5 secciones de **Output**, metadatos y esqueleto v0.5.
3. Redacta **NFR-01…NFR-10** (una categoría §A.4 cada uno) con métrica + origen + justificación breve.
4. En **Alcance**, incluye tabla capacidad→US y exclusiones del laboratorio (Strangler Fig, monolito).
5. Ejecuta **Verification** V1–V8 y escribe el bloque de cierre obligatorio.
6. **NO** incluyas razonamiento interno fuera del bloque de verificación.

---

## Stop condition

Detente cuando se cumplan **todas** las condiciones:

- El PRD tiene las **5 secciones obligatorias** de **Output**.
- Cada NFR cita origen en el brief.
- **Criterios cuantitativos (TODO 3 — completado):**
  - **≥ 7** subsecciones de capacidades (una por capacidad §A.3).
  - **10** NFRs numerados (NFR-01…NFR-10), uno por categoría §A.4, con métrica y `[Brief §A.4 …]`.
  - Bloque **Verificación PRD: V1–V8** presente al final del documento.
  - Tabla **Capacidad → US** en la sección Alcance (mín. US-01, US-02, US-03).
  - **≥ 6** filas en la tabla/lista de stakeholders (incluye sistemas externos).
  - **100 %** de los NFRs del output con campo *Origen* explícito.
  - Longitud total **≤ 1 800 palabras** (~4 páginas equivalentes a 450 palabras/página).

No continues produciendo contenido más allá de estas condiciones.

---

## Output

**Formato:** Markdown.

### Secciones obligatorias del PRD

1. **Contexto y objetivos** (1–2 párrafos).
2. **Stakeholders** (lista con rol y necesidad principal).
3. **Capacidades de negocio** (las 7 del Cap. 2, cada una con 1 párrafo).
4. **Requisitos no funcionales** (**10** NFRs, uno por fila §A.4).
5. **Alcance** (incluye / excluye / coexistencia + **tabla capacidad → US**).

### Formato obligatorio del encabezado (corrida 2+)

```markdown
# PRD — FTGO (Food To Go)

| Campo | Valor |
| **Versión** | 1.x |
| **Fecha** | YYYY-MM-DD |
| **Origen** | [Brief Anexo A](Brief.md) · PR-PRD-FTGO-001 v0.5 |
| **Estado** | Borrador para FSD y ADRs |
```

### Esqueleto mínimo por sección (TODO 4 — v0.5)

Copia esta estructura en el output; sustituye el texto de ejemplo por redacción propia, sin inventar dominio.

```markdown
# PRD — FTGO (Food To Go)

## 1. Contexto y objetivos

FTGO es un marketplace de delivery en migración desde un monolito Java (WAR) hacia microservicios
mediante Strangler Fig ([Brief §A.1]). Este PRD define capacidades, NFRs y alcance para documentar
la arquitectura objetivo que alimentará el FSD y dos ADRs.

## 2. Stakeholders

| Rol | Necesidad principal | Origen |
| Consumidor | UX rápida, estado transparente, tracking | [Brief §A.2] |
| ... (6 filas del Context, columna Origen obligatoria) |

## 3. Capacidades de negocio

### 3.1 Consumer Management
Registro y perfiles de consumidores; direcciones y preferencias para checkout ([Brief §A.3]).

### 3.2 Restaurant Management
... (repetir hasta 3.7 Notifications)

## 4. Requisitos no funcionales

### NFR-01: Carga en horarios pico
- **Métrica:** soportar tráfico 5× en 12:00–14:00 y 19:00–22:00.
- **Origen:** [Brief §A.4 Carga].
- **Justificación:** almuerzo y cena concentran la demanda del consumidor.

### NFR-02: Latencia UX
- **Métrica:** < 200 ms p95 en acciones del consumidor en la app.
- **Origen:** [Brief §A.4 Latencia UX].
- **Justificación:** percepción de fluidez en checkout y consulta de menú.

### NFR-03: Disponibilidad toma de pedidos
- **Métrica:** 99,9 % mensual mínimo en el flujo de toma de pedidos.
- **Origen:** [Brief §A.4 Disponibilidad].
- **Justificación:** ingresos críticos dependen de completar pedidos.

(repetir hasta NFR-10 — una categoría §A.4 por NFR)

## 5. Alcance

**Incluye:** 7 capacidades; 10 NFRs; Strangler Fig 18–24 meses; base para FSD y 2 ADRs.

| Capacidad PRD | US / flujo brief |
| Order Taking | US-01 |
| Order Fulfillment / Kitchen | US-02 |
| Delivery | US-03 |
| ... (vincular las 7 capacidades donde aplique) |

**Excluye:** código, APIs detalladas, descomposición en microservicios (→ ADRs).
**Coexistencia:** monolito legacy ([Brief §A.4 Migración incremental]).

## Verificación del PRD (V1–V8)

Verificación PRD: V1–V8 → 8/8 ✅ | Pendientes: ninguna
```

---

## Invariants

- El PRD debe citar al brief en cada NFR.
- El PRD debe cubrir las **7 capacidades** de negocio del Cap. 2.
- El PRD no debe inventar stakeholders fuera del brief.
- El PRD no debe exceder **4 páginas equivalentes** (~1 800 palabras).

---

## Failure modes

| Código | Descripción |
| :--- | :--- |
| `E_MISSING_BRIEF` | No se proporcionó `docs/Brief.md` → abortar. |
| `E_INVENTED_DOMAIN` | Stakeholders o capacidades fuera del brief → rechazar. |
| `E_INCOMPLETE_NFR` | NFRs sin métrica o sin origen → reintentar. |
| `E_STOP_NOT_MET` | No cumple criterios cuantitativos de Stop condition → reintentar. |
| `E_MISSING_US_MAP` | Alcance sin tabla capacidad→US → reintentar. |
| `E_INCOMPLETE_A4` | Menos de 10 NFRs (falta categoría §A.4) → reintentar. |

---

## Anti-patterns (evitar en corrida 2)

| Anti-pattern | Impacto en métricas | Corrección |
| :--- | :--- | :--- |
| Omitir bloque **Verificación V1–V8** | Baja **S6** | Cerrar siempre con el bloque de 1 línea + tabla opcional |
| Solo 5 NFRs genéricos | Pierde **S9** (cobertura §A.4) | Documentar **NFR-01…10** según tabla Context |
| Stakeholders sin columna **Origen** | Baja trazabilidad percibida | Copiar formato tabla §2 del esqueleto v0.5 |
| Repetir párrafos del brief | Sube **S7** sin valor | Parafrasear; justificación NFR ≤ 1 línea |
| Inventar actor o capacidad | `E_INVENTED_DOMAIN` | Usar solo tablas Context |
| Alcance sin **US-01…03** | Falla **V8** | Tabla capacidad→US en §5 |

---

## Verification

Antes de dar por terminado el **PRD generado**, valida cada ítem (marca ✅/❌). Si hay ❌, corrige solo lo faltante sin expandir el alcance.

| # | Verificación | Criterio de éxito |
| :---: | :--- | :--- |
| V1 | Secciones obligatorias | Existen las 5 secciones del Output (contexto, stakeholders, capacidades, NFRs, alcance) |
| V2 | Stakeholders | ≥ 6 roles alineados a §A.2; ningún actor inventado |
| V3 | Capacidades | Exactamente **7** subsecciones o párrafos, una por capacidad §A.3 |
| V4 | NFRs | ≥ 5 NFRs con **métrica numérica** y `[Brief §A.4 …]` en cada uno |
| V5 | Trazabilidad | 100 % de NFRs con campo *Origen*; 0 capacidades fuera del brief |
| V6 | Alcance laboratorio | Menciona Strangler Fig, monolito legacy y qué queda **fuera** (código, APIs detalladas) |
| V7 | Tamaño | ≤ 1 800 palabras; tono ligero, sin repetir el brief completo |
| V8 | Preparación downstream | Tabla capacidad→US en alcance; FSD (US-01…03) y 2 ADRs derivables sin aclaraciones |
| V9 | Cobertura §A.4 | **10/10** categorías NFR presentes (S9) |

**Salida de verificación (obligatoria al final del PRD):**

```text
Verificación PRD: V1–V9 → [N/9] ✅ | Pendientes: [lista vacía o ítems]
```

---

## Huecos TODO — estado

| # | Ubicación | Estado |
| :---: | :--- | :--- |
| 1 | Context | ✅ Stakeholders §A.2 |
| 2 | Context | ✅ Capacidades §A.3 |
| 3 | Stop condition | ✅ Criterios numéricos |
| 4 | Output | ✅ Esqueleto Markdown completo |

---

## Changelog

Historial de evolución del prompt **PR-PRD-FTGO-001**. Cada entrada documenta *qué* cambió y *por qué*.

### v0.1-seed

| Qué | Por qué |
| :--- | :--- |
| Estructura base (Role, Task, Context, Reasoning, Stop, Output, Invariants, Failure modes) | Plantilla mínima del laboratorio para generar un PRD ligero FTGO. |
| **4 TODO** sin rellenar (stakeholders, capacidades, stop cuantitativo, esqueleto de salida) | Obligar al maestrante a mejorar el prompt antes de usarlo; sin datos del brief el modelo improvisa dominio. |
| Referencia genérica al Anexo A | El brief existía fuera del prompt; no estaba materializado en tablas ejecutables. |

### v0.2-improved (2026-05-22)

| Qué | Por qué |
| :--- | :--- |
| **TODO 1:** tabla de 6 stakeholders desde `docs/Brief.md` §A.2 | Evita que el modelo re-derive o invente actores; asegura trazabilidad `[Brief §A.2]`. |
| **TODO 2:** tabla de las 7 capacidades §A.3 + nota «no = 7 microservicios» | Fija el alcance funcional del PRD sin confundir capacidades de negocio con descomposición técnica (reservada a ADRs). |
| Tabla de **10 NFRs base** §A.4 como referencia | El output debe tener ≥ 5 NFRs con métrica; la tabla reduce omisiones (carga 5×, latencia 200 ms, Strangler Fig, etc.). |
| **TODO 3:** criterios cuantitativos de stop (7 capacidades, 5 NFRs, 6 stakeholders, 1 800 palabras) | Convierte «PRD ligero» en condiciones verificables; reduce outputs truncados o demasiado largos. |
| **TODO 4:** esqueleto Markdown completo del PRD de salida | Homogeneiza estructura entre corridas del mismo modelo y entre modelos. |
| Metadato `Brief canónico: docs/Brief.md` | Una sola fuente oficial del dominio (§A.6 del brief). |
| Código `E_STOP_NOT_MET` | Enlaza failure modes con los criterios numéricos del stop. |
| Tabla **Huecos TODO — estado** y **Métrica de 3 corridas** | Cumple requisitos D4 del laboratorio (seguimiento y evaluación repetible). |

### v0.3-improved (2026-05-22)

| Qué | Por qué |
| :--- | :--- |
| Sección **Verification** (checklist V1–V8) | Requisito D4: el artefacto PRD exige **trazabilidad y completitud**; una checklist post-generación detecta NFRs sin origen o stakeholders inventados antes de entregar. |
| Bloque opcional «Verificación PRD: V1–V8» en el output | Faculta auto-revisión explícita sin mezclar razonamiento interno con el contenido del PRD. |

### v0.4 — Métrica antes/después (2026-05-22)

| Qué | Por qué |
| :--- | :--- |
| Sección **Métrica de calidad (antes / después)** con ICP, 6 corridas y resumen Δ | Evidencia obligatoria del laboratorio: comparar semilla vs mejorado en completitud y reducción de iteraciones. |

---

## Métrica de calidad (antes / después)

Evalúa el **mismo indicador** con el prompt semilla (`docs/PROMPTS/PRD.md`) y con este prompt mejorado. Ejecuta **3 corridas por versión** (6 en total), mismas condiciones: `docs/Brief.md` adjunto, temperatura **0.2**, mismo modelo cuando sea posible.

### Indicador principal — PRD

| Métrica | Definición | Meta (mejorado) |
| :--- | :--- | :---: |
| **Índice de completitud del PRD (ICP)** | `(S1/5)×30% + (min(S3,10)/10)×25% + (S4%)×15% + (S5/6)×10% + (S6/9)×10% + (S9/10)×10%` — usar **V1–V9** (V9 = cobertura §A.4) | **≥ 95 %** |
| **Iteraciones hasta aceptar** | Reintentos hasta cumplir stop sin `E_*` | **≤ 2** (media); meta corrida 2: **1** |

### Indicadores secundarios

| Código | Qué mide | Cómo contar |
| :--- | :--- | :--- |
| S1 | Secciones obligatorias | 0–5 (contexto, stakeholders, capacidades, NFRs, alcance) |
| S2 | Capacidades §A.3 | 0–7 párrafos o subsecciones |
| S3 | NFRs con métrica numérica | Entero (objetivo ≥ 5) |
| S4 | Trazabilidad NFR | % NFRs con `[Brief §A.4 …]` |
| S5 | Stakeholders correctos | 0–6 (sin actores inventados) |
| S6 | Cumple V1–V9 (Verification) | 0–9 ítems ✅ |
| S7 | Palabras | Entero (límite 1 800; ideal 1 000–1 500) |
| S8 | Tabla capacidad→US en alcance | ✅ / ❌ |
| S9 | Categorías §A.4 cubiertas | 0–10 (NFR-01…10) |

### Registro — Antes (prompt semilla v0.1)

| Corrida | Fecha | Modelo | S1 | S2 | S3 | S4 (%) | S5 | S6 | S7 | **ICP (%)** | Iteraciones | Evidencia |
| :---: | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| 1 | | | | | | | | | | | | |
| 2 | | | | | | | | | | | | |
| 3 | | | | | | | | | | | | |

### Registro — Después (prompt mejorado v0.5)

| Corrida | Prompt | Fecha | Modelo | S1 | S2 | S3 | S4 | S5 | S6 | S7 | S8 | S9 | **ICP** | Iter. | Evidencia |
| :---: | :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| 1 | v0.4 | 2026-05-22 | Composer | 5 | 7 | 10 | 100 | 6 | 8/8 | 1 165 | — | 10 | **100** | 1 | [`docs/PRD.md`](../docs/PRD.md) v1.0 |
| 2 | v0.5 | 2026-05-22 | Composer | 5 | 7 | 10 | 100 | 6 | 9/9 | 1 315 | ✅ | 10 | **100** | 1 | [`docs/PRD.md`](../docs/PRD.md) v1.1 |
| 3 | v0.5 | | | | | | | | | | | | | | | |

> **Evidencia:** enlace a chat, captura, commit del `docs/PRD.md` o nota «corrida N — prompt X».

### Resumen comparativo (rellenar tras 6 corridas)

| Indicador | Antes (media 3 corridas) | Después (media 3 corridas) | Δ (después − antes) |
| :--- | :---: | :---: | :---: |
| ICP (%) | — | **100** *(media 2 corridas v0.4–v0.5)* | — |
| Iteraciones hasta aceptar | — | **1** | — |
| S1 — Secciones / 5 | — | **5** | — |
| S3 — NFRs con métrica | — | **10** | — |
| S4 — Trazabilidad NFR (%) | — | **100** | — |
| S6 — Checklist V / 9 | — | **8,5/9** *(c1: 8/8; c2: 9/9)* | +0,5 |
| S8 — Tabla capacidad→US | — | **✅** *(c2)* | +1 |
| S9 — Cobertura §A.4 / 10 | — | **10** | — |

> **Corrida 2 (v0.5):** ICP = 30+25+15+10+10+10 = **100 %**. Mejoras vs v1.0: **S8** tabla US, **V9**, verificación 9/9. Ver sección *Métrica de calidad — corrida 2* en `docs/PRD.md` v1.1.

**Interpretación v0.5:** mantiene ICP ≥ 95 % con fórmula ampliada; mejora **reproducibilidad** (formato fijo) y **V8** aunque S3 ya era 10.

### v0.5-improved — tuning corrida 2 (2026-05-22)

| Qué | Por qué |
| :--- | :--- |
| Sección **Ajustes tras corrida 1** | Enlaza métricas medidas con acciones concretas para la siguiente corrida. |
| **10 NFRs obligatorios** (1:1 §A.4) + **S9** | Corrida 1 ya tenía 10 NFRs; el prompt fija la regla para no regresar a ≥5. |
| Tabla **capacidad→US** + `E_MISSING_US_MAP` | Cierra gap **S8** y refuerza **V8** (FSD). |
| **Anti-patterns** PRD | Reduce regresiones en corridas 2–3. |
| **V9**, verificación **obligatoria**, ICP recalculado | S6 pasa a 9 ítems; métrica alineada a checklist ampliada. |
| Metadatos y stakeholders con **Origen** en esqueleto | Formato alineado al PRD v1.0 exitoso, más estricto. |

---

**Comando sugerido (corrida 2, prompt v0.5):**

```text
Aplica PR-PRD-FTGO-001 v0.5. Adjuntos: docs/Brief.md + este prompt.
Genera docs/PRD.md v1.1: 10 NFRs (NFR-01…10), tabla capacidad→US en alcance,
stakeholders con columna Origen, bloque obligatorio Verificación V1–V9.
Sin razonamiento previo. Temperatura 0.2.
```
