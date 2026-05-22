# B.2 — Prompt mejorado: FSD ligero de FTGO

**Versión:** v0.5-improved (tuning corrida 2) · **Huecos TODO:** 4/4 · **D4:** Examples · **Métrica:** CF ampliado.

---

## Metadatos

| Campo | Valor |
| :--- | :--- |
| **ID** | PR-FSD-FTGO-001 |
| **Artefacto destino** | FSD |
| **Modelo recomendado** | Sonnet |
| **Temperatura** | 0.2 |
| **Versión** | v0.5-improved |
| **Brief canónico** | `docs/Brief.md` (§A.5 user stories) |
| **Entrada previa** | `docs/PRD.md` **v1.1+** (tabla capacidad→US en §5) |
| **Última corrida** | FSD v1.0 — CF 100 % (ver Métrica → corrida 1) |

---

## Role

Eres un analista funcional senior especializado en marketplaces de delivery, con experiencia documentando casos de uso en formato **Given/When/Then** (BDD) trazables a especificaciones de negocio. Conoces el caso **FTGO** del libro de Richardson.

---

## Task

A partir del **PRD** ya generado y de las **3 user stories semilla** del brief (§A.5), produce un **FSD ligero** en Markdown con **≥ 5 casos de uso (UCs)** formalizados con bloques Given/When/Then explícitos.

---

## Context

- **Documento fuente primario:** PRD de FTGO (capacidades §A.3 reflejadas en el PRD).
- **Documento fuente secundario:** `docs/Brief.md` — US-01, US-02, US-03 (§A.5) y UCs derivables listados al final de §A.5.
- **Restricciones:** cada UC debe rastrearse a una US semilla, a una capacidad del PRD o al brief. Los UCs derivados citan `[Brief §A.5]` o el ejemplo de UC derivable.

### Catálogo obligatorio de UCs (TODO 1 — completado)

| ID | Título | Actor primario | Capacidad PRD | Origen |
| :--- | :--- | :--- | :--- | :--- |
| **UC-01** | Tomar pedido | Consumidor | Order Taking | [Brief §A.5 US-01] |
| **UC-02** | Aceptar o rechazar ticket de pedido | Restaurante | Order Fulfillment / Kitchen | [Brief §A.5 US-02] |
| **UC-03** | Asignar pedido a courier | Courier | Delivery | [Brief §A.5 US-03] |
| **UC-04** | Procesar pago en checkout | Consumidor | Order Taking + Billing & Accounting | [Brief §A.5 UCs derivables — pago en checkout] |
| **UC-05** | Consultar tracking en tiempo real | Consumidor | Delivery + Notifications | [Brief §A.5 UCs derivables — tracking; NFR latencia §A.4] |

**Opcionales** (solo si el alcance del FSD lo permite; no sustituyen a UC-01…UC-05):

| ID | Título | Origen |
| :--- | :--- | :--- |
| UC-06 | Reasignar entrega si courier rechaza | [Brief §A.5 — reasignación automática] |
| UC-07 | Cancelar pedido (consumidor) | [Brief §A.5 — cancelación por consumidor] |

> El FSD **debe** documentar al menos UC-01 a UC-05. No inventes UCs fuera del brief, del PRD ni de Richardson (Cap. 3 flujo de pedido).

### Criterios de aceptación semilla (referencia §A.5)

**US-01:** menú, carrito, confirmación con dirección y pago, validación restaurante/stock, número de pedido único.

**US-02:** notificación de tickets, aceptar con ETA o rechazar con motivo, actualización al consumidor, cancelación si rechaza.

**US-03:** disponibilidad del courier, ofertas cercanas, aceptar/rechazar en **30 s**, ruta optimizada al aceptar.

### Alineación PRD v1.1 (capacidad → US)

Usa la tabla **Trazabilidad capacidad → user story** del [PRD §5](docs/PRD.md). Cada UC debe citar **capacidad PRD (§3.x)** y **US** cuando aplique.

| UC | US | Capacidad PRD (§) |
| :--- | :--- | :--- |
| UC-01 | US-01 | 3.3 Order Taking |
| UC-02 | US-02 | 3.4 Order Fulfillment / Kitchen |
| UC-03 | US-03 | 3.5 Delivery |
| UC-04 | Derivado §A.5 | 3.3 + 3.6 |
| UC-05 | Derivado §A.5 | 3.5 + 3.7 |

