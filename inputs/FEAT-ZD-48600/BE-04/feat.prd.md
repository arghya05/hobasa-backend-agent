<!-- input feat.prd for BE-04: from hobasa-requirement-agent/outputs/FEAT-ZD-48600/feat.prd.md at commit 908b58d, produced by REQ-05 -->
# feat.prd: FEAT-ZD-48600

Product Requirement Document for FEAT-ZD-48600 (ZD-48600, "Show which overdue invoices belong to vendors that usually pay late"). This is a draft for the REQ-05 gate (PM).

This PRD carries forward the decisions in the approved BRD (feat.brd). It adds no scope. It turns the BRD scope (S-1 to S-7) and business rules (BR-1 to BR-16) into user stories, numbered acceptance criteria in EARS form and non-functional requirements, and ends with a validation check. Statements marked "agent judgement" are this agent's own reading; no input states them as fact. PRD-level assumptions (P-A) and questions (PQ) are new in this step and need confirmation at the gate.

| Field | Value | Source |
|---|---|---|
| Trace ID | FEAT-ZD-48600 | record:brief; feat.brd |
| Intake | ZD-48600, Zendesk ticket (sample, synthetic), channel Customer Feedback, received 2026-10-24 | feat.brd; file:hobasa-agentic-sdlc/requirements/ZD-48600.md |
| Source link (GitHub) | `hobasa-agentic-sdlc/requirements/ZD-48600.md` | feat.brd |
| Trigger | BRD approved | record:brief |
| Fitment | "Yes", linked roadmap item R-AP-12 ("give AP managers earlier visibility of overdue payables") | feat.brd |
| Category / qualifier | New Feature / Not Technical Only | feat.brd |
| Risk tier (advisory) | T2. "The risk label is advisory only." | feat.brd; file:hobasa-agentic-sdlc/requirements/ZD-48600.md |
| Repositories | ap_pipeline, ap_forecast, ap_aging, ap_portal | feat.brd; file:hobasa-agentic-sdlc/requirements/ZD-48600.md |
| Owner / gate of this step | PM / PM | record:brief |
| Template | PRD template: summary, user stories, numbered acceptance criteria (EARS: When <trigger>, the system shall <response>), non-functional requirements, open questions | org.templates |

## 1. Summary

| Item | Statement | Source |
|---|---|---|
| Problem | "AP managers want to see which overdue invoices belong to vendors that usually pay late, so they can chase them first." | feat.brd (section 1) |
| What the product will do | Build each vendor's payment-delay history from a new optional `paid_date` in the vendor feed, give each vendor a rule-based risk level (`high`, `medium`, `low`) from its predicted delay, list unpaid invoices more than 30 days past due with that risk level, and show the list as a table in the portal. | feat.brd (sections 2, 4) |
| Flow | feed -> delays -> risk -> overdue list -> portal table, proven end to end. | feat.brd (S-7, SM-1) |
| Users | AP managers. Finance teams benefit indirectly (agent judgement carried from the BRD). | feat.brd (section 3) |
| Business meaning | Carried from the BRD as an assumption: the risk level helps AP managers decide which overdue payables to act on first (B-A1, still open as OQ-B1). | feat.brd (B-A1, OQ-B1) |
| Architecture rule | "Keep every existing signature; add new functions." Existing behaviour changes only in two additive ways: the optional `risk` argument on `build_explanation_request` (CR-4) and feed ingestion accepting the optional `paid_date` (CR-1). | feat.brd (section 4, SM-5) |
| Not in this change | Finance posting, authentication, tenancy and payment execution; acting on the risk level automatically; an aging export (R-AP-14); a change of LLM provider or model; using an LLM to set the risk level; the work of ZD-48501 and ZD-48211; the regulatory "Late" rule (REG-LPR-2026-4.2). | feat.brd (OS-1 to OS-8) |

## 2. User stories

Stories follow the story template's "As a / I want / So that" form. They are product-level stories; the backlog stories in NEW state are written later, in REQ-08. The "So that" of US-1 comes from the BRD (B-A1). The "So that" clauses of US-2 to US-5, and the choice of the PM as the actor in US-5, are agent judgement: the BRD states the rules and scope behind them but not these purposes.

