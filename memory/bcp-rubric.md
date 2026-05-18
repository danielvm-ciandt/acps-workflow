# BCP rubric — quick reference (counting command)

**T-shirt scale (all dimensions):** XS = **1**, S = **2**, M = **3**, L = **5**, XL = **8** points.

Use this with **`acps.spec`** (auto-count after spec, or `acps.spec --count-only` to recount; modes: full / simplified / fp-snap). Only count what the **spec explicitly states**; prefer the **simplest** defensible size when ambiguous.

---

## Functional dimensions (1–10)

### 1. Business rules

**Rules:** List **every distinct rule**; score **each rule** (max 8 per rule), then **sum** — total can exceed 8.

| Size | Meaning |
|------|---------|
| **XS** | Simple validations: formats, required fields, simple comparisons, simple permission checks, simple status sets. |
| **S** | Small workflow: few steps/phases, **no** decision points. |
| **M** | Few steps with **2–4** decision points. |
| **L** | Complex conditional logic, many branches. |
| **XL** | Many steps and/or many decision points. |

### 2. Boundaries (integrations / external systems)

**Rules:** If several boundaries appear, use **MAX** complexity only — **do not sum**. Cap **8** (XL).

| Size | Meaning |
|------|---------|
| **XS** | Internal only: DB/UI, no cross-system boundary. |
| **M** | Physical device / hardware-style interface. |
| **L** | Standard remote business APIs (stable contracts). |
| **XL** | Volatile / real-time / “ethereal” exchange (streams, websockets, etc.). |

### 3. Interface elements

**Rules:** Split into **static** vs **dynamic** elements. Group up to **5** of a kind; score **each group**, then **sum** groups (static points + dynamic points). **No global cap** on total — many groups ⇒ large totals.

| Context | Up to 5 static | Up to 5 dynamic |
|---------|----------------|-----------------|
| **Existing** business | S (2) | L (5) |
| **New** business | M (3) | XL (8) |

Groups with **1–5** elements use the same score as a full group of 5 for that type/context.

### 4. Roles / permissions

**Rules:** If not relevant, **0** (skip). Otherwise single band **S–L** (2–5 in rubric examples).

| Size | Meaning |
|------|---------|
| **S** | Same permissions for everyone (one level). |
| **M** | Different permissions **at the same depth** (e.g. Admin vs User). |
| **L** | **Multiple depth levels** / nested RBAC. |

### 5. Solution variabilities

**Rules:** Almost always scored: **no explicit variation ⇒ XS (1)**. Do **not** double-count role/UI/filter “variations” here (those belong elsewhere). Only **explicit** behavioral/config variations count toward S–XL.

| Size | Meaning |
|------|---------|
| **XS** | None stated, or variation is really roles/UI (excluded). |
| **S** | 1–2 explicit variations, simple conditions. |
| **M** | 3–4 explicit variations / conditional behavior. |
| **L** | 5+ variations or complex multi-parameter conditionals. |
| **XL** | Multi-tenant-style or heavily configuration-driven paths **explicitly** described. |

### 6. Domain entities (existing model)

**Rules:** Count **explicit** entities touched.

| Size | Meaning |
|------|---------|
| **XS** | 1 entity |
| **S** | 2–3 entities |
| **M** | 4–5 |
| **L** | 6–7 |
| **XL** | 8+ |

### 7. New domain entities

**Rules:** Only when the spec **introduces new entity types** (not just CRUD on existing types). Band **M–L** in standard rubric.

| Size | Meaning |
|------|---------|
| **M** | New attributes/relationships across up to **3** **existing** entity types. |
| **L** | Up to **3 new** entity types / aggregates in the business context. |

### 8. Background processes

**Rules:** Not synchronous user flow. **Event-driven async** vs **scheduled** jobs.

| Size | Meaning |
|------|---------|
| **M** | Triggered by **system events**, asynchronous. |
| **L** | **Time-scheduled** (daily/weekly/etc.) background work. |

(If not a background process → **0** / not relevant.)

### 9. Notifications

**Rules:** Count **distinct notification channel types** (email, SMS, push, in-app, …) — **not** recipients, not duplicate triggers for the same type.

| Size | Meaning |
|------|---------|
| **XS** | 1 type |
| **S** | 2–3 types |
| **M** | 4+ types |

### 10. Audits

**Rules:** One **audit requirement** (even with many fields: who/when/what/IP) = **XS (1)**. Multiple **different audit purposes/types** increase the band.

| Size | Meaning |
|------|---------|
| **XS** | 1 audit requirement |
| **S** | 2–3 distinct requirements |
| **M** | 4+ distinct requirements |

---

## NFR dimensions (11–13)

Same point scale **XS–XL**. Evaluate **only** explicit NFR text.

### 11. Quality attributes

Performance, scalability, reliability, maintainability, **explicit** quality SLAs.

| Size | Meaning (examples) |
|------|---------------------|
| **XS** | Typical internal / standard web expectations. |
| **S** | Stronger UX-facing targets, light caching. |
| **M** | Business-critical SLAs, observability/tracing. |
| **L** | High throughput / replication / failover. |
| **XL** | Extreme scale / multi-region / mission-critical. |

### 12. Security & compliance

AuthZ, crypto, regulatory frameworks **as stated**.

| Size | Meaning (examples) |
|------|---------------------|
| **XS** | Low risk, basic hygiene. |
| **S** | Business app: sessions, basic RBAC, audit. |
| **M** | MFA, OAuth, GDPR-style data handling. |
| **L** | Sensitive domain, **multiple** frameworks. |
| **XL** | Financial / government-grade, strict certifications. |

### 13. UX & accessibility

Responsiveness, WCAG level, i18n **as stated**.

| Size | Meaning (examples) |
|------|---------------------|
| **XS** | Internal tool, basic responsive. |
| **S** | Public site, keyboard basics, semantic HTML. |
| **M** | WCAG 2.0 A, PWA, single extra language. |
| **L** | WCAG 2.1 AA, RTL, several locales. |
| **XL** | AAA, many languages, flagship UX. |

---

## Calculation

- **Functional BCP** = sum of points for dimensions **1–10** (use **0** when not relevant).
- **NFR BCP** = sum for dimensions **11–13**.
- **Total BCP** = Functional BCP + NFR BCP.

## NFR ratio (diagnostic)

Let **NFR Ratio** = **NFR BCP ÷ Total BCP** (if total is 0, treat ratio as undefined).

| Range | Interpretation |
|-------|----------------|
| **0–15%** | Primarily functional scope |
| **15–30%** | Balanced functional / NFR |
| **30–50%** | NFR-heavy |
| **50%+** | Extreme NFR weight (check for missing functional detail or truly compliance-driven work) |

## Global rules (short)

- **Boundaries:** **MAX** of boundaries, **not** sum.
- **Business rules:** **each rule separately**, then **sum** (can exceed 8 total for the dimension).
- **Interface elements:** **sum** group scores; **no** ceiling on the dimension total.
- **Not every dimension applies** — irrelevant dimensions contribute **0**, not guilt-by-association.

For algorithmic detail and JSON shapes, see `prompts/bcp-functional.txt` and `prompts/bcp-nfr.txt` in this extension.