---

## Ajustes tras corrida 1 (objetivo corrida 2)

[`docs/FSD.md`](../docs/FSD.md) v1.0 alcanzó **CF 100 %** con prompt v0.4. Para **corrida 2** (prompt v0.5):

| Gap corrida 1 | Acción v0.5 |
| :--- | :--- |
| PRD v1.0 sin tabla US centralizada | Entrada obligatoria: **PRD v1.1+**; intro cita tabla §5 del PRD |
| Sin **checklist de verificación** en el FSD | Bloque **Verificación F1–F8** obligatorio al final |
| Esqueleto solo detalla UC-01/02 | Exigir **UC-03…05 completos** (no «…» ni TBD) |
| Sin **diagrama de flujo** entre UCs | `flowchart` Mermaid UC-01→04→02→03→05 |
| Sin **métricas en el artefacto** | Sección **## Métrica de calidad — corrida N** en el output |
| Criterios §A.5 no explícitos por UC | Tabla **criterio brief cubierto** por UC (F9) |

---

## Reasoning

Sigue estos pasos en orden:

1. Lee **PRD v1.1+** (tabla capacidad→US) y el catálogo **UC-01…UC-05**; no improvises IDs.
2. Cubre US-01…03 y derivados UC-04/05 con los **7 campos completos** cada uno.
3. Aplica **regla de granularidad**; cada **FA** con ID (FA-01, FA-02…).
4. Incluye **diagrama de flujo** de UCs y tabla **criterios §A.5 cubiertos**.
5. Ejecuta checklist **F1–F8** y sección **Métrica** (si es corrida de laboratorio).
6. **NO** incluyas razonamiento fuera de verificación/métrica.

### Regla de granularidad (TODO 2 — completado)

| Criterio | **UC nuevo** | **Flujo alternativo** (mismo UC) |
| :--- | :--- | :--- |
| Actor primario | Cambia (Consumidor vs Restaurante vs Courier) | Mismo actor |
| Objetivo de negocio | Meta distinta (pagar vs ordenar vs asignar entrega) | Misma meta, variante (ej. aceptar **o** rechazar ticket) |
| Capacidad PRD | Capacidad distinta | Misma capacidad |
| Ejemplo FTGO | UC-04 (pago) separado de UC-01 (tomar pedido) | En UC-02: flujo principal = aceptar; alternativo = rechazar con motivo |

**Regla práctica:** si el escenario comparte actor, capacidad y objetivo con un UC existente, documéntalo en **Flujos alternativos** de ese UC; no crees UC-08 por cada variante.

---

## Stop condition

Detente cuando se cumplan **todas** las condiciones:

- **≥ 5 UCs** completos (UC-01…UC-05 como mínimo).
- Cada UC tiene **≥ 1 bloque Given/When/Then** con las tres cláusulas explícitas.
- **Criterios adicionales (TODO 3 — completado):**
  - Tabla resumen de UCs con **5 filas** y columnas: ID, título, actor, capacidad, origen.
  - Cada UC incluye los **7 campos** del esqueleto (ningún campo vacío o «TBD»).
  - **100 %** de los UCs con origen trazable (`US-NN`, `[Brief §A.5]`, o capítulo del libro citado).
  - Longitud total **≤ 2 500 palabras** (FSD ligero).
  - **Diagrama Mermaid** de flujo entre UC-01…05.
  - Bloque **Verificación F1–F8** y, en corridas evaluadas, **Métrica de calidad** con CF calculado.
  - **100 %** criterios §A.5 de la US vinculada reflejados en flujos o FA (métrica F9).

No continues produciendo contenido más allá de estas condiciones.

---

## Output

**Formato:** Markdown.

### Estructura del FSD

1. **Metadatos** (versión, fecha, PRD de entrada, prompt).
2. **Introducción** (1 párrafo): PRD v1.1+ y brief §A.5.
3. **Diagrama de flujo** de UCs (Mermaid).
4. **Tabla de UCs** (5 filas, columnas del catálogo).
5. **Detalle UC-01…UC-05** (7 campos + GWT cada uno).
6. **Tabla criterios §A.5 cubiertos** por UC.
7. **Verificación F1–F8** (obligatoria).
8. **Métrica de calidad — corrida N** (obligatoria en evaluación del laboratorio).

