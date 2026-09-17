# Checkpoint 4 — Ecosistema de Herramientas e Integraciones

**Curso:** AI Automation Avanzado (CoderHouse)
**Alumna:** Faviola Ramírez
**Módulo 4:** Integración de agentes con herramientas reales del negocio

## Descripción del sistema

Flujo agéntico en n8n que automatiza la **recepción y validación inicial de
solicitudes de pago a proveedores** para una Gerencia Contable que administra
gastos de una matriz y sus filiales. El agente recibe las solicitudes por
correo, extrae sus datos, las registra, valida al proveedor contra el CRM y
prepara un dictamen en borrador para revisión humana.

## Arquitectura del flujo

`Gmail Trigger → IF (seguridad) → AI Agent → Google Sheets → HubSpot → IF (validación) → Gmail Create Draft`

| Nodo | Función |
|------|---------|
| **Gmail Trigger** | Recibe la solicitud de pago por correo |
| **IF (candado anti-bucle)** | Descarta correos automáticos (Out of Office, Auto-reply, no-reply) en inglés y español |
| **AI Agent** (GPT-4o-mini) | Extrae 7 campos estructurados: proveedor, filial, sucursal, solicitante, concepto, monto y fecha |
| **Google Sheets** | Registra toda solicitud (autorizada o no) para trazabilidad |
| **HubSpot Search** | Valida si el proveedor pertenece a la cartera autorizada |
| **IF (validación)** | Bifurca según el resultado del CRM |
| **Gmail Create Draft** | Prepara un dictamen en borrador (Human-in-the-loop): la Gerencia decide el pago |

## Herramientas integradas (OAuth2)

- **Gmail** — recepción y borradores
- **HubSpot** — cartera de proveedores (CRM)
- **Google Sheets** — registro y trazabilidad

## Controles de seguridad implementados

- **Contención de bucles:** filtro de correos automáticos (bilingüe).
- **Búsqueda previa en CRM:** consulta antes de actuar, evitando duplicados.
- **Human-in-the-loop:** salida en modo Borrador; ninguna acción se envía sin revisión humana.

## Nota sobre el estado (MVP)

La validación contra HubSpot está implementada y operativa. El filtrado exacto
por nombre de empresa vía API presenta una limitación conocida del conector
(el campo `company` en Contactos no filtra de forma consistente por búsqueda),
por lo que el sistema se entrega como MVP funcional, dejando el ajuste fino de
coincidencia como mejora futura.

## Escalabilidad prevista

El borrador de dictamen está diseñado para crecer: futuras validaciones
(facturas, listas del SAT, histórico de pagos) se sumarán como secciones
adicionales del mismo correo consolidado que recibe la Gerencia.