| ID | As a | I want | So that | BRD scope | Source |
|---|---|---|---|---|---|
| US-1 | AP manager | to see, in the portal, the unpaid invoices that are more than 30 days past due, each with its vendor's risk level, sorted by days overdue (most overdue first) | I can decide which overdue payables to act on first (B-A1) | S-5, S-6 | feat.brd (section 2, S-5, S-6, B-A1) |
| US-2 | AP manager | each vendor's payment-delay history to be built from the date each invoice was actually paid | the risk level reflects how late the vendor's invoices have actually been settled | S-1, S-2 | feat.brd (S-1, S-2, BR-3) |
| US-3 | AP manager | each vendor to get a risk level from a fixed, explicit rule on its predicted delay, with no LLM involved | I can trust and trace why a vendor is marked high, medium or low | S-3 | feat.brd (S-3, BR-4, BR-6, section 2 product-promise link) |
| US-4 | AP manager | the payment-delay explanation to state the vendor's risk level when one is given | the explanation and the table tell the same story | S-4 | feat.brd (S-4, BR-15) |
| US-5 | PM | the whole flow to be proven end to end, from the feed to the portal table | I know the four repositories work together before release | S-7 | feat.brd (S-7, SM-1); record:brief |

## 3. Acceptance criteria (EARS)

Each criterion uses the EARS form "When <trigger>, the system shall <response>" (org.templates). "Days" are calendar days after the due date (BR-9, B-A2).

### 3.1 Vendor feed `paid_date` (ap_pipeline, S-1, US-2)

| # | Acceptance criterion | Traces to | Source |
|---|---|---|---|
| AC-1 | When a vendor feed row has an empty `paid_date`, the system shall accept the row and set `Invoice.paid_date` to None. | BR-1, S-1 | feat.brd (BR-1, S-1); file:hobasa-agentic-sdlc/requirements/ZD-48600.md (org.data_model: "paid_date (date or None)") |
| AC-2 | When a vendor feed row that is marked paid has a `paid_date` that is a valid ISO date, the system shall map it to `Invoice.paid_date`. (A row that is unpaid but has a valid `paid_date` is not covered here; its handling is open as OQ-B6.) | BR-1, S-1 | feat.brd (BR-1, S-1, OQ-B6) |
| AC-3 | When a vendor feed row has a `paid_date` that is not empty and is not a valid ISO date, the system shall reject the row with the reason `'paid_date'`. | BR-2, SM-4 | feat.brd (BR-2, SM-4); assumption P-A3 |

### 3.2 Payment-delay history (ap_pipeline, S-2, US-2)

| # | Acceptance criterion | Traces to | Source |
|---|---|---|---|
| AC-4 | When `delays_by_vendor(records)` is called, the system shall return `{vendor: [delay_days, ...]}` built only from records that are paid and have a `paid_date`, where `delay_days = (paid_date - due_date)` in days, with each vendor's values in feed order. | BR-3, S-2 | feat.brd (BR-3, S-2) |
| AC-5 | When a record is marked paid but has no `paid_date`, the system shall leave it out of the delay history and shall not reject it. | B-A5 | feat.brd (B-A5) |
| AC-6 | When a paid record's `paid_date` is before its `due_date`, the system shall keep the negative `delay_days` as computed, with no floor. | B-A4 | feat.brd (B-A4) |

### 3.3 Vendor risk level and explanation (ap_forecast, S-3, S-4, US-3, US-4)

| # | Acceptance criterion | Traces to | Source |
|---|---|---|---|
| AC-7 | When `vendor_risk(history)` is called and `predict_delay_days(history) > 30`, the system shall return `'high'`. | BR-4 | feat.brd (BR-4) |
| AC-8 | When `vendor_risk(history)` is called and `predict_delay_days(history)` is greater than 10 and not greater than 30 (including exactly 30), the system shall return `'medium'`. | BR-4, BR-5, SM-2 | feat.brd (BR-4, BR-5, SM-2) |
| AC-9 | When `vendor_risk(history)` is called and `predict_delay_days(history)` is not greater than 10 (including exactly 10), the system shall return `'low'`. | BR-4, BR-5, SM-2 | feat.brd (BR-4, BR-5, SM-2) |
| AC-10 | When `vendor_risk` computes a risk level, the system shall apply the rule on the predicted delay only and shall not call an LLM. | BR-6, OS-5 | feat.brd (BR-6, OS-5) |
| AC-11 | When `build_explanation_request` is called with a `risk` argument, the system shall build a prompt that ends with `' Risk level: <risk>.'`. | BR-15, S-4 | feat.brd (BR-15, S-4) |
| AC-12 | When `build_explanation_request` is called without a `risk` argument, the system shall build the same prompt as today. | B-A6, SM-5 | feat.brd (B-A6, SM-5) |

