<!-- input task.components for BE-05: from hobasa-backend-agent/outputs/FEAT-ZD-48600/task.components.md at commit 7cb32bd, produced by BE-04 -->
# task.components: FEAT-ZD-48600

BE-04 "Identify the components/layers to be changed". This file lists the components and layers in ap_aging that the Backend tasks create or update, keeping each change as small as possible. It is based on a read of the current code and on the approved design. It adds no scope beyond TSK-05 (STORY-05) and TSK-06 (STORY-06).

| Field | Value | Source |
|---|---|---|
| Trace ID | FEAT-ZD-48600 | record:brief |
| Step | BE-04 Identify the components/layers to be changed | record:brief |
| Owner / gate | Developer; automated checks, the independent judge and sampled human evaluation | record:brief |
| Inputs | feat.tasks (b0fd4d2), feat.stories (0590ea4), feat.prd (908b58d), feat.trd (3637e51), feat.design (722a98f), task.repo_access (daae080) | record:brief |
| Backend tasks in scope | TSK-05 (STORY-05, HC-6) and TSK-06 (STORY-06, HC-7), both in ap_aging | feat.tasks (section 1); task.repo_access (header) |
| Repository / branch | ap_aging, `feature/story` at `5d2cbb9` | task.repo_access (sections 1, 3) |
| Code read: source and tests | `src/ap_aging/aging.py`, `src/ap_aging/__init__.py`, `tests/test_aging.py`, `tests/__init__.py` | tool:repo |
| Code read: CI and docs | `ci.py`, `README.md` | file:ap_aging/ci.py; file:ap_aging/README.md |

## 1. What the code does today

| Component | Location | Behaviour today | Referenced by | Source |
|---|---|---|---|---|
| `BUCKETS` | `src/ap_aging/aging.py` | Tuple of the five bucket labels | `aging_buckets` | tool:repo |
| `days_past_due(due_date, as_of)` | `src/ap_aging/aging.py` | `max((as_of - due_date).days, 0)`: calendar days after the due date, or 0 when not yet due | `aging_buckets`; `tests/test_aging.py` | tool:repo |
| `bucket_for(days)` | `src/ap_aging/aging.py` | `days < 30` returns `"1-30"`, so exactly 30 days returns `"31-60"` | `aging_buckets`; `tests/test_aging.py` | tool:repo; feat.trd (F-10, R-1) |
| `aging_buckets(invoices, as_of)` | `src/ap_aging/aging.py` | Skips invoices where `inv.get("paid")` is truthy; sums `Decimal(inv["amount"])` per bucket | `src/ap_aging/__init__.py`; `tests/test_aging.py` | tool:repo |
| Typing import | `src/ap_aging/aging.py` | `from typing import Dict, Iterable` (no `List`) | — | tool:repo |
| Package interface | `src/ap_aging/__init__.py` | Imports `aging_buckets`; `__version__ = "1.4.0"`; `__all__ = ["aging_buckets"]` | Not named in any input | tool:repo |
| Existing tests | `tests/test_aging.py` | `test_days_past_due`, `test_bucket_edges`, `test_aging_buckets_skips_paid`, with `AS_OF = date(2026, 10, 25)` | — | tool:repo |
| CI | `ci.py` | Compiles the code and runs `unittest` discovery under `tests/`. Requires coverage of `src/ap_aging` of at least `COVERAGE_MIN = 90.0`. Static checks flag `print`/`eval`/`exec`, bare `except`, and lines over 140 characters | — | file:ap_aging/ci.py |

The code has no service or transport layer between ap_aging and ap_portal, and no database. `src/ap_aging/` holds only `aging.py` and `__init__.py` (tool:repo; feat.design A3 "Transport for CT-1"; task.repo_access section 4).

Items marked **agent judgement** below are this agent's proposals for the smallest change. No input states them.

## 2. Layers: changed or not

