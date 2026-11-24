# Glovebox — System Architecture

## 1. Problem & V1 Scope
- **Goal:** A vehicle lifecycle, maintenance, and aftermarket modification hub tailored for older and modified cars.
- **In Scope (V1):**
  - Vehicle profile management (year, make, model, trim, VIN, current odometer).
  - Deterministic maintenance & modification logging with spend tracking.
  - Mileage/time-based service interval tracking and due-state alerts.
  - Bounded AI/MCP workflow to parse unstructured repair notes/receipts into structured draft entries (requiring user confirmation before saving).
- **Out of Scope (V1):** Native iOS/Android apps, live OBD-II hardware telemetry, automated parts checkout.

## 2. Core Domain Entities (Relational Model)
- `Vehicle` (id, user_id, vin, year, make, model, trim, current_odometer, created_at)
- `MaintenanceEvent` (id, vehicle_id, category, description, odometer_at_service, cost, performed_at, source_type)
- `Modification` (id, vehicle_id, part_name, category, brand, install_odometer, cost, notes, status)
- `ServiceIntervalRule` (id, vehicle_id, service_type, interval_miles, interval_months, last_completed_odometer, last_completed_date)

## 3. High-Level Components & Data Flow
- **Frontend (TS/JS Web UI):** Responsive dashboard for viewing vehicle timelines, logging entries, and reviewing AI-parsed drafts.
- **Backend API (Java / Spring Boot):** Owns authentication, REST endpoints, deterministic validation (e.g., preventing odometer regressions, idempotency keys), interval math, and MySQL persistence.
- **Agent / MCP Layer (Spring AI / MCP Tools):** Constrained to structured extraction and maintenance/part reference lookups; never mutates database state directly without user approval.

## 4. Architectural Trade-Offs & Decisions
- *(Add entries here as you progress through System Design — e.g., why relational SQL over NoSQL, synchronous vs. async interval recalculation, caching strategies, failure modes).*

## 5. Open Questions
- How should VIN/spec enrichment degrade gracefully if an external lookup fails or returns partial trim data?
- What retry/guardrail limits apply to the receipt/note extraction agent?