### 3.4 Overdue list with risk (ap_aging, S-5, US-1)

| # | Acceptance criterion | Traces to | Source |
|---|---|---|---|
| AC-13 | When `overdue_with_risk(invoices, as_of, risk_by_vendor)` is called, the system shall include an invoice only when it is unpaid and more than 30 days past due as of `as_of`, counting calendar days after the due date. | BR-7, BR-9, S-5 | feat.brd (BR-7, BR-9, S-5); assumption P-A1 |
| AC-14 | When an unpaid invoice is exactly 30 days past due as of `as_of`, the system shall not include it in the overdue list. | BR-7, SM-2 | feat.brd (BR-7, SM-2); record:memory (L-007) |
| AC-15 | When an invoice is paid, the system shall exclude it from the overdue list, whatever its days past due. | BR-8 | feat.brd (BR-8); record:memory (D-2026-050) |
| AC-16 | When the overdue list is returned, the system shall give each row the keys `id`, `vendor`, `days`, `amount` and `risk`, and shall sort the rows by `days` descending, then by `id`. | BR-10 | feat.brd (BR-10); assumption P-A2 |
| AC-17 | When an invoice's vendor has no entry in `risk_by_vendor`, the system shall set that row's `risk` to `'unknown'`. | BR-11 | feat.brd (BR-11) |
| AC-18 | When the system sets a row's `amount`, it shall use the Invoice `amount` as a Decimal with 2 dp; when the list is passed from ap_aging to ap_portal as JSON, the system shall carry the amount as a string with two decimals and dates as ISO strings. | BR-12, B-A7, SM-3 | feat.brd (BR-12, B-A7, SM-3) |

### 3.5 Portal table (ap_portal, S-6, US-1)

| # | Acceptance criterion | Traces to | Source |
|---|---|---|---|
| AC-19 | When `renderOverdueRisk(rows)` is called with a non-empty list of rows, the system shall render `<table class="overdue-risk">` with one `<tr class="risk-RISK">` per row, showing id, vendor, days and amount to two decimals under the columns Invoice, Vendor, Days overdue and Amount. | BR-13, BR-14, S-6 | feat.brd (BR-13, BR-14, S-6) |
| AC-20 | When a row is rendered, the system shall set its class to `risk-high`, `risk-medium`, `risk-low` or `risk-unknown` to match the row's `risk` value. | BR-14 | feat.brd (BR-14) |
| AC-21 | When `renderOverdueRisk(rows)` is called with no rows, the system shall return an empty string. | BR-13 | feat.brd (BR-13); file:hobasa-agentic-sdlc/requirements/ZD-48600.md (org.design_library: "render nothing when there are no rows") |

### 3.6 End to end (cross-repository, S-7, US-5)

| # | Acceptance criterion | Traces to | Source |
|---|---|---|---|
| AC-22 | When the end-to-end test runs on a vendor feed, the system shall carry the data through feed -> delays -> risk -> overdue list -> portal table, and the rendered table shall match the overdue list built from that feed. | S-7, SM-1 | feat.brd (S-7, SM-1); assumption P-A4 |
| AC-23 | When the end-to-end test runs, the system shall pass each vendor's `delay_days` list from `delays_by_vendor` to `vendor_risk` as its `history`, and shall pass the resulting vendor-to-risk map to `overdue_with_risk` as `risk_by_vendor`. | B-A3 | feat.brd (B-A3) |

## 4. Non-functional requirements