### Formato obligatorio del encabezado (corrida 2+)

```markdown
# FSD — FTGO (Food To Go)

| Campo | Valor |
| **Versión** | 1.x |
| **Fecha** | YYYY-MM-DD |
| **Entrada** | [PRD v1.1](PRD.md) · [Brief §A.5](Brief.md) |
| **Prompt** | PR-FSD-FTGO-001 v0.5 |
```

### Diagrama de flujo (obligatorio)

```mermaid
flowchart LR
    UC01[UC-01 Tomar pedido] --> UC04[UC-04 Pago]
    UC04 --> UC02[UC-02 Ticket cocina]
    UC02 --> UC03[UC-03 Asignar courier]
    UC03 --> UC05[UC-05 Tracking]
```

### Esqueleto formal por UC (TODO 4 — v0.5)

```markdown
### UC-01: Tomar pedido

| Campo | Valor |
| :--- | :--- |
| **Actor primario** | Consumidor |
| **Capacidad PRD** | Order Taking |
| **Origen** | [Brief §A.5 US-01] |

**Precondiciones:** el Consumidor está autenticado; eligió un restaurante con menú disponible.

**Flujo principal:**
1. El Consumidor consulta el menú del restaurante.
2. Agrega o quita ítems del carrito.
3. Confirma dirección de entrega y método de pago.
4. El sistema valida disponibilidad del restaurante y stock.
5. El sistema crea el pedido y devuelve un número único.

**Flujos alternativos:**
- **FA-01:** restaurante no disponible → mensaje de error, no se crea pedido.
- **FA-02:** ítem sin stock → el carrito se actualiza y se notifica al Consumidor.

**Postcondiciones:** pedido registrado con número único; estado inicial listo para pago o cocina según diseño.

**Given/When/Then:**
- **Given:** el Consumidor tiene un carrito con al menos 1 ítem y el restaurante está disponible.
- **When:** el Consumidor confirma el pedido con dirección y método de pago válidos.
- **Then:** el sistema crea el pedido y muestra un número de pedido único al Consumidor.

---

### UC-02: Aceptar o rechazar ticket de pedido

| Campo | Valor |
| :--- | :--- |
| **Actor primario** | Restaurante |
| **Capacidad PRD** | Order Fulfillment / Kitchen |
| **Origen** | [Brief §A.5 US-02] |

**Precondiciones:** existe un ticket de pedido en estado pendiente para el restaurante.

**Flujo principal:** (aceptar con tiempo estimado de preparación) …

**Flujos alternativos:** (rechazar con motivo → cancelación y notificación al Consumidor) …

**Postcondiciones:** ticket aceptado con ETA, o pedido cancelado si se rechaza.

**Given/When/Then:**
- **Given:** el Restaurante recibió un nuevo ticket en su dashboard.
- **When:** el Restaurante acepta el ticket e indica el tiempo estimado de preparación.
- **Then:** el estado del pedido se actualiza y el Consumidor es notificado.

### UC-03: Asignar pedido a courier

Completar 7 campos; **When/Then** deben mencionar timeout **30 s** y ruta optimizada ([Brief §A.5 US-03]).

### UC-04: Procesar pago en checkout

Incluir **FA-01 pasarela caída** alineado a [PRD NFR-04] / [Brief §A.4].

### UC-05: Consultar tracking en tiempo real

Vincular [PRD NFR-02] latencia; **FA-01** degradación tracking [NFR-03].
```

---

## Anti-patterns (evitar en corrida 2)

| Anti-pattern | Impacto | Corrección |
| :--- | :--- | :--- |
| UC-03…05 con «…» o TBD | Baja **F3** | Redactar 7 campos completos |
| Omitir timeout **30 s** en UC-03 | Baja **F9** | Citar criterio US-03 en flujo o GWT |
| Sin FA en UC-02 rechazo | Baja **F9** | FA-01 rechazo con motivo y cancelación |
| PRD v1.0 ignorado | Desalineación | Usar tabla §5 PRD v1.1 |
| UC inventado (UC-99) | `E_INVENTED_UC` | Solo catálogo UC-01…05 |
| GWT de una sola línea vacía | `E_MISSING_GWT` | Tres cláusulas Given/When/Then |

