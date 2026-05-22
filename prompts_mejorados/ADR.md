# B.3 — Prompt mejorado: ADR de FTGO

**Versión:** v0.4-improved · **Huecos TODO:** 4/4 · **Sección D4:** Anti-patterns · **Métrica:** ISA (antes/después).

---

## Metadatos

| Campo | Valor |
| :--- | :--- |
| **ID** | PR-ADR-FTGO-001 |
| **Artefacto destino** | ADR (Architecture Decision Record) |
| **Modelo recomendado** | Opus |
| **Temperatura** | 0.3 |
| **Versión** | v0.4-improved |
| **Brief canónico** | `docs/Brief.md` (§A.4, §A.6) |
| **Entrada previa** | PRD + FSD generados |

---

## Role

Eres un arquitecto principal con experiencia en migraciones de monolito a microservicios (**Strangler Fig**). Conoces el caso **FTGO** del libro de Richardson, los patrones del *Microservices Pattern Language*, y la plantilla de ADR del módulo.

Producción de ADRs honestos: opciones reales, trade-offs explícitos, decisión fundamentada y consecuencias **positivas y negativas**.

---

## Task

A partir del **PRD + FSD** y de `docs/Brief.md`, produce **1 ADR** en Markdown sobre una decisión arquitectónica clave.

La decisión se pasa como parámetro (ej. *estilo arquitectónico*, *IPC predominante*, *estrategia de descomposición*, *estrategia de datos*).

---

## Context

### Documentos fuente

- `docs/PRD.md` (NFRs y capacidades).
- `docs/FSD.md` (UCs y flujos).
- `docs/Brief.md` — restricciones §A.4 y migración §A.6.
- PDF *Microservices Patterns* (caps. según la decisión).

### Restricciones y NFRs que la decisión DEBE respetar (TODO 1 — completado)

| ID ref. | Restricción (Brief §A.4) | Implicación arquitectónica |
| :--- | :--- | :--- |
| R-01 | Carga pico **5×** (12:00–14:00, 19:00–22:00) | Escalado horizontal independiente por componente crítico |
| R-02 | Latencia UX **< 200 ms p95** (consumidor) | Evitar cadenas síncronas largas en checkout y consulta de menú |
| R-03 | Disponibilidad **99,9 %** toma de pedidos; **99,5 %** tracking | Aislamiento de fallos; degradación controlada del tracking |
| R-04 | Tolerancia a fallos externos (pasarela caída, mapas degradables) | Cola de retry / async para pagos; no bloquear toma de pedidos |
| R-05 | Escalabilidad horizontal (Scale Cube X/Y) | Evitar cuello de botella en monolito compartido |
| R-06 | Consistencia fuerte en aggregate **pedido**; eventual en reporting | Límites de transacción distribuida; eventos para lecturas analíticas |
| R-07 | Trazabilidad end-to-end (correlation ID, tracing) | Propagación de contexto en IPC y logs |
| R-08 | Migración **Strangler Fig** **18–24 meses** | Coexistencia con monolito legacy; prohibido big-bang |
| R-09 | **Java/Spring Boot** en el core | Stack alineado al equipo; satélites con libertad tecnológica |
| R-10 | PCI-DSS vía Stripe; GDPR consumidores | No persistir PAN; minimizar datos personales fuera de bounded contexts |

> Contexto de laboratorio [Brief §A.6]: el **monolito sigue vivo**; cada opción del ADR debe evaluar compatibilidad con extracción gradual, no reescritura total.

---

## Reasoning

Sigue estos pasos en orden:

1. Identifica el problema arquitectónico (2–3 líneas) vinculado al parámetro de decisión.
2. Filtra opciones con la tabla **R-01…R-10**; descarta las que violen Strangler Fig o NFRs críticos.
3. Evalúa opciones según **TODO 2** (mínimo y dimensiones).
4. Por opción: descripción, pros, contras, impacto en NFRs del PRD.
5. Decide, justifica y declara consecuencias positivas **y** negativas.
6. Define follow-ups (ADR posterior, POC).

### Evaluación de opciones (TODO 2 — completado)

- **Número mínimo:** evalúa **≥ 3 opciones** distintas y **realistas** (evita «no hacer nada» o «microservicios perfectos» como única alternativa seria).
- **Dimensiones obligatorias de comparación:**

