# PRD — FTGO (Food To Go)

| Campo | Valor |
| :--- | :--- |
| **Versión** | 1.1 |
| **Fecha** | 2026-05-22 |
| **Origen** | [Brief Anexo A](Brief.md) · Prompt PR-PRD-FTGO-001 v0.5 |
| **Referencia previa** | PRD v1.0 (corrida 1, prompt v0.4) |
| **Estado** | Borrador para FSD y ADRs |

---

## 1. Contexto y objetivos

**FTGO** conecta consumidores con restaurantes para delivery de comida. Opera como **monolito Java (WAR)** con *infierno monolítico*: builds lentos, escalado conflictivo, pocos aislamientos de fallo y equipos bloqueados ([Brief §A.1]).

La migración a **microservicios** usa **Strangler Fig** en **18–24 meses**, con el monolito en producción durante la transición.

**Objetivo:** documentar stakeholders, **7 capacidades** (Cap. 2 Richardson), **10 NFRs** (§A.4) y alcance para el **FSD** (US-01…03) y **2 ADRs**. Sin implementación ni APIs detalladas.

---

## 2. Stakeholders

| Rol | Necesidad principal | Origen |
| :--- | :--- | :--- |
| **Consumidor** | UX rápida, estado transparente, tracking en tiempo real | [Brief §A.2] |
| **Restaurante** | Tickets, carga de cocina, dashboard de pedidos | [Brief §A.2] |
| **Courier** | Asignaciones cercanas, rutas optimizadas, pago confiable | [Brief §A.2] |
| **Empleado FTGO** (back office) | Visibilidad, reportes, resolución de incidentes | [Brief §A.2] |
| **Equipo de arquitectura** | Calidad arquitectónica, trazabilidad, mantenibilidad | [Brief §A.2] |
| **Sistemas externos** | Stripe, Google Maps, SendGrid/Twilio — integración estable | [Brief §A.2] |

---

## 3. Capacidades de negocio

Las siete capacidades son candidatas a bounded contexts; la descomposición técnica va en ADRs ([Brief §A.3]).

### 3.1 Consumer Management

Registro, perfiles, direcciones y preferencias para checkout y entrega ([Brief §A.3]).

### 3.2 Restaurant Management

Restaurantes, menús, horarios y disponibilidad para validar pedidos ([Brief §A.3]).

### 3.3 Order Taking

Menú, carrito, validación, total y confirmación con número de pedido único ([Brief §A.3]; **US-01**).

### 3.4 Order Fulfillment / Kitchen

Tickets a cocina, aceptación/rechazo, ETA de preparación, notificación al consumidor ([Brief §A.3]; **US-02**).

### 3.5 Delivery

Disponibilidad del courier, asignación (timeout **30 s**), rutas y tracking ([Brief §A.3]; **US-03**).

### 3.6 Billing & Accounting

Cobros vía pasarela, comisiones y payouts a restaurantes y couriers ([Brief §A.3]; checkout).

### 3.7 Notifications

Email, SMS y push para confirmaciones, estados y alertas ([Brief §A.3]).

---

## 4. Requisitos no funcionales

### NFR-01: Carga

- **Métrica:** tráfico **5×** en **12:00–14:00** y **19:00–22:00** (hora local).
- **Origen:** [Brief §A.4 Carga].
- **Justificación:** picos de almuerzo y cena en Order Taking y Delivery.

### NFR-02: Latencia UX

- **Métrica:** **< 200 ms p95** en acciones del consumidor en la app.
- **Origen:** [Brief §A.4 Latencia UX].
- **Justificación:** fluidez en menú, carrito y confirmación.

### NFR-03: Disponibilidad

- **Métrica:** **99,9 %** mensual en toma de pedidos; **99,5 %** en tracking.
- **Origen:** [Brief §A.4 Disponibilidad].
- **Justificación:** ingreso crítico en el flujo de pedido.

### NFR-04: Tolerancia a fallos externos

- **Métrica:** pedidos aceptados con pasarela caída (cola retry); mapas degradables sin bloquear pedido.
- **Origen:** [Brief §A.4 Tolerancia a fallos externos].
- **Justificación:** resiliencia frente a Stripe y Google Maps.

### NFR-05: Escalabilidad horizontal

- **Métrica:** escalado **independiente** por componente (Scale Cube ejes X e Y).
- **Origen:** [Brief §A.4 Escalabilidad horizontal].
- **Justificación:** sustituir límites del monolito en pico.

### NFR-06: Consistencia de datos

- **Métrica:** consistencia **fuerte** en aggregate **pedido**; **eventual** entre servicios para reporting.
- **Origen:** [Brief §A.4 Consistencia de datos].
- **Justificación:** coherencia operativa vs analítica.

### NFR-07: Trazabilidad

- **Métrica:** **correlation ID** y **distributed tracing** en flujos críticos del consumidor.
- **Origen:** [Brief §A.4 Trazabilidad].
- **Justificación:** diagnóstico en cadena monolito–microservicios.

### NFR-08: Migración incremental

