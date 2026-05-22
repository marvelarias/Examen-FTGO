# PRD — FTGO (Food To Go)

| Campo | Valor |
| :--- | :--- |
| **Versión** | 1.0 |
| **Fecha** | 2026-05-22 |
| **Origen** | [Brief Anexo A](Brief.md) · Prompt PR-PRD-FTGO-001 (mejorado v0.4) |
| **Estado** | Borrador para FSD y ADRs |

---

## 1. Contexto y objetivos

**FTGO** es un marketplace de delivery que conecta consumidores con restaurantes y entrega comida a domicilio. La plataforma opera como **monolito Java (WAR)** con síntomas de *infierno monolítico*: builds lentos, escalado conflictivo, poco aislamiento de fallos y equipos bloqueados por el tamaño del código ([Brief §A.1]).

La dirección definió migrar a **microservicios** con patrón **Strangler Fig** en un horizonte de **18–24 meses**, manteniendo el monolito en producción mientras se documenta y despliega la arquitectura objetivo.

**Objetivo de este PRD:** fijar stakeholders, las **7 capacidades de negocio** del Cap. 2 de Richardson, los **NFRs** del brief y el **alcance** del laboratorio. El PRD alimenta el FSD (user stories §A.5) y **dos ADRs** (descomposición, IPC o datos). No define implementación ni APIs detalladas.

---

## 2. Stakeholders

| Rol | Necesidad principal | Origen |
| :--- | :--- | :--- |
| **Consumidor** | UX rápida, transparencia del estado del pedido, tracking en tiempo real | [Brief §A.2] |
| **Restaurante** | Gestión de tickets, control de carga de cocina, dashboard de pedidos | [Brief §A.2] |
| **Courier** | Asignaciones cercanas, rutas optimizadas, pago confiable | [Brief §A.2] |
| **Empleado FTGO** (back office) | Visibilidad, reportes, resolución de incidentes | [Brief §A.2] |
| **Equipo de arquitectura** | Calidad arquitectónica, trazabilidad, mantenibilidad del rediseño | [Brief §A.2] |
| **Sistemas externos** | Integración estable con Stripe, Google Maps, SendGrid/Twilio | [Brief §A.2] |

---

## 3. Capacidades de negocio

Las siete capacidades siguientes son **candidatas** a límites de bounded context o servicios; la descomposición técnica se decide en ADRs ([Brief §A.3]).

### 3.1 Consumer Management

Registro e inicio de sesión de consumidores; perfiles, direcciones guardadas y preferencias que alimentan el checkout y la entrega ([Brief §A.3]).

### 3.2 Restaurant Management

Alta y mantenimiento de restaurantes asociados; menús, horarios de operación y disponibilidad que condicionan la toma de pedidos ([Brief §A.3]).

### 3.3 Order Taking

Selección de restaurante y menú, carrito, validación de stock y disponibilidad, cálculo del total y confirmación del pedido con número único ([Brief §A.3]; base de US-01).

### 3.4 Order Fulfillment / Kitchen

Emisión de tickets a cocina, aceptación o rechazo por el restaurante, tiempos estimados de preparación y actualización de estado hacia el consumidor ([Brief §A.3]; US-02).

### 3.5 Delivery

Marcación de disponibilidad del courier, oferta de pedidos listos para retirar, asignación con timeout, rutas optimizadas y **tracking en tiempo real** ([Brief §A.3]; US-03).

### 3.6 Billing & Accounting

Cobro al consumidor (vía pasarela externa), comisiones de plataforma y **payouts** a restaurantes y couriers; coherente con checkout y cierre del pedido ([Brief §A.3]).

### 3.7 Notifications

Canales email, SMS y push para confirmaciones, cambios de estado, alertas y recibos; soporte a restaurante, consumidor y courier ([Brief §A.3]).

---

## 4. Requisitos no funcionales

### NFR-01: Carga en horarios pico

- **Métrica:** soportar tráfico **5×** el baseline en **12:00–14:00** y **19:00–22:00** (hora local).
- **Origen:** [Brief §A.4 Carga].
- **Justificación:** almuerzo y cena concentran pedidos; Order Taking y Delivery son los más expuestos.

### NFR-02: Latencia percibida en la app del consumidor

- **Métrica:** **< 200 ms p95** en acciones interactivas del consumidor (menú, carrito, confirmación).
- **Origen:** [Brief §A.4 Latencia UX].
- **Justificación:** la percepción de fluidez impacta conversión en horario pico.