| Dimensión | Pregunta guía |
| :--- | :--- |
| Ajuste a migración | ¿Permite Strangler Fig sin detener el monolito? [R-08] |
| Escalabilidad | ¿Soporta pico 5× en Order Taking / Delivery? [R-01, R-05] |
| Latencia y UX | ¿Mantiene < 200 ms p95 en flujos del consumidor? [R-02] |
| Resiliencia | ¿Cumple pedidos con pasarela caída y mapas degradables? [R-04] |
| Datos y consistencia | ¿Respeta aggregate pedido vs reporting eventual? [R-06] |
| Operación y equipo | ¿Complejidad acorde a Java/Spring y madurez del equipo? [R-09] |
| Trazabilidad | ¿Facilita correlation ID entre servicios? [R-07] |

---

## Stop condition

Detente cuando:

- El ADR tiene las **5 secciones obligatorias** del Output.
- Se evaluaron **≥ 3 opciones** con pros, contras e impacto en NFRs.
- **Criterios de calidad (TODO 3 — completado):**
  - Cada opción tiene **≥ 2 contras** explícitos (no genéricos).
  - La sección **Consecuencias** lista **≥ 2 negativas** y **≥ 2 positivas** de la decisión elegida.
  - La **Decisión** cita **≥ 1** referencia al libro (cap./patrón) o `[Brief §A.4]`.
  - Cada opción declara impacto en **≥ 2 NFRs** del PRD (por ID o categoría §A.4).
  - Ninguna opción es «de paja» (debe ser defendible en producción FTGO).

No continues produciendo contenido más allá de estas condiciones.

---

## Output

**Formato:** Markdown con secciones.

### Secciones obligatorias

1. **Título y status** (Proposed / Accepted / Superseded).
2. **Contexto** (problema y urgencia de decidir ahora).
3. **Opciones consideradas** (≥ 3, con descripción + pros + contras + impacto en NFRs).
4. **Decisión** (elección y justificación).
5. **Consecuencias** (positivas **y** negativas).

### Esqueleto de «Opciones consideradas» (TODO 4 — completado)

```markdown
## Opciones consideradas

### Opción 1: Microservicio por capacidad de negocio (7 servicios)

**Descripción:** cada capacidad del PRD (§A.3) se despliega como servicio independiente con BD propia.

**Pros:**
- Escala independiente en Order Taking y Delivery → [R-01], [R-05].
- Aislamiento de fallos entre cocina y billing → [R-03], [R-04].
- Alineado a *Decompose by Business Capability* (Richardson, Cap. 2).

**Contras:**
- Siete despliegues desde fases tempranas → operación y observabilidad costosas.
- Riesgo de *Distributed Monolith* si los UCs del FSD cruzan muchos servicios síncronos.
- Strangler Fig más lento si no hay façade clara sobre el monolito → [R-08].

**Impacto en NFRs:** NFR carga ✓; NFR latencia ⚠ (overhead de red en checkout); NFR migración ⚠.

---

### Opción 2: Monolito modular + extracción gradual (Strangler Fig)

**Descripción:** mantener monolito Java/Spring como núcleo; extraer primero Order Taking y Notifications detrás de rutas/API gateway.

**Pros:**
- Menor riesgo operativo en meses 1–12 → [R-08].
- Reutiliza skills Java/Spring → [R-09].
- Checkout puede mantener latencia baja con llamadas in-process → [R-02].

**Contras:**
- Escalado sigue siendo conflictivo hasta extraer módulos calientes → [R-01].
- Deuda de acoplamiento interno persiste.
- Extracción lenta si los límites de módulo no coinciden con capacidades.

**Impacto en NFRs:** NFR migración ✓; NFR carga ⚠; NFR escalabilidad horizontal ⚠.

---

### Opción 3: Subconjunto de servicios (3–4) agrupados por journey

**Descripción:** agrupar capacidades por recorrido: *Order* (Taking + Kitchen), *Delivery*, *Billing*, *Platform* (Consumer + Restaurant + Notifications).

**Pros:**
- Balance entre descomposición y operabilidad.
- Escala el journey de pedido sin 7 equipos de servicio.
- Compatible con eventos Kafka entre *Order* y *Delivery* → [R-04], [R-06].

**Contras:**
- Límites entre grupos pueden volverse ambiguos (ej. Notifications en varios journeys).
- Requiere disciplina de API entre grupos.
- POC necesario para validar consistencia del aggregate pedido → [R-06].

**Impacto en NFRs:** NFR carga ✓; NFR consistencia ⚠ (definir límites de transacción); NFR trazabilidad ✓ con bus de eventos.

## Decisión

(Elegir una opción, citar Richardson o [Brief §A.4], explicar por qué descartas las otras en 1 párrafo.)

## Consecuencias

**Positivas:** …
**Negativas:** … (obligatorio ≥ 2)
```