---

## Verification (FSD)

| # | Verificación | Criterio |
| :---: | :--- | :--- |
| F1 | Estructura | Metadatos + intro + diagrama + tabla + 5 UCs + verificación |
| F2 | Catálogo | UC-01…05 presentes con títulos del Context |
| F3 | 7 campos / UC | 35/35 campos sin TBD |
| F4 | GWT | 5/5 UCs con Given+When+Then |
| F5 | Tabla resumen | 5 filas ✅ |
| F6 | Trazabilidad | 100 % con US o [Brief §A.5] |
| F7 | Palabras | ≤ 2 500 |
| F8 | UCs inventados | 0 |
| F9 | Criterios §A.5 | Tabla: cada UC lista ítems del brief cubiertos |

**Salida obligatoria:**

```text
Verificación FSD: F1–F9 → [N/9] ✅ | Pendientes: [lista]
```

---

## Invariants

- El FSD debe tener **≥ 5 UCs**.
- Cada UC debe tener al menos **1 bloque Given/When/Then** completo.
- Cada UC debe mapearse a una **capacidad del PRD**.
- Los UCs derivados deben citar **`[Brief §A.5]`** o US explícita.

---

## Failure modes

| Código | Descripción |
| :--- | :--- |
| `E_MISSING_PRD` | No se proporcionó el PRD → abortar. |
| `E_INSUFFICIENT_UCS` | Hay menos de 5 UCs → reintentar. |
| `E_MISSING_GWT` | Hay UCs sin Given/When/Then → reintentar. |
| `E_INVENTED_UC` | UC no rastreable al PRD/brief/libro → rechazar. |
| `E_STOP_NOT_MET` | Faltan campos del esqueleto o tabla incompleta → reintentar. |
| `E_MISSING_FLOW` | Sin diagrama Mermaid de UCs → reintentar. |
| `E_WEAK_US03` | UC-03 sin timeout 30 s → reintentar. |
| `E_PRD_VERSION` | PRD anterior a v1.1 sin tabla capacidad→US → usar PRD actualizado. |

---

## Examples (input / output)

Referencia de **calidad esperada** para un UC derivado del brief. No copies literalmente; adapta al PRD entregado.

### Input (fragmento)

```text
[Brief §A.5 US-01]
Como Consumidor, quiero realizar un pedido desde el menú de un restaurante seleccionado…
Aceptación: carrito, confirmación con dirección y pago, número de pedido único.

[PRD — capacidad]
Order Taking: validación, total y confirmación del pedido.
```

### Output esperado (extracto UC-01)

```markdown
### UC-01: Tomar pedido

| Campo | Valor |
| **Actor primario** | Consumidor |
| **Capacidad PRD** | Order Taking |
| **Origen** | [Brief §A.5 US-01] |

**Given/When/Then:**
- **Given:** el Consumidor tiene un carrito con al menos 1 ítem y el restaurante está disponible.
- **When:** confirma el pedido con dirección de entrega y método de pago válidos.
- **Then:** el sistema crea el pedido y muestra un número de pedido único.
```

### Contra-ejemplo (output deficiente — no generar)

```markdown
### UC-99: Gestionar inventario global
Actor: Administrador inventario
Origen: (sin citar)
Given: el sistema existe.
When: el usuario hace algo.
Then: funciona.
```

> El contra-ejemplo viola `E_INVENTED_UC` (actor fuera del brief) y `E_MISSING_GWT` (GWT vacío).

---

## Huecos TODO — estado

| # | Ubicación | Estado |
| :---: | :--- | :--- |
| 1 | Context | ✅ Catálogo UC-01…UC-05 desde §A.5 |
| 2 | Reasoning | ✅ Regla UC nuevo vs flujo alternativo |
| 3 | Stop condition | ✅ 7 campos + tabla + ≤ 2 500 palabras |
| 4 | Output | ✅ Esqueleto formal UC-01 y UC-02 |

---

## Changelog

Historial de evolución del prompt **PR-FSD-FTGO-001**. Cada entrada documenta *qué* cambió y *por qué*.

### v0.1-seed