- **Métrica:** **Strangler Fig** **18–24 meses**; sin big-bang.
- **Origen:** [Brief §A.4 Migración incremental].
- **Justificación:** continuidad del negocio con monolito vivo.

### NFR-09: Tecnología

- **Métrica:** **Java / Spring Boot** en el core; libertad en satélites documentada.
- **Origen:** [Brief §A.4 Tecnología].
- **Justificación:** aprovechar skills y código existente.

### NFR-10: Cumplimiento

- **Métrica:** **PCI-DSS** vía Stripe (sin PAN en FTGO); **GDPR** y normativa local en datos de consumidor.
- **Origen:** [Brief §A.4 Cumplimiento].
- **Justificación:** minimizar superficie regulatoria en plataforma.

---

## 5. Alcance

### Incluye

- **7 capacidades** §A.3 y **10 NFRs** §A.4.
- Migración **Strangler Fig** con **monolito legacy**.
- Base para **FSD** y **2 ADRs** (descomposición, IPC o datos).

### Trazabilidad capacidad → user story (FSD)

| Capacidad PRD | US / flujo brief | Notas |
| :--- | :--- | :--- |
| Consumer Management | Soporte US-01 (perfil, dirección) | Contexto del consumidor |
| Restaurant Management | Soporte US-01 (menú, disponibilidad) | |
| **Order Taking** | **US-01** | Toma de pedido |
| **Order Fulfillment / Kitchen** | **US-02** | Tickets cocina |
| **Delivery** | **US-03** | Asignación courier |
| Billing & Accounting | Derivado §A.5 (pago checkout) | Tras US-01 |
| Notifications | Derivado §A.5 (tracking, alertas) | US-02, US-03, consumidor |

### Excluye

- Código, esquemas BD detallados, OpenAPI.
- Mapa 1:1 capacidad→microservicio (→ ADRs).
- Todos los UCs derivables del brief (solo mínimo FSD).

### Coexistencia

**Monolito legacy** activo durante extracción por fachadas/rutas ([Brief §A.4], [Brief §A.6]).

---

## Verificación del PRD (V1–V9)

```text
Verificación PRD: V1–V9 → 9/9 ✅ | Pendientes: ninguna
```

| # | Resultado |
| :---: | :--- |
| V1 | ✅ 5 secciones |
| V2 | ✅ 6 stakeholders §A.2 |
| V3 | ✅ 7 capacidades `### 3.1`…`3.7` |
| V4 | ✅ 10 NFRs con métrica |
| V5 | ✅ 100 % NFRs con [Brief §A.4] |
| V6 | ✅ Alcance Strangler + exclusiones |
| V7 | ✅ 1 315 palabras (≤ 1 800) |
| V8 | ✅ Tabla capacidad→US + FSD/ADRs |
| V9 | ✅ 10/10 categorías §A.4 |

---

## Métrica de calidad — corrida 2

Generación con **PR-PRD-FTGO-001 v0.5** (referencia: PRD v1.0 / corrida 1 v0.4).

| Campo | Valor |
| :--- | :--- |
| **Corrida** | 2 (después, prompt v0.5) |
| **Fecha** | 2026-05-22 |
| **Modelo** | Composer (Cursor) |
| **Iteraciones** | 1 |
| **Artefacto** | PRD v1.1 |

### Indicadores secundarios

| Código | Valor | Meta |
| :--- | :---: | :---: |
| S1 — Secciones / 5 | 5 | 5 |
| S2 — Capacidades | 7 | 7 |
| S3 — NFRs con métrica | 10 | ≥ 5 |
| S4 — Trazabilidad NFR (%) | 100 | 100 |
| S5 — Stakeholders | 6 | 6 |
| S6 — Verification V1–V9 | 9/9 | 9 |
| S7 — Palabras | 1 315 | ≤ 1 800 |
| S8 — Tabla capacidad→US | ✅ | ✅ |
| S9 — Cobertura §A.4 / 10 | 10 | 10 |

### Índice de completitud (ICP)

| Componente | Cálculo | Puntos |
| :--- | :--- | :---: |
| Secciones | (5/5) × 30 % | 30 % |
| NFRs | (10/10) × 25 % | 25 % |
| Trazabilidad | 100 % × 15 % | 15 % |
| Stakeholders | (6/6) × 10 % | 10 % |
| Verification | (9/9) × 10 % | 10 % |
| Cobertura §A.4 | (10/10) × 10 % | 10 % |
| **ICP total** | | **100 %** |

### Mejora vs corrida 1 (v1.0)

| Indicador | Corrida 1 (v0.4) | Corrida 2 (v0.5) | Δ |
| :--- | :---: | :---: | :---: |
| ICP (fórmula) | 100 % * | **100 %** | 0 |
| S6 — Verification | 8/8 | **9/9** | +1 |
| S8 — Tabla capacidad→US | ❌ | **✅** | +1 |
| S7 — Palabras | 1 165 | 1 315 | +150 |

\* Corrida 1 usó fórmula ICP v0.4 (sin S8 ni V9).