---

## Invariants

- **≥ 3 opciones** evaluadas.
- Consecuencias **positivas y negativas** en la decisión final.
- Cada opción: impacto en **≥ 1 NFR** del PRD (mínimo stop: **≥ 2**).
- Decisión con referencia al **libro** o **brief**.

---

## Failure modes

| Código | Descripción |
| :--- | :--- |
| `E_MISSING_INPUTS` | Faltan PRD/FSD/brief → abortar. |
| `E_INSUFFICIENT_OPTIONS` | < 3 opciones → reintentar. |
| `E_NO_TRADEOFFS` | Sin contras o sin consecuencias negativas → reintentar. |
| `E_UNREALISTIC_OPTION` | Opciones triviales o de paja → reintentar. |
| `E_STOP_NOT_MET` | No cumple criterios de calidad TODO 3 → reintentar. |

---

## Anti-patterns

**No** produzcas un ADR con estos patrones. Si los detectas en borrador, reescribe antes de entregar.

| Anti-pattern | Por qué falla en FTGO | Corrección |
| :--- | :--- | :--- |
| **Big-bang rewrite** | Viola Strangler Fig 18–24 meses [Brief §A.4, §A.6] | Opción con coexistencia monolito + fachada/rutas graduales |
| **Opción de paja** | «Monolito perfecto sin cambios» o «100 microservicios día 1» | Comparar 3 opciones defendibles en producción |
| **Solo consecuencias positivas** | Oculta deuda operativa y riesgo de *Distributed Monolith* | ≥ 2 negativas explícitas en la decisión elegida |
| **Decisión sin NFR** | No enlaza a carga 5×, latencia 200 ms o pasarela caída | Impacto en ≥ 2 NFRs del PRD por opción |
| **Ignorar pasarela caída** | FTGO debe tomar pedidos con Stripe down [R-04] | Incluir cola/retry o async en opción elegida |
| **7 servicios por defecto** | Capacidades §A.3 ≠ 7 microservicios obligatorios | Justificar agrupación con trade-offs y ADR de descomposición |
| **Sin referencia al libro** | No ancla la decisión a Richardson | Citar cap./patrón (ej. Decompose by Business Capability, Strangler) |
| **IPC único no cuestionado** | Solo REST o solo eventos sin analizar latencia vs resiliencia | Evaluar sync/async según R-02 y R-04 |

---

## Huecos TODO — estado

| # | Ubicación | Estado |
| :---: | :--- | :--- |
| 1 | Context | ✅ Restricciones R-01…R-10 desde §A.4 |
| 2 | Reasoning | ✅ ≥ 3 opciones y 7 dimensiones |
| 3 | Stop condition | ✅ Contras, consec. negativas, refs. libro/NFR |
| 4 | Output | ✅ Esqueleto de 3 opciones FTGO |

---

## Changelog

Historial de evolución del prompt **PR-ADR-FTGO-001**. Cada entrada documenta *qué* cambió y *por qué*.

### v0.1-seed

| Qué | Por qué |
| :--- | :--- |
| Prompt semilla en `docs/PROMPTS/ADR.md` | Genera 1 ADR honesto (opciones, trade-offs, consecuencias ±) a partir de PRD + FSD + brief. |
| **4 TODO** vacíos (restricciones, nº opciones, calidad de stop, esqueleto de opciones) | Sin restricciones §A.4 el ADR ignora Strangler Fig, pasarela caída o pico 5× — decisiones irreales para FTGO. |
| Decisión por **parámetro** (estilo, IPC, descomposición, datos) | Un ADR = una pregunta arquitectónica; evita documentos genéricos. |

### v0.2-improved (2026-05-22)

| Qué | Por qué |
| :--- | :--- |
| **TODO 1:** tabla **R-01…R-10** con implicación arquitectónica por NFR §A.4 | Cada opción del ADR debe evaluarse contra restricciones reales del caso (no solo opinión). |
| Nota de contexto §A.6 (monolito vivo, Strangler Fig) | Las opciones big-bang deben descartarse o marcarse como inviables en FTGO. |
| **TODO 2:** mínimo **≥ 3 opciones** + **7 dimensiones** de comparación | Evita `E_INSUFFICIENT_OPTIONS` y comparaciones superficiales (solo «microservicios sí/no»). |
| **TODO 3:** calidad mínima (≥ 2 contras/opción, ≥ 2 consecuencias negativas, ref. libro/brief, ≥ 2 NFRs impactados) | Combate ADRs propaganda; alinea con el Role («ADRs honestos»). |
| **TODO 4:** esqueleto de 3 opciones FTGO (7 servicios, monolito modular, 3–4 por journey) | Opciones defendibles y comparables; base para ADR de descomposición o IPC del laboratorio. |
| `E_STOP_NOT_MET` | Reintento cuando el ADR cumple forma pero no profundidad de trade-offs. |