| Qué | Por qué |
| :--- | :--- |
| Prompt semilla en `docs/PROMPTS/FSD.md` | Define generación de FSD desde PRD + 3 user stories del brief. |
| **4 TODO** vacíos (lista de UCs, granularidad, stop extra, esqueleto de UC) | Sin catálogo de UCs el modelo crea IDs distintos en cada corrida y no alcanza ≥ 5 casos de uso trazables. |
| Requisito ≥ 5 UCs con Given/When/Then | Alineado a §A.5 y al flujo del laboratorio (US-01…03 + derivados). |

### v0.2-improved (2026-05-22)

| Qué | Por qué |
| :--- | :--- |
| **TODO 1:** catálogo **UC-01…UC-05** mapeado a US-01…03 y derivados §A.5 (pago checkout, tracking) | Cumple el brief: 3 US obligatorias + ≥ 2 UCs derivados justificados; impide `E_INVENTED_UC`. |
| Tabla de **criterios de aceptación** resumidos por US | El modelo no tiene que re-leer todo el brief para cada UC; reduce omisión de reglas (timeout 30 s del courier, número de pedido único). |
| **TODO 2:** regla UC nuevo vs **flujo alternativo** (tabla actor/objetivo/capacidad) | Evita inflar el FSD con un UC por cada variante (ej. rechazar ticket = alternativa de UC-02, no UC separado). |
| **TODO 3:** stop con 7 campos por UC, tabla resumen, ≤ 2 500 palabras | Evita UCs con «TBD» o GWT incompletos; mantiene FSD ligero según §A.6. |
| **TODO 4:** esqueleto formal UC-01 y UC-02 (tabla de campos + GWT) | Misma razón que en PRD: salida consistente entre modelos y corridas. |
| UCs opcionales UC-06/07 documentados | Permiten extensión sin sustituir el mínimo obligatorio del laboratorio. |
| Entrada previa explícita: PRD generado | El FSD debe mapear a **capacidades del PRD**, no solo al brief. |

### v0.3-improved (2026-05-22)

| Qué | Por qué |
| :--- | :--- |
| Sección **Examples (input/output)** | Requisito D4: el FSD es **BDD/GWT**; un par input (US-01 + capacidad PRD) → output (UC-01) ancla el nivel de detalle esperado. |
| **Contra-ejemplo** UC-99 inventado | Muestra explícitamente qué dispara `E_INVENTED_UC` y `E_MISSING_GWT`; refuerza la regla de no inventar dominio. |

### v0.4 — Métrica antes/después (2026-05-22)

| Qué | Por qué |
| :--- | :--- |
| **Métrica de calidad** con CF, registro 6 corridas y Δ | Mide cobertura UC-01…05 y GWT vs semilla; evidencia de menos reintentos. |

### v0.5-improved — tuning corrida 2 (2026-05-22)

| Qué | Por qué |
| :--- | :--- |
| Sección **Ajustes tras corrida 1** | Conecta CF 100 % de v1.0 con mejoras medibles (F9, diagrama, PRD v1.1). |
| Entrada **PRD v1.1+** y tabla capacidad→US | Alinea FSD con PRD corrida 2; evita drift entre artefactos. |
| **Verificación F1–F9** obligatoria | Sube reproducibilidad; F9 = cobertura criterios §A.5 por UC. |
| **Diagrama Mermaid** de flujo UC | Mejora V8 downstream y lectura del orden pedido→pago→cocina→entrega. |
| Esqueleto **UC-03…05** explícito + **Anti-patterns** | Corrida 1 tenía CF 100 % pero UC-03…05 menos guiados en el prompt. |
| **Métrica en artefacto FSD** | Paridad con PRD v1.1; evidencia de corrida en el documento generado. |
| Failure modes `E_MISSING_FLOW`, `E_WEAK_US03`, `E_PRD_VERSION` | Detección automática de regresiones típicas. |
| **CF v0.5** incluye componente F9 (10 %) | Incentiva cumplir criterios de aceptación del brief por UC. |

---

## Métrica de calidad (antes / después)

**3 corridas** con `docs/PROMPTS/FSD.md` (antes) y **3** con este prompt (después). Mismas entradas: `docs/Brief.md` + el mismo `docs/PRD.md`, temperatura **0.2**.

### Indicador principal — FSD