| Layer | Change | Why | Source |
|---|---|---|---|
| Domain logic (`src/ap_aging/aging.py`) | **Update (additive only)** | Both new functions go here, next to `days_past_due`, which `overdue_with_risk` reuses | feat.design (DD-2, B3.1); feat.trd (C-6, C-7) |
| Package public interface (`src/ap_aging/__init__.py`) | **Update** | Export the two new functions and make one minor version step | feat.design (B3.1, DD-11); feat.tasks (section 2) |
| Unit tests (`tests/`) | **Create** `tests/test_overdue.py` | New file for the TSK-05 and TSK-06 tests; `tests/test_aging.py` stays unchanged | feat.tasks (section 2, TSK-05, TSK-06) |
| Serialisation (CT-1) | Inside the domain module (`overdue_rows_to_json`); no new layer | ap_aging owns serialisation; no transport is added | feat.design (DD-3, CT-1) |
| Transport / API / service | **No change** | Adding a transport would reopen DES-01 | feat.design (DD-3, A8); feat.tasks (TSK-06 "Must not change") |
| Database / schema | **No change** | No repository uses a database | task.repo_access (section 4); feat.design (A1 "Data") |
| Dependencies | **No change** | Standard library only (`json`, `decimal`, `datetime`, `typing`) | feat.design (DD-1); file:ap_aging/CLAUDE.md |
| CI (`ci.py`) | **No change** | Discovery already picks up any new test file under `tests/`, and the 90% gate covers the new code | file:ap_aging/ci.py |
| Docs (`README.md`) | **No change** | The README does not list functions, so nothing in it goes out of date | file:ap_aging/README.md |

## 3. Components to create or update (minimum change)

### 3.1 `src/ap_aging/aging.py` (update, additive)

| # | Change | Task | Minimal form | Source |
|---|---|---|---|---|
| A1 | Add `import json` | TSK-06 | One import line at the top of the module | feat.design (B3.1); feat.tasks (TSK-06) |
| A2 | Add `List` to the typing import | TSK-05 | Change the line to `from typing import Dict, Iterable, List`. Agent judgement: extend the existing line rather than add a new one | tool:repo; feat.design (B3.1) |
| A3 | New constant `OVERDUE_AFTER_DAYS = 30` | TSK-05 | Agent judgement: one line next to `BUCKETS` | feat.design (B3.2, DD-4) |
| A4 | New constant `TWO_PLACES = Decimal("0.01")` | TSK-05 (TSK-06 reuses it) | Agent judgement: one line next to `OVERDUE_AFTER_DAYS` | feat.design (B3.2); feat.tasks (TSK-06 notes) |
| A5 | New function `overdue_with_risk(invoices: Iterable[Dict], as_of: date, risk_by_vendor: Dict[str, str]) -> List[Dict]` | TSK-05 | One loop. Skip the invoice when `inv.get("paid")` is truthy. Set `days = days_past_due(inv["due_date"], as_of)` and keep only `days > OVERDUE_AFTER_DAYS`. Build the five-key row with `Decimal(inv["amount"]).quantize(TWO_PLACES)` and `risk_by_vendor.get(inv["vendor"], "unknown")`. Then `rows.sort(key=lambda row: (-row["days"], row["id"]))`. Docstring as in feat.design B3.2. Agent judgement: no private helpers are needed, although size class M would allow them | feat.design (B3.2 steps 1 to 4); feat.trd (IF-5.1 to IF-5.7); feat.tasks (section 2, class M) |
| A6 | New function `overdue_rows_to_json(rows: Iterable[Dict]) -> str` | TSK-06 | A single `json.dumps` over a list comprehension, in CT-1 key order: `id`, `vendor`, `days` as `int`, `amount` as `str(Decimal(r["amount"]).quantize(TWO_PLACES))`, `risk`. Docstring as in feat.design B3.2 | feat.design (B3.2, CT-1); feat.tasks (TSK-06 notes) |

**Reused, not changed:**
- `days_past_due`, which counts calendar days (D-2026-031).
- The paid test `inv.get("paid")`, which `aging_buckets` already uses (D-2026-050).

Sources: feat.design (NF-2, NF-3); tool:repo; record:memory.

**Must not change:** `BUCKETS`, `days_past_due`, `bucket_for` and `aging_buckets`, including their signatures and results. `overdue_with_risk` must **never call `bucket_for`**: `bucket_for(30)` returns `"31-60"`, while AC-05.2 excludes an invoice exactly 30 days past due. Sources: feat.tasks (TSK-05); feat.design (DD-4); feat.trd (F-10); tool:repo.

