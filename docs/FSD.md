# FSD — FTGO (Food To Go)

| Campo | Valor |
| :--- | :--- |
| **Versión** | 1.0 |
| **Fecha** | 2026-05-22 |
| **Entrada** | [PRD v1.0](PRD.md) · [Brief §A.5](Brief.md) |
| **Prompt** | PR-FSD-FTGO-001 (mejorado v0.4) |
| **Estado** | Borrador para ADRs y validación BDD |

---

## Introducción

Este **FSD ligero** detalla los casos de uso del flujo principal de pedido en FTGO: desde la **toma del pedido** por el consumidor hasta la **asignación al courier**, incluyendo **pago en checkout** y **tracking en tiempo real**. Se apoya en el [PRD v1.0](PRD.md) (capacidades §A.3) y en las user stories **US-01, US-02 y US-03** del brief; **UC-04** y **UC-05** son derivados explícitos de §A.5. No cubre back office ni gestión de menús (fuera del alcance mínimo del laboratorio).

---

## Tabla de casos de uso

| ID | Título | Actor primario | Capacidad PRD | Origen |
| :--- | :--- | :--- | :--- | :--- |
| UC-01 | Tomar pedido | Consumidor | Order Taking | [Brief §A.5 US-01] |
| UC-02 | Aceptar o rechazar ticket de pedido | Restaurante | Order Fulfillment / Kitchen | [Brief §A.5 US-02] |
| UC-03 | Asignar pedido a courier | Courier | Delivery | [Brief §A.5 US-03] |
| UC-04 | Procesar pago en checkout | Consumidor | Order Taking + Billing & Accounting | [Brief §A.5 — pago en checkout] |
| UC-05 | Consultar tracking en tiempo real | Consumidor | Delivery + Notifications | [Brief §A.5 — tracking; NFR-02 PRD] |

---

## UC-01: Tomar pedido

| Campo | Valor |
| :--- | :--- |
| **Actor primario** | Consumidor |
| **Capacidad PRD** | Order Taking (§3.3) |
| **Origen** | [Brief §A.5 US-01] |

**Precondiciones:** el Consumidor está autenticado; seleccionó un restaurante con menú publicado y disponible.

**Flujo principal:**

1. El Consumidor consulta el menú del restaurante.
2. Agrega o quita ítems del carrito.
3. Indica dirección de entrega y método de pago.
4. El sistema valida disponibilidad del restaurante y stock de ítems.
5. El sistema registra el pedido y muestra un **número de pedido único**.

**Flujos alternativos:**

- **FA-01 — Restaurante no disponible:** el sistema informa indisponibilidad; no se crea pedido.
- **FA-02 — Ítem sin stock:** el carrito se actualiza y se notifica al Consumidor antes de confirmar.

**Postcondiciones:** pedido creado con número único; estado inicial listo para pago (UC-04) y emisión de ticket a cocina tras confirmación de pago.

**Given/When/Then:**

- **Given:** el Consumidor tiene un carrito con al menos un ítem y el restaurante está disponible.
- **When:** el Consumidor confirma el pedido con dirección de entrega y método de pago válidos.
- **Then:** el sistema crea el pedido y muestra un número de pedido único al Consumidor.

---

## UC-02: Aceptar o rechazar ticket de pedido

| Campo | Valor |
| :--- | :--- |
| **Actor primario** | Restaurante |
| **Capacidad PRD** | Order Fulfillment / Kitchen (§3.4) |
| **Origen** | [Brief §A.5 US-02] |

**Precondiciones:** existe un ticket de pedido pagado o confirmado en estado pendiente para el restaurante.

**Flujo principal:**

1. El Restaurante recibe notificación de un nuevo ticket en su dashboard.
2. El Restaurante **acepta** el ticket e indica el **tiempo estimado de preparación**.
3. El sistema actualiza el estado del pedido y notifica al Consumidor.

**Flujos alternativos:**

- **FA-01 — Rechazo:** el Restaurante rechaza con motivo; el pedido se **cancela** y el Consumidor es notificado ([Brief §A.5 US-02]).

**Postcondiciones:** ticket aceptado con ETA de preparación, o pedido cancelado si se rechazó.

**Given/When/Then:**

- **Given:** el Restaurante tiene un ticket nuevo visible en el dashboard.
- **When:** el Restaurante acepta el ticket e ingresa el tiempo estimado de preparación.
- **Then:** el estado del pedido pasa a «en preparación» y el Consumidor recibe la actualización.

---

## UC-03: Asignar pedido a courier