| ID | Requirement | Traces to | Source |
|---|---|---|---|
| NFR-1 | Monetary amounts use Decimal, never float; Decimal arithmetic is kept and amounts are never converted to float. | BR-12, SM-3 | feat.brd (BR-12, SM-3); record:memory (D-2026-044, L-011) |
| NFR-2 | JSON between services carries amounts as strings with two decimals and dates as ISO strings. | BR-12 | feat.brd (BR-12); file:hobasa-agentic-sdlc/requirements/ZD-48600.md (org.architecture) |
| NFR-3 | ap_portal shows the amount to two decimals without converting it to a binary floating-point number. Agent judgement: D-2026-044 applies to ap_portal too. How ap_portal does this is left to the TRD (REQ-07); OQ-B10 stays open for confirmation. | SM-3, OQ-B10 | feat.brd (SM-3, OQ-B10); record:memory (D-2026-044) |
| NFR-4 | Backward compatibility: "Keep every existing signature; add new functions." Only the two additive changes CR-1 and CR-4 touch existing behaviour; callers that do not pass `risk` see no change (AC-12). | SM-5, OS-6 | feat.brd (section 4, SM-5, OS-6) |
| NFR-5 | Auditability: the risk level comes from an explicit rule (no LLM), and rejected feed rows carry a named reason (`'paid_date'`). | BR-2, BR-6 | feat.brd (section 2 product-promise link, BR-2, BR-6) |
| NFR-6 | The risk label is advisory only. No risk value shall trigger an automatic action, posting or payment. | BR-16, OS-1, OS-2 | feat.brd (BR-16, OS-1, OS-2) |
| NFR-7 | Security and tenancy: no finance posting, authentication, tenancy or payment execution in this change (risk tier T2). Whether `renderOverdueRisk` must HTML-escape vendor names and ids is open (OQ-B8). | OS-1, OQ-B8 | feat.brd (OS-1, OQ-B8); file:hobasa-agentic-sdlc/requirements/ZD-48600.md (org.risk_map) |
| NFR-8 | Testability: boundary cases are tested explicitly: an unpaid invoice exactly 30 days past due (not listed, AC-14), and predicted delays of exactly 30 (`'medium'`, AC-8) and exactly 10 (`'low'`, AC-9). | SM-2 | feat.brd (SM-2); record:memory (L-007) |
| NFR-9 | Feed schedule unchanged: daily 02:00 Asia/Kolkata. | S-1 | feat.brd (S-1: "The schedule is unchanged"); file:hobasa-agentic-sdlc/requirements/ZD-48600.md (org.customer_mappings: "Schedule: daily 02:00 Asia/Kolkata (unchanged)") |
| NFR-10 | Terminology: the portal and later documents keep "overdue" (more than 30 days past due) distinct from the regulatory "Late" label (more than 60 days, REG-LPR-2026-4.2). Carried as a recommendation; OQ-B2 stays open. | OS-8, OQ-B2 | feat.brd (OS-8, OQ-B2) |
| NFR-11 | Performance, data volume and availability targets: not given in the inputs (PQ-4). | — | feat.brd; file:hobasa-agentic-sdlc/requirements/ZD-48600.md |

Background, not a requirement: org.architecture describes ap_pipeline, ap_forecast and ap_aging as Python 3.9 and ap_portal as Node.js 22 CommonJS (file:hobasa-agentic-sdlc/requirements/ZD-48600.md). No input sets a requirement on runtimes, so this PRD sets none; runtime matters are for the TRD (REQ-07).

## 5. Assumptions

### 5.1 Carried from the approved BRD

| ID | Assumption | Used in | Source |
|---|---|---|---|
| B-A1 | The risk level helps AP managers decide which overdue payables to act on first. | Summary, US-1 | feat.brd |
| B-A2 | "Days past due" and `delay_days` are calendar days, following D-2026-031. | AC-4, AC-13 | feat.brd; record:memory (D-2026-031) |
| B-A3 | `history` for `vendor_risk` is one vendor's `delay_days` list from `delays_by_vendor`; `risk_by_vendor` maps each vendor to its `vendor_risk` result. | AC-23 | feat.brd |
| B-A4 | A negative `delay_days` is kept as computed. | AC-6 | feat.brd |
| B-A5 | A paid record with no `paid_date` is left out of the delay history, not rejected. | AC-5 | feat.brd |
| B-A6 | Without `risk`, `build_explanation_request` builds today's prompt. | AC-12 | feat.brd |
| B-A7 | The overdue-list `amount` is the Invoice `amount` (Decimal, 2 dp) and travels to ap_portal as a two-decimal string. | AC-18 | feat.brd |

### 5.2 New in this PRD (please confirm at the gate)