**Rules the code must keep:**
- Money stays `Decimal` and never passes through `float` (D-2026-044, L-011; feat.design NF-1).
- `as_of` is required, with no default (feat.tasks TSK-05 notes).
- A missing `vendor` on an overdue unpaid invoice raises `KeyError`, with no silent default (feat.tasks TSK-05 notes; feat.design B3.4).
- Both functions are pure: no I/O and no change to their inputs (feat.design NF-10).

Sources: feat.tasks (TSK-05 notes); feat.design (NF-1, NF-10, B3.4); record:memory.

### 3.2 `src/ap_aging/__init__.py` (update)

| # | Change | Task | Minimal form | Source |
|---|---|---|---|---|
| I1 | Import the new functions | TSK-05, TSK-06 | Agent judgement: extend the existing line to `from .aging import aging_buckets, overdue_rows_to_json, overdue_with_risk` | tool:repo; feat.design (B3.1) |
| I2 | Add them to `__all__` | TSK-05, TSK-06 | `__all__ = ["aging_buckets", "overdue_with_risk", "overdue_rows_to_json"]` | feat.trd (C-6); feat.design (B3.1) |
| I3 | Version step | TSK-05 (owns the ap_aging version step) | One minor step from `"1.4.0"`. The Developer confirms the exact number (open question BC-2) | feat.tasks (section 2); feat.design (DD-11); tool:repo |

### 3.3 `tests/test_overdue.py` (create)

Setup:
- Use `AS_OF = date(2026, 10, 25)`.
- Build due dates as `AS_OF - timedelta(days=N)` so that `days` is 30, 31, 42 and 75.
- Use synthetic data only (`SYN-INV-*`, "Synthetic Vendor &lt;letter&gt;").

Sources: feat.tasks (TSK-05); feat.design (B3.5, SE-4).

| Test | Covers | Task | Source |
|---|---|---|---|
| `test_overdue_boundary_30_and_31` | AC-05.1, AC-05.2 (L-007) | TSK-05 | feat.tasks (TSK-05); record:memory |
| `test_overdue_excludes_paid_and_not_due` | AC-05.3, AC-05.4 | TSK-05 | feat.tasks (TSK-05) |
| `test_overdue_row_keys_and_sort` | AC-05.5, AC-05.6 (tie on 42: `SYN-INV-002` before `SYN-INV-003`) | TSK-05 | feat.tasks (TSK-05); feat.stories (STORY-05) |
| `test_overdue_unknown_risk` | AC-05.7 | TSK-05 | feat.tasks (TSK-05) |
| `test_overdue_amount_quantised` | AC-05.8 (`Decimal("980.5")` → `Decimal("980.50")`) | TSK-05 | feat.tasks (TSK-05) |
| `test_overdue_missing_vendor_raises` | AC-05.10 | TSK-05 | feat.tasks (TSK-05) |
| `test_existing_aging_unchanged` | AC-05.9 (pins `bucket_for(30) == "31-60"`) | TSK-05 | feat.tasks (TSK-05); feat.design (B3.5) |
| `test_rows_to_json` | AC-06.1 to AC-06.3: `"1250.00"` for SYN-INV-004; every amount a two-decimal string; `days` an integer; `[]` for no rows; CT-1 key order. Compare `json.loads` values, not raw strings. Agent judgement: also assert that no row has a date field, to cover AC-06.4 from feat.stories, which the TSK-06 test list does not name | TSK-06 | feat.tasks (TSK-06); feat.design (B3.5); feat.stories (AC-06.4) |

`tests/test_aging.py` stays unchanged (feat.tasks section 2), and so does `tests/__init__.py` (tool:repo).

## 4. Order of work

| Order | Change | Depends on | Source |
|---|---|---|---|
| 1 | TSK-05: A2, A3, A4, A5; I1 and I2 for `overdue_with_risk`; I3; the TSK-05 tests | Nothing in code | feat.tasks (TSK-05) |
| 2 | TSK-06: A1, A6; I1 and I2 for `overdue_rows_to_json`; `test_rows_to_json` | TSK-05: takes its rows and reuses `TWO_PLACES` | feat.tasks (TSK-06); feat.stories (section 2) |