| Campo | Valor |
| :--- | :--- |
| **Actor primario** | Courier |
| **Capacidad PRD** | Delivery (§3.5) |
| **Origen** | [Brief §A.5 US-03] |

**Precondiciones:** el pedido está listo para retirar en el restaurante; existe al menos un courier disponible en la zona.

**Flujo principal:**

1. El Courier marca **disponibilidad** en la app.
2. El sistema ofrece pedidos listos para retirar **cercanos** a su ubicación.
3. El Courier **acepta** la asignación dentro del plazo (ej. **30 s**).
4. El sistema muestra la **ruta optimizada** al restaurante y al domicilio del Consumidor.

**Flujos alternativos:**

- **FA-01 — Rechazo o timeout:** el Courier rechaza o no responde en 30 s; el sistema ofrece el pedido a otro courier (escenario extendible UC-06, fuera de este FSD mínimo).

**Postcondiciones:** entrega asignada a un courier con ruta visible; pedido en estado «en camino» o equivalente.

**Given/When/Then:**

- **Given:** el Courier está disponible y hay un pedido listo para retirar cerca de su ubicación.
- **When:** el Courier acepta la asignación dentro del timeout de 30 s.
- **Then:** el sistema registra al Courier en el pedido y muestra la ruta optimizada restaurante → consumidor.

---

## UC-04: Procesar pago en checkout

| Campo | Valor |
| :--- | :--- |
| **Actor primario** | Consumidor |
| **Capacidad PRD** | Order Taking + Billing & Accounting (§3.3, §3.6) |
| **Origen** | [Brief §A.5 — pago en checkout] |

**Precondiciones:** el Consumidor completó UC-01; el pedido tiene total calculado y método de pago seleccionado.

**Flujo principal:**

1. El Consumidor inicia el cobro en checkout.
2. El sistema solicita el cargo a la **pasarela externa** (Stripe).
3. La pasarela confirma el pago exitoso.
4. El sistema marca el pedido como pagado y habilita la emisión del ticket a cocina.

**Flujos alternativos:**

- **FA-01 — Pasarela caída ([NFR-04 PRD]):** el sistema **acepta el pedido** en estado pendiente de pago, encola reintento de cobro y notifica al Consumidor; no se pierde la orden.

**Postcondiciones:** pedido pagado o pendiente de pago con reintento programado; sin almacenar PAN en FTGO ([Brief §A.4 Cumplimiento]).

**Given/When/Then:**

- **Given:** existe un pedido confirmado con total y token de pago válido hacia Stripe.
- **When:** el Consumidor confirma el pago en checkout.
- **Then:** el sistema registra el resultado del cobro y actualiza el estado del pedido a «pagado» o «pendiente de pago (retry)».

---

## UC-05: Consultar tracking en tiempo real

| Campo | Valor |
| :--- | :--- |
| **Actor primario** | Consumidor |
| **Capacidad PRD** | Delivery + Notifications (§3.5, §3.7) |
| **Origen** | [Brief §A.5 — tracking; NFR-02 PRD] |

**Precondiciones:** el Consumidor tiene un pedido activo (tras UC-01; idealmente tras UC-02 o UC-03 en curso).

**Flujo principal:**

1. El Consumidor abre el detalle del pedido en la app.
2. El sistema muestra el **estado actual** (preparación, listo, asignado, en camino).
3. El sistema actualiza la posición o ETA según eventos de Delivery y notifica cambios relevantes.

**Flujos alternativos:**

- **FA-01 — Degradación de tracking ([NFR-03 PRD]):** si el subsistema de tracking no responde, se muestra último estado conocido sin bloquear la consulta.

**Postcondiciones:** el Consumidor tiene visibilidad del progreso; correlación de eventos trazable ([NFR-07 PRD]).

**Given/When/Then:**

- **Given:** el Consumidor tiene un pedido activo con al menos un cambio de estado registrado.
- **When:** el Consumidor consulta el tracking en la app.
- **Then:** el sistema muestra estado y ubicación/ETA actualizados según la última información de Delivery.

---

## Trazabilidad al PRD y brief

| UC | US / derivado brief | Capacidad PRD |
| :--- | :--- | :--- |
| UC-01 | US-01 | Order Taking |
| UC-02 | US-02 | Order Fulfillment / Kitchen |
| UC-03 | US-03 | Delivery |
| UC-04 | Derivado §A.5 | Order Taking + Billing |
| UC-05 | Derivado §A.5 + NFR-02 | Delivery + Notifications |