### v0.3-improved (2026-05-22)

| Qué | Por qué |
| :--- | :--- |
| Sección **Anti-patterns** (8 patrones FTGO) | Requisito D4: en ADRs el fallo típico es **sesgo hacia la decisión deseada** (big-bang, opción de paja, solo pros); listar anti-patrones reduce `E_NO_TRADEOFFS` y `E_UNREALISTIC_OPTION`. |
| Tabla anti-pattern → corrección | Convierte advertencias en acciones concretas al reescribir el borrador. |

### v0.4 — Métrica antes/después (2026-05-22)

| Qué | Por qué |
| :--- | :--- |
| **Métrica de calidad** con ISA y conteo de anti-patterns (A6) | Cuantifica trade-offs reales y reducción de ADRs débiles frente a la semilla. |

---

## Métrica de calidad (antes / después)

**3 corridas** por versión del prompt (semilla vs mejorado). Mismo **parámetro de decisión** en las 6 corridas (ej. estrategia de descomposición), mismos insumos (Brief + PRD + FSD), temperatura **0.3**.

### Indicador principal — ADR

| Métrica | Definición | Meta (mejorado) |
| :--- | :--- | :---: |
| **Índice de solidez del ADR (ISA)** | `(opciones ≥ 3 / 3) × 25% + (opciones con ≥ 2 contras / opciones) × 25% + (consecuencias negativas ≥ 2 / 2) × 20% + (decisión con ref. libro o brief / 1) × 15% + (opciones con impacto ≥ 2 NFRs / opciones) × 15%` | **≥ 90 %** |
| **Iteraciones hasta aceptar** | Reintentos hasta cumplir stop sin `E_NO_TRADEOFFS` ni `E_UNREALISTIC_OPTION` | **≤ 2** (media) |

### Indicadores secundarios

| Código | Qué mide | Cómo contar |
| :--- | :--- | :--- |
| A1 | Secciones ADR (título, contexto, opciones, decisión, consecuencias) | 0–5 |
| A2 | Opciones evaluadas | Entero |
| A3 | Contras por opción (mín. 2) | % opciones que cumplen |
| A4 | Consecuencias negativas en decisión | 0–n (≥ 2) |
| A5 | Referencia Richardson o [Brief] en decisión | ✅ / ❌ |
| A6 | Anti-pattern detectado en borrador | Conteo (big-bang, paja, solo pros…) — ideal **0** tras corrección |
| A7 | Restricciones R-01…R-10 citadas en opciones | 0–10 |
| A8 | Cumple stop TODO 3 | ✅ / ❌ |

### Registro — Antes (prompt semilla v0.1)

| Corrida | Fecha | Modelo | Tema decisión | A1 | A2 | A3 (%) | A4 | A5 | A6 | A7 | **ISA (%)** | Iteraciones | Evidencia |
| :---: | :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| 1 | | | | | | | | | | | | | |
| 2 | | | | | | | | | | | | | |
| 3 | | | | | | | | | | | | | |

### Registro — Después (prompt mejorado v0.3)

| Corrida | Fecha | Modelo | Tema decisión | A1 | A2 | A3 (%) | A4 | A5 | A6 | A7 | **ISA (%)** | Iteraciones | Evidencia |
| :---: | :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| 1 | | | | | | | | | | | | | |
| 2 | | | | | | | | | | | | | |
| 3 | | | | | | | | | | | | | |

### Resumen comparativo

| Indicador | Antes (media) | Después (media) | Δ |
| :--- | :---: | :---: | :---: |
| ISA (%) | | | |
| Iteraciones | | | |
| A2 — Opciones | | | |
| A3 — % opciones con ≥ 2 contras | | | |
| A4 — Consec. negativas | | | |
| A6 — Anti-patterns en borrador | | | |

**Interpretación esperada:** **ISA** y **A3** suben con tabla R-01…R-10 y Anti-patterns; **A6** y **iteraciones** bajan.

---

**Comando sugerido:** adjuntar `docs/Brief.md`, PRD, FSD y parámetro de decisión; temperatura **0.3**.