Neither task depends on another lane's code. ap_aging reads plain dicts (DC-1, DC-3) and imports no other package (feat.design Part C "Lane dependencies").

## 5. Why this is the smallest change

- **Only additive code.** Four existing lines change: the typing import in `aging.py`, and the import, `__version__` and `__all__` lines in `__init__.py`. No existing signature changes (file:ap_aging/CLAUDE.md; feat.design DD-2, NF-4; tool:repo).
- **No new module, layer or dependency.** Agent judgement: no private helper either. Both functions sit in `aging.py`, next to the `days_past_due` they reuse (feat.design DD-1, DD-2; tool:repo).
- **Two new constants only.** `overdue_with_risk` uses both `OVERDUE_AFTER_DAYS` and `TWO_PLACES`; `overdue_rows_to_json` reuses `TWO_PLACES` (feat.design B3.2).
- **No explicit error handling.** A missing `vendor` raises `KeyError` through `inv["vendor"]`, and `test_overdue_missing_vendor_raises` covers it (feat.design B3.4). Agent judgement: other missing keys also raise `KeyError` through indexing, and no input asks for them to be handled.
- **No CI or README change** (file:ap_aging/ci.py; file:ap_aging/README.md).

## 6. Risks for later steps

| ID | Risk | Handling | Source |
|---|---|---|---|
| CR-1 | Reusing `bucket_for` would wrongly list an invoice that is exactly 30 days past due | Use `OVERDUE_AFTER_DAYS` with strict `>`; covered by `test_overdue_boundary_30_and_31` | feat.trd (F-10, R-1); feat.design (DD-4) |
| CR-2 | Coverage could fall below the 90% gate | Agent judgement: the tests in section 3.3 reach the paid, not-yet-due, exactly-30, over-30, unknown-risk, missing-vendor and empty-list paths. BE-06 measures the actual figure | file:ap_aging/ci.py; feat.tasks (section 2 "CI bar") |
| CR-3 | CI imports the third-party `coverage` package, and nobody has checked that it is installed | Carried from task.repo_access RA-2 | task.repo_access (section 5, RA-2) |

## 7. Open questions (non-blocking)

| ID | Question | Interim handling | Owner (proposed) |
|---|---|---|---|
| BC-1 | Where should the new tests go? feat.stories TK-05.2 suggests `tests/test_aging.py`. The later feat.tasks (section 2) assigns a new `tests/test_overdue.py`. feat.design B3.5 allows either | Follow feat.tasks: create `tests/test_overdue.py` | Developer |
| BC-2 | What is the exact ap_aging version after the minor step from `1.4.0`? No input gives the number | Propose a minor step in BE-05 for the Developer to confirm | Developer |
| — | Carried unchanged: RA-1 (one shared branch, `feature/story`, for STORY-05 and STORY-06); RA-2 (whether `coverage` is installed); TQ-1 (`bucket_for(30)`); TQ-3 (CT-1 approval; AC-06.1 to AC-06.4 pending); TQ-9 (`KeyError` for a missing vendor; AC-05.10 pending); PQ-2 (sort order and the meaning of `days`; AC-05.6 pending); DQ-2 (rounding mode) | As recorded in the source files | As recorded |

Sources for the carried items: task.repo_access (section 6); feat.trd (section 7.2); feat.stories (STORY-05, STORY-06); feat.design (D3).

## 8. Validation check

| Check | Result | Source |
|---|---|---|
| Existing code read and understood | Pass (section 1) | tool:repo; file:ap_aging/ci.py; file:ap_aging/README.md |
| Every component to create or update is listed with its task | Pass (section 3) | feat.tasks (TSK-05, TSK-06) |
| The change matches the design | Pass (HC-6, HC-7, B3.1 to B3.5, DD-3, DD-4) | feat.design |
| No existing signature changes; additive only | Pass | file:ap_aging/CLAUDE.md; feat.design (NF-4) |
| Decisions D-2026-031, D-2026-044, D-2026-050; lessons L-007, L-011 | Consistent | record:memory |
| Database or dependency change needed | None | task.repo_access (section 4); feat.design (DD-1) |
| Blocking questions | None | — |
