# B.1 — Prompt mejorado: PRD ligero de FTGO

**Versión:** v0.4-improved · **Huecos TODO:** 4/4 · **Sección D4:** Verification · **Métrica:** ICP (antes/después).

---

## Metadatos

| Campo | Valor |
| :--- | :--- |
| **ID** | PR-PRD-FTGO-001 |
| **Artefacto destino** | PRD |
| **Modelo recomendado** | Sonnet / Opus |
| **Temperatura** | 0.2 |
| **Versión** | v0.4-improved |
| **Brief canónico** | `docs/Brief.md` (Anexo A) |

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

Usa estas categorías de §A.4; el PRD debe incluir **≥ 5** con métrica explícita (puedes agrupar o numerar NFR-01…NFR-10):

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

## Reasoning

Sigue estos pasos en orden:

1. Lee `docs/Brief.md` y usa las tablas de **Context** (no re-derives stakeholders ni capacidades).
2. Estructura el PRD con las 5 secciones de **Output** y el esqueleto de **TODO 4**.
3. Asegura trazabilidad: cada NFR con `[Brief §A.4 <categoría>]`.
4. Declara alcance: migración Strangler Fig, monolito legacy coexistiendo, qué queda fuera del laboratorio.
5. **NO** incluyas razonamiento interno en el output final.

---

## Stop condition

Detente cuando se cumplan **todas** las condiciones:

- El PRD tiene las **5 secciones obligatorias** de **Output**.
- Cada NFR cita origen en el brief.
- **Criterios cuantitativos (TODO 3 — completado):**
  - **≥ 7** subsecciones de capacidades (una por capacidad §A.3).
  - **≥ 5** NFRs numerados, cada uno con **métrica numérica o porcentaje** y `[Brief §A.4 …]`.
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
4. **Requisitos no funcionales** (≥ 5, cada uno con métrica y origen `[Brief §A.4]`).
5. **Alcance** (qué entra, qué queda fuera).

### Esqueleto mínimo por sección (TODO 4 — completado)

Copia esta estructura en el output; sustituye el texto de ejemplo por redacción propia, sin inventar dominio.

```markdown
# PRD — FTGO (Food To Go)

## 1. Contexto y objetivos

FTGO es un marketplace de delivery en migración desde un monolito Java (WAR) hacia microservicios
mediante Strangler Fig ([Brief §A.1]). Este PRD define capacidades, NFRs y alcance para documentar
la arquitectura objetivo que alimentará el FSD y dos ADRs.

## 2. Stakeholders

| Rol | Necesidad principal |
| Consumidor | UX rápida, estado transparente, tracking ([Brief §A.2]) |
| ... (completar las 6 filas del Context) |

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

(... ≥ 5 NFRs en total; cubrir también tolerancia a fallos, migración, trazabilidad, etc.)

## 5. Alcance

**Incluye:** documentación objetivo de las 7 capacidades; NFRs §A.4; contexto Strangler Fig 18–24 meses.
**Excluye:** implementación de código; diseño detallado de APIs; descomposición final en microservicios (→ ADRs).
**Coexistencia:** monolito legacy operativo durante la migración ([Brief §A.4 Migración incremental]).
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
| V8 | Preparación downstream | Un lector puede derivar FSD (US §A.5) y 2 ADRs sin pedir aclaraciones de dominio |

**Salida de verificación (opcional, 3 líneas máx. al final del PRD):**

```text
Verificación PRD: V1–V8 → [N/8] ✅ | Pendientes: [lista vacía o ítems]
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
| **Índice de completitud del PRD (ICP)** | `(secciones obligatorias presentes / 5) × 40% + (NFRs con métrica numérica / 5) × 30% + (NFRs con origen [Brief §A.4] / total NFRs) × 20% + (stakeholders §A.2 sin inventar / 6) × 10%` | **≥ 95 %** |
| **Iteraciones hasta aceptar** | Nº de mensajes o reintentos hasta cumplir stop condition sin `E_*` | **≤ 2** (media) |

### Indicadores secundarios

| Código | Qué mide | Cómo contar |
| :--- | :--- | :--- |
| S1 | Secciones obligatorias | 0–5 (contexto, stakeholders, capacidades, NFRs, alcance) |
| S2 | Capacidades §A.3 | 0–7 párrafos o subsecciones |
| S3 | NFRs con métrica numérica | Entero (objetivo ≥ 5) |
| S4 | Trazabilidad NFR | % NFRs con `[Brief §A.4 …]` |
| S5 | Stakeholders correctos | 0–6 (sin actores inventados) |
| S6 | Cumple V1–V8 (Verification) | 0–8 ítems ✅ |
| S7 | Palabras | Entero (límite 1 800) |

### Registro — Antes (prompt semilla v0.1)

| Corrida | Fecha | Modelo | S1 | S2 | S3 | S4 (%) | S5 | S6 | S7 | **ICP (%)** | Iteraciones | Evidencia |
| :---: | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| 1 | | | | | | | | | | | | |
| 2 | | | | | | | | | | | | |
| 3 | | | | | | | | | | | | |

### Registro — Después (prompt mejorado v0.4)

| Corrida | Fecha | Modelo | S1 | S2 | S3 | S4 (%) | S5 | S6 | S7 | **ICP (%)** | Iteraciones | Evidencia |
| :---: | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| 1 | 2026-05-22 | Composer (Cursor) | 5 | 7 | 10 | 100 | 6 | 8 | 1 165 | **100** | 1 | [`docs/PRD.md`](../docs/PRD.md) v1.0 |
| 2 | | | | | | | | | | | | |
| 3 | | | | | | | | | | | | |

> **Evidencia:** enlace a chat, captura, commit del `docs/PRD.md` o nota «corrida N — prompt X».

### Resumen comparativo (rellenar tras 6 corridas)

| Indicador | Antes (media 3 corridas) | Después (media 3 corridas) | Δ (después − antes) |
| :--- | :---: | :---: | :---: |
| ICP (%) | — | **100** *(solo corrida 1)* | — |
| Iteraciones hasta aceptar | — | **1** *(solo corrida 1)* | — |
| S1 — Secciones / 5 | — | **5** | — |
| S3 — NFRs con métrica | — | **10** | — |
| S4 — Trazabilidad NFR (%) | — | **100** | — |
| S6 — Checklist V1–V8 / 8 | — | **8** | — |

> **Nota corrida 1 (después):** ICP = 40 % (5/5 secciones) + 30 % (10/5 NFRs, tope 30 %) + 20 % (trazabilidad) + 10 % (6/6 stakeholders) = **100 %**. Sin reintentos (`E_*`).

**Interpretación esperada:** el prompt mejorado sube **ICP** y **S4** (tablas del brief embebidas) y baja **iteraciones** (stop cuantitativo + Verification).

---

**Comando sugerido:** adjuntar `docs/Brief.md` y este prompt; temperatura **0.2**; pedir solo el PRD sin explicaciones previas.