| ID | Assumption | Used in | Source |
|---|---|---|---|
| P-A1 | `days` in an overdue row is the number of calendar days from `due_date` to `as_of` (agent judgement from BR-9 and the `as_of` argument). | AC-13, AC-16 | feat.brd (BR-9, S-5) |
| P-A2 | "Then by id" means ascending order of `id`, compared as text, because the data model types `id` as a string (agent judgement; the ticket does not state the direction). | AC-16 | feat.brd (BR-10); file:hobasa-agentic-sdlc/requirements/ZD-48600.md (org.data_model: "id (str)") |
| P-A3 | An "invalid" `paid_date` is a non-empty value that is not a valid ISO date (agent judgement from "ISO date, empty when unpaid"). | AC-3 | feat.brd (BR-1, BR-2) |
| P-A4 | "Proven end to end" means the end-to-end test checks that the rendered table matches the overdue list built from the same feed (agent judgement; the BRD says only that the flow is proven end to end). | AC-22 | feat.brd (S-7, SM-1) |

## 6. Open questions (non-blocking)

### 6.1 Carried from the approved BRD

| ID | Question | Owner (as recorded) | Source |
|---|---|---|---|
| OQ-B1 | Confirm the business meaning of "vendors that usually pay late" and "chase them first" (B-A1). | PM / Leadership | feat.brd |
| OQ-B2 | Keep "overdue" (more than 30 days) and the regulatory "Late" (more than 60 days) as distinct labels (NFR-10). | Not recorded | feat.brd |
| OQ-B3 | Which business KPI should measure success? The inputs give none. | Not recorded | feat.brd |
| OQ-B4 | Confirm calendar days for "days past due" in `overdue_with_risk`, with the 30-day boundary tested. | Not recorded | feat.brd |
| OQ-B5 | Should one shared overdue definition serve this item, ZD-48501 and ZD-48211? | PM | feat.brd |
| OQ-B6 | How should an unpaid row that has a `paid_date` be handled: reject it, or ignore the date? No acceptance criterion is written for this case until it is answered. | Not recorded | feat.brd |
| OQ-B7 | Should an approved business question set be added to org.templates? | Not recorded | feat.brd; org.templates |
| OQ-B8 | Should `renderOverdueRisk` HTML-escape the vendor names and ids it renders (NFR-7)? | Not recorded | feat.brd |
| OQ-B9 | Should the portal show the risk level as visible text, or only as the row class `risk-RISK`? AC-19 follows CR-6 (id, vendor, days, amount shown) until answered. | Not recorded | feat.brd |
| OQ-B10 | How should ap_portal format the two-decimal amount string without converting it to a binary float (NFR-3)? | Not recorded | feat.brd |
| — | Carried unchanged: feat.fitment FQ-3, FQ-5 and FQ-6, and the questions feat.fitment carries from feat.classification (OQ-1 to OQ-5) and feat.prebacklog (Q-1 to Q-5). | — | feat.brd (section 11) |

### 6.2 New in this PRD

| ID | Question | Why it matters | Source |
|---|---|---|---|
| PQ-1 | What should `vendor_risk` return when `history` is empty? The inputs do not say how `predict_delay_days` behaves on an empty list. Agent judgement: if, under B-A3, a vendor with no paid history is given no entry in `risk_by_vendor`, its rows get `'unknown'` (AC-17); but the inputs do not say whether such a vendor gets an entry, and a direct call with an empty list is not specified. | Edge case for AC-7 to AC-9 | feat.brd (BR-4, BR-11, B-A3) |
| PQ-2 | Confirm P-A1 to P-A4 (meaning of `days`, the direction and type of the `id` tie-break, the meaning of "invalid" `paid_date`, and what the end-to-end test checks). | Exact test expectations for AC-3, AC-13, AC-16, AC-22 | this file |
| PQ-3 | Should the overdue list have a named JSON contract between ap_aging and ap_portal (field names, the type of `days`, `amount` as a string)? Proposed for the TRD (REQ-07); the type of `days` is not given in the inputs. | Interface between two coding lanes | feat.brd (BR-12, B-A7) |
| PQ-4 | Are there performance, data volume or availability targets (NFR-11)? | Non-functional completeness | this file |
| PQ-5 | Who decides the order of this change and PROV-ACME-2026-11, which both change `build_explanation_request`? | Delivery dependency for AC-11, AC-12 | feat.brd (section 12, risks and dependencies) |
| PQ-6 | CR-1 needs feed v3 and ZD-48470 brings feed v2. Is feed v3 available to ap_pipeline for this change? | Delivery dependency for AC-1 to AC-3 | feat.brd (section 12, risks and dependencies) |

## 7. Traceability (BRD to PRD)