### NFR-03: Disponibilidad del flujo de pedido

- **Métrica:** **99,9 %** mensual mínimo en **toma de pedidos**; tracking en tiempo real puede operar al **99,5 %**.
- **Origen:** [Brief §A.4 Disponibilidad].
- **Justificación:** el ingreso crítico depende de completar pedidos aunque el tracking degrade de forma controlada.

### NFR-04: Tolerancia a fallos de sistemas externos

- **Métrica:** el sistema **acepta pedidos** con pasarela de pago caída (cola de reintentos); mapas/rutas pueden degradarse temporalmente sin bloquear la toma del pedido.
- **Origen:** [Brief §A.4 Tolerancia a fallos externos].
- **Justificación:** Stripe y Google Maps son externos; FTGO no debe detener el negocio por sus caídas.

### NFR-05: Escalabilidad horizontal

- **Métrica:** cada componente desplegable debe escalar de forma **independiente** (ejes **X e Y** del Scale Cube).
- **Origen:** [Brief §A.4 Escalabilidad horizontal].
- **Justificación:** el monolito actual no escala por módulo; la arquitectura objetivo debe permitir escalar Order Taking y Delivery en pico.

### NFR-06: Consistencia de datos

- **Métrica:** **consistencia fuerte** dentro del **aggregate del pedido**; **consistencia eventual** aceptada entre servicios para reporting y analítica.
- **Origen:** [Brief §A.4 Consistencia de datos].
- **Justificación:** el estado del pedido debe ser coherente para consumidor y restaurante; reportes pueden rezagarse.

### NFR-07: Trazabilidad end-to-end

- **Métrica:** **100 %** de acciones del consumidor en flujos críticos con **correlation ID** propagado y soporte de **distributed tracing**.
- **Origen:** [Brief §A.4 Trazabilidad].
- **Justificación:** incidentes en cadena monolito→servicios extraídos requieren seguimiento único del pedido.

### NFR-08: Migración incremental

- **Métrica:** migración **Strangler Fig** en **18–24 meses**; prohibido reemplazo big-bang del monolito.
- **Origen:** [Brief §A.4 Migración incremental].
- **Justificación:** el negocio no puede detener operación; coexistencia controlada con el legacy.

### NFR-09: Stack tecnológico del core

- **Métrica:** **Java / Spring Boot** como stack preferido en servicios core; libertad tecnológica documentada solo en servicios satélite.
- **Origen:** [Brief §A.4 Tecnología].
- **Justificación:** reutilizar conocimiento del equipo y del monolito existente.

### NFR-10: Cumplimiento

- **Métrica:** **PCI-DSS** para pagos delegado a **Stripe** (sin almacenar PAN en FTGO); tratamiento de datos de consumidores conforme a **GDPR** y normativa local.
- **Origen:** [Brief §A.4 Cumplimiento].
- **Justificación:** datos de pago y personales son regulados; el diseño debe minimizar superficie de cumplimiento en FTGO.

---

## 5. Alcance

### Incluye

- Documentación de las **7 capacidades** §A.3 y **10 NFRs** anteriores.
- Contexto de migración **Strangler Fig** y coexistencia con **monolito legacy**.
- Base para **FSD** (US-01, US-02, US-03 y UCs derivados del brief) y **2 ADRs**.

### Excluye

- Implementación de código, esquemas de BD detallados y contratos OpenAPI.
- Decisión final de «un microservicio por capacidad» (→ ADRs).
- Agotar todos los UCs derivables del brief (solo los necesarios para el FSD del laboratorio).

### Coexistencia

El **monolito legacy** sigue sirviendo tráfico mientras se extraen capacidades detrás de fachadas o rutas ([Brief §A.4 Migración incremental], [Brief §A.6]).

---

## Verificación del PRD (V1–V8)

```text
Verificación PRD: V1–V8 → 8/8 ✅ | Pendientes: ninguna
```

| # | Resultado |
| :---: | :--- |
| V1 | ✅ 5 secciones presentes |
| V2 | ✅ 6 stakeholders §A.2 |
| V3 | ✅ 7 capacidades §A.3 |
| V4 | ✅ 10 NFRs con métrica numérica o criterio verificable |
| V5 | ✅ 100 % NFRs con [Brief §A.4] |
| V6 | ✅ Alcance Strangler + exclusiones |
| V7 | ✅ PRD ligero (~1 450 palabras) |
| V8 | ✅ Listo para FSD y ADRs |
