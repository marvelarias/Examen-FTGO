# B.2 — Prompt mejorado: FSD ligero de FTGO

**Versión:** v0.4-improved · **Huecos TODO:** 4/4 · **Sección D4:** Examples · **Métrica:** CF (antes/después).

---

## Metadatos

| Campo | Valor |
| :--- | :--- |
| **ID** | PR-FSD-FTGO-001 |
| **Artefacto destino** | FSD |
| **Modelo recomendado** | Sonnet |
| **Temperatura** | 0.2 |
| **Versión** | v0.4-improved |
| **Brief canónico** | `docs/Brief.md` (§A.5 user stories) |
| **Entrada previa** | PRD generado (`docs/PRD.md` o salida del prompt B.1) |

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

---

## Reasoning

Sigue estos pasos en orden:

1. Usa el **catálogo UC-01…UC-05** del Context; no improvises IDs ni títulos.
2. Cubre las **3 US semilla** con UC-01, UC-02 y UC-03.
3. Documenta **UC-04 y UC-05** como derivados explícitos del brief (§A.5).
4. Aplica la **regla de granularidad** (TODO 2) al redactar flujos alternativos.
5. Completa los **7 campos** por UC según el esqueleto del Output.
6. Mapea cada UC a **una capacidad PRD** (columna del catálogo).

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

No continues produciendo contenido más allá de estas condiciones.

---

## Output

**Formato:** Markdown.

### Estructura del FSD

1. **Introducción** (1 párrafo): propósito, alcance y relación con el PRD.
2. **Tabla de UCs** (ID, título, actor primario, capacidad PRD, origen).
3. **Detalle de cada UC** con el esqueleto siguiente.

### Esqueleto formal por UC (TODO 4 — completado)

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

(Repetir estructura para UC-03…UC-05; UC-04 debe incluir tolerancia a pasarela caída si aplica [Brief §A.4].)
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

---

## Métrica de calidad (antes / después)

**3 corridas** con `docs/PROMPTS/FSD.md` (antes) y **3** con este prompt (después). Mismas entradas: `docs/Brief.md` + el mismo `docs/PRD.md`, temperatura **0.2**.

### Indicador principal — FSD

| Métrica | Definición | Meta (mejorado) |
| :--- | :--- | :---: |
| **Cobertura funcional (CF)** | `(UCs catálogo UC-01…05 documentados / 5) × 35% + (% UCs con 7 campos completos) × 25% + (% UCs con GWT Given+When+Then) × 25% + (% UCs con origen trazable) × 15%` | **≥ 90 %** |
| **Iteraciones hasta aceptar** | Reintentos hasta ≥ 5 UCs sin `E_INSUFFICIENT_UCS` ni `E_MISSING_GWT` | **≤ 2** (media) |

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

### Registro — Antes (prompt semilla v0.1)

| Corrida | Fecha | Modelo | F1 | F2 | F3 (%) | F4 (%) | F5 | F6 (%) | F7 | F8 | **CF (%)** | Iteraciones | Evidencia |
| :---: | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| 1 | | | | | | | | | | | | | |
| 2 | | | | | | | | | | | | | |
| 3 | | | | | | | | | | | | | |

### Registro — Después (prompt mejorado v0.4)

| Corrida | Fecha | Modelo | F1 | F2 | F3 (%) | F4 (%) | F5 | F6 (%) | F7 | F8 | **CF (%)** | Iteraciones | Evidencia |
| :---: | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| 1 | 2026-05-22 | Composer (Cursor) | 5 | 5 | 100 | 100 | ✅ | 100 | 1 302 | 0 | **100** | 1 | [`docs/FSD.md`](../docs/FSD.md) v1.0 |
| 2 | | | | | | | | | | | | | |
| 3 | | | | | | | | | | | | | |

### Resumen comparativo

| Indicador | Antes (media) | Después (media) | Δ |
| :--- | :---: | :---: | :---: |
| CF (%) | — | **100** *(solo corrida 1)* | — |
| Iteraciones | — | **1** *(solo corrida 1)* | — |
| F2 — UCs catálogo / 5 | — | **5** | — |
| F3 — Campos completos (%) | — | **100** | — |
| F4 — GWT completos (%) | — | **100** | — |
| F8 — UCs inventados (0 = bueno) | — | **0** | — |

> **Nota corrida 1 (después):** CF = 35 % (5/5 UCs catálogo) + 25 % (35/35 campos) + 25 % (5/5 GWT) + 15 % (trazabilidad) = **100 %**. F7 ≤ 2 500 palabras. Sin `E_INVENTED_UC`.

**Interpretación esperada:** tras mejorar el prompt, **F2** y **F4** suben (catálogo UC-01…05 + Examples); bajan iteraciones y apariciones de **F8**.

---

**Comando sugerido:** adjuntar `docs/Brief.md`, PRD generado y este prompt; temperatura **0.2**; pedir solo el FSD.