| Métrica | Definición | Meta (mejorado) |
| :--- | :--- | :---: |
| **Cobertura funcional (CF)** | `(F2/5)×30% + (F3%)×25% + (F4%)×25% + (F6%)×10% + (F9%)×10%` — F9 = % ítems §A.5 cubiertos en tabla criterios | **≥ 90 %** |
| **Iteraciones hasta aceptar** | Reintentos hasta cumplir stop sin `E_*` | **≤ 2**; meta corrida 2: **1** |

### Indicadores secundarios

| Código | Qué mide | Cómo contar |
| :--- | :--- | :--- |
| F1 | UCs en salida | 0–n (objetivo ≥ 5; catálogo fija UC-01…05) |
| F2 | UCs del catálogo cubiertos | 0–5 |
| F3 | Campos completos por UC | 0–7 × n UCs → % sobre 7×5 = 35 campos máx. si solo 5 UCs |
| F4 | GWT completos | n UCs con las 3 cláusulas / n UCs |
| F5 | Tabla resumen de UCs | ✅ / ❌ |
| F6 | Trazabilidad origen | % UCs con US-NN o [Brief §A.5] |
| F7 | Palabras | ≤ 2 500 ✅ / ❌ |
| F8 | UC inventado (E_INVENTED_UC) | 0 = ✅ |
| F9 | Criterios §A.5 cubiertos (tabla) | % ítems del brief reflejados (meta ≥ 95 %) |
| F10 | Diagrama flujo UC | ✅ / ❌ |
| F11 | Verificación F1–F9 en output | 0–9 ítems ✅ |

### Registro — Antes (prompt semilla v0.1)

| Corrida | Fecha | Modelo | F1 | F2 | F3 (%) | F4 (%) | F5 | F6 (%) | F7 | F8 | **CF (%)** | Iteraciones | Evidencia |
| :---: | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| 1 | | | | | | | | | | | | | |
| 2 | | | | | | | | | | | | | |
| 3 | | | | | | | | | | | | | |

### Registro — Después (prompt mejorado v0.5)

| Corrida | Prompt | Fecha | Modelo | F2 | F3 | F4 | F5 | F6 | F7 | F8 | F9 | F10 | F11 | **CF** | Iter. | Evidencia |
| :---: | :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| 1 | v0.4 | 2026-05-22 | Composer | 5 | 100 | 100 | ✅ | 100 | 1 302 | 0 | — | ❌ | 0/8 | **100*** | 1 | [`docs/FSD.md`](../docs/FSD.md) v1.0 |
| 2 | v0.5 | 2026-05-22 | Composer | 5 | 100 | 100 | ✅ | 100 | 1 689 | 0 | 100 | ✅ | 9/9 | **100** | 1 | [`docs/FSD.md`](../docs/FSD.md) v1.1 |
| 3 | v0.5 | | | | | | | | | | | | | | | |

\* CF v0.4 sin F9/F10/F11.

### Resumen comparativo

| Indicador | Antes (media) | Después (media) | Δ |
| :--- | :---: | :---: | :---: |
| CF (%) | — | **100** *(media c1–c2)* | — |
| Iteraciones | — | **1** | — |
| F9 — Criterios §A.5 | — | **100** *(c2)* | +100 % |
| F10 — Diagrama flujo | — | **✅** *(c2)* | +1 |
| F11 — Verificación F | — | **9/9** *(c2)* | +9 |

> **Corrida 2 (v0.5):** CF = 30+25+25+10+10 = **100 %**. Mejoras vs v1.0: **F9** 17/17 criterios §A.5, **F10** diagrama, **F11** 9/9, entrada **PRD v1.1**. Ver *Métrica de calidad — corrida 2* en `docs/FSD.md` v1.1.

**Interpretación v0.5:** mantiene CF ≥ 90 %; mejora trazabilidad al PRD y cumplimiento explícito de criterios §A.5.

---

**Comando sugerido (corrida 2, prompt v0.5):**

```text
Aplica PR-FSD-FTGO-001 v0.5. Adjuntos: docs/Brief.md, docs/PRD.md v1.1, este prompt.
Genera docs/FSD.md v1.1: UC-01…05 completos, diagrama Mermaid, tabla criterios §A.5,
Verificación F1–F9 y sección Métrica de calidad — corrida 2.
Sin razonamiento previo. Temperatura 0.2.
```