| BRD item | PRD coverage |
|---|---|
| S-1 | US-2; AC-1, AC-2, AC-3; NFR-9 |
| S-2 | US-2; AC-4, AC-5, AC-6 |
| S-3 | US-3; AC-7, AC-8, AC-9, AC-10 |
| S-4 | US-4; AC-11, AC-12 |
| S-5 | US-1; AC-13 to AC-18 |
| S-6 | US-1; AC-19, AC-20, AC-21 |
| S-7 | US-5; AC-22, AC-23 |
| BR-1 / BR-2 | AC-1, AC-2 / AC-3 |
| BR-3 | AC-4 |
| BR-4 / BR-5 | AC-7, AC-8, AC-9 |
| BR-6 | AC-10; NFR-5 |
| BR-7 / BR-8 / BR-9 | AC-13, AC-14 / AC-15 / AC-13 |
| BR-10 / BR-11 | AC-16 / AC-17 |
| BR-12 | AC-18; NFR-1, NFR-2, NFR-3 |
| BR-13 / BR-14 | AC-19, AC-21 / AC-19, AC-20 |
| BR-15 | AC-11 |
| BR-16 | NFR-6 |
| SM-1 | AC-22 |
| SM-2 | AC-8, AC-9, AC-14; NFR-8 |
| SM-3 | AC-18; NFR-1, NFR-2, NFR-3 |
| SM-4 | AC-3 |
| SM-5 | AC-12; NFR-4 |
| SM-6 (no business KPI) | OQ-B3 carried |
| OS-1 to OS-8 | Summary ("Not in this change"); NFR-6, NFR-7, NFR-10 |
| B-A1 to B-A7 | Section 5.1 |
| OQ-B6 (unpaid row with a `paid_date`) | Left open; AC-2 is limited to paid rows so that it does not decide the case |

## 8. Validation check

### Template

| Check | Result | Source |
|---|---|---|
| PRD template sections present: summary, user stories, numbered acceptance criteria (EARS), non-functional requirements, open questions | Pass: sections 1, 2, 3, 4 and 6. | org.templates |
| Every acceptance criterion uses "When <trigger>, the system shall <response>" | Pass: AC-1 to AC-23. | org.templates |
| User stories use "As a / I want / So that" | Pass: US-1 to US-5. | org.templates |

### Carry-forward from the approved BRD

| Check | Result | Source |
|---|---|---|
| Every BRD scope item S-1 to S-7 is covered | Pass (section 7). | feat.brd |
| Every business rule BR-1 to BR-16 is covered by an acceptance criterion or NFR | Pass (section 7). | feat.brd |
| Every delivery success measure SM-1 to SM-5 is covered | Pass (section 7). SM-6 has no business KPI; OQ-B3 is carried. | feat.brd |
| Out of scope OS-1 to OS-8 is kept, and no new scope is added | Pass. The PRD adds no function, repository or behaviour beyond S-1 to S-7. | feat.brd |
| BRD assumptions and open questions are carried, not silently resolved | Pass. B-A1 to B-A7 and OQ-B1 to OQ-B10 are carried. NFR-3 states a requirement for OQ-B10 as agent judgement and keeps the question open. | feat.brd |
| Every non-functional requirement is backed by a BRD item, a decision, a lesson or a stated source rule | Pass. Runtime versions from org.architecture are given as background only, not as a requirement. | feat.brd; record:memory; file:hobasa-agentic-sdlc/requirements/ZD-48600.md |
| No regulation applies, so no regulation section and page is cited | Pass, as recorded in the BRD ("No regulatory source applies to this change"). | feat.brd (BQ-9) |

### Decision log

| Decision | Result | Source |
|---|---|---|
| D-2026-031: aging buckets use calendar days after due date | No conflict. Applied to the overdue list and `delay_days` as assumption B-A2 (AC-4, AC-13). | record:memory; feat.brd |
| D-2026-044: monetary amounts use Decimal, never float | Consistent (AC-18, NFR-1 to NFR-3). | record:memory; feat.brd |
| D-2026-050: paid invoices are excluded from aging | Consistent (AC-15). | record:memory; feat.brd |

No conflict with a recorded decision or with the approved BRD was found.

### Lessons applied

| Lesson | Applied in | Source |
|---|---|---|
| L-007: test the boundary day (exactly 30 days) explicitly for any day-count rule | AC-14, NFR-8 | record:memory |
| L-011: keep Decimal arithmetic; never convert amounts to float | AC-18, NFR-1, NFR-3 | record:memory |
