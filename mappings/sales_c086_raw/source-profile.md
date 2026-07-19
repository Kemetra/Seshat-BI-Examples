# Source Profile -- `sales_c086_raw`

> Filled instance of the source-profile template for the C086 pharmacy branch sales
> export, landed into the `Ex-1` DB bronze layer. Stage 1 (Source Ready) of the
> readiness spine. Mechanical numbers measured over a READ-ONLY connection via
> `seshat.profile.profile()` against `bronze.sales_c086_raw`; semantic rows below are
> PROPOSED for human confirmation, not asserted as fact. ASCII only.

---

## Header

| Field | Value |
|-------|-------|
| Table id | `sales_c086_raw` |
| Source kind | `db-table` |
| Source system | Pharmacy branch (site `C086`) sales export, folded from multiple periodic Excel exports (see `_source_file` lineage) |
| Landed location | `bronze.sales_c086_raw` (faithful all-TEXT landing + `_source_file`/`_loaded_at` lineage) |
| Connection | read-only; credentials from the gitignored `.env` at runtime -- no connection string committed |
| Profiled on | 2026-07-19 |
| Profiled by | agent (`seshat.profile.profile()`, read-only session over `doctl`-provisioned Postgres; `SET default_transaction_read_only = on`) |
| Source files folded in | 4 distinct values in `_source_file` (e.g. `C086_Sales_2023_H1_Jan-Jun.xlsx`) -- see Cross-file schema drift below |

---

## Shape

| Metric | Value |
|--------|-------|
| Row count (landed) | 249,106 |
| Column count (landed) | 46 source columns (+ 2 lineage: `_source_file`, `_loaded_at`) |
| Schema(s) present | `bronze` (this table); `silver`/`gold` not yet created |

---

## Per-column profile

Missingness measured as `trim(col) = '' OR col IS NULL` (RC5 -- a faithful landing writes
`''` for blank, so `IS NULL` alone would report 0). All source columns landed as `TEXT`.
Numbers from `seshat.profile.profile()` against the live table, 2026-07-19.

| Column | Type as landed | Missingness (`'' OR NULL`, count / %) | Distinct cardinality | Candidate key? | Notes |
|--------|----------------|----------------------------------------|----------------------|----------------|-------|
| `material` | TEXT | 0 / 0.00% | 9,690 | no | product code; 1:1 with `material_desc` |
| `material_desc` | TEXT | 0 / 0.00% | 9,690 | no | product description; 1:1 with `material` |
| `quantity` | TEXT | 0 / 0.00% | 369 | no | float-as-text; sign does NOT reliably indicate a return |
| `personel_number` | TEXT | 1,748 / 0.70% | 258 | no | staff id (pharmacist/seller) |
| `person_name` | TEXT | 22,044 / 8.85% | 142 | no | staff display name (Arabic); PII -- see PII ruling |
| `position` | TEXT | 22,044 / 8.85% | 32 | no | staff role; missingness co-occurs with `person_name` |
| `category` | TEXT | 3 / 0.00% | 88 | no | product category; 1:1 with `material` |
| `division` | TEXT | 3 / 0.00% | 15 | no | e.g. `RX`, `OTC`; drives the silver row filter |
| `customer_name` | TEXT | 0 / 0.00% | 512 | no | Arabic customer display name; 1:1 with `customer` |
| `brand` | TEXT | 284 / 0.11% | 3,420 | no | product brand |
| `item_cluster` | TEXT | 79,734 / 32.01% | 7 | no | RULED blank = unknown/missing (data owner) |
| `subcategory` | TEXT | 3 / 0.00% | 243 | no | product subcategory |
| `segment` | TEXT | 3 / 0.00% | 699 | no | therapeutic/marketing segment |
| `billing_type` | TEXT | 0 / 0.00% | 10 | no | **authoritative returns/billing-type column** (Arabic) |
| `certification` | TEXT | 169,302 / 67.96% | 19,742 | no | **DROPPED** (data owner) -- no clean applicability pattern |
| `assignment` | TEXT | 116,776 / 46.88% | 12,820 | no | RULED blank = unknown/missing (credit-account-like) |
| `salse_not_tax` | TEXT | 0 / 0.00% | 7,234 | no | money-like (sic source name); dropped later (RC9) |
| `fi_document_no` | TEXT | 33,809 / 13.57% | 89,887 | no | FI/accounting doc ref; NOT unique alone |
| `insurance_tel` | TEXT | 95,018 / 38.14% | 10,108 | no | **PII** -- insurance contact phone; RULED DROP |
| `insurance_no` | TEXT | 95,018 / 38.14% | 14,436 | no | **PII** -- insurance policy no; RULED DROP; co-missing with `insurance_tel` |
| `dis_tax` | TEXT | 0 / 0.00% | 4,555 | no | money; discount-tax component |
| `paid` | TEXT | 0 / 0.00% | 15,891 | no | money |
| `kzwi1` | TEXT | 0 / 0.00% | 6,504 | no | SAP condition-value field; dropped later (RC9) |
| `crm_order` | TEXT | 247,912 / 99.52% | 579 | no | CRM order ref; near-entirely blank; drop candidate |
| `buyer` | TEXT | 6 / 0.00% | 17 | no | buyer/purchaser code |
| `item_status` | TEXT | 0 / 0.00% | 7 | no | `ACTIVE`/`DELISTED`/`CANCELLED` + `-T` variants |
| `subtotal5_discount` | TEXT | 0 / 0.00% | 4,069 | no | money |
| `tax` | TEXT | 0 / 0.00% | 3,929 | no | money; tax component |
| `add_dis` | TEXT | 0 / 0.00% | 1,344 | no | money; additional discount |
| `customer` | TEXT | 0 / 0.00% | 638 | no | customer code; 1:1 with `customer_name` |
| `mat_group` | TEXT | 0 / 0.00% | 536 | no | material group; 1:1 with `mat_group_2`; dropped (RC12) |
| `mat_group_2` | TEXT | 0 / 0.00% | 536 | no | material group 2; dropped (RC12) |
| `item_no` | TEXT | 0 / 0.00% | 44 | part-of-composite | line-item number; PK col 2 of (reference_no, item_no) |
| `knumv` | TEXT | 249,106 / 100.00% | 1 | no | 100% blank single-value; drop (RC3) |
| `billing_document` | TEXT | 0 / 0.00% | 102,818 | no | REJECTED as PK -- conflates two doc/channel types |
| `billing_type_2` | TEXT | 0 / 0.00% | 10 | no | billing-type code; -> `billing_type_code` |
| `cosm_mg` | TEXT | 0 / 0.00% | 1 | no | single-value; drop (RC3) |
| `area_mg` | TEXT | 0 / 0.00% | 1 | no | single-value; drop (RC3) |
| `date` | TEXT | 0 / 0.00% | 1,094 | no | transaction date; -> `sale_date` |
| `reference_no` | TEXT | 0 / 0.00% | 102,818 | part-of-composite | invoice/reference; PK col 1; uniform `C086`+10 digits |
| `site` | TEXT | 0 / 0.00% | 1 | no | single-value (`C086`); drop (RC3) |
| `site_name` | TEXT | 0 / 0.00% | 1 | no | single-value; drop (RC3) |
| `gross_sales` | TEXT | 0 / 0.00% | 4,939 | no | **THE sales measure** (net_sales rejected) |
| `net_sales` | TEXT | 0 / 0.00% | 11,730 | no | landed net_sales -- RULED UNRELIABLE, rejected |
| `ref_return` | TEXT | 236,741 / 95.04% | 4,183 | no | original-ref for return rows; sparse |
| `ref_return_date` | TEXT | 249,106 / 100.00% | 1 | no | 100% blank single-value; drop (RC3) |

**Lineage columns** (added by the landing loader, not source): `_source_file` (4 distinct,
0 missing), `_loaded_at` (TIMESTAMPTZ, 4 distinct, 0 missing).

---

## Semantics

Derived from the data, not field names.

- **Code <-> label pairs.** `material` <-> `material_desc`: 1:1 (9,690 <-> 9,690).
  `customer` <-> `customer_name`: 1:1 (638 code vs 512 name -- some codes share a name).
- **Returns population & identification.** Identified from the **authoritative column**
  `billing_type` (Arabic `mrtja` values), NOT the sign of `quantity`. Sign-alone would
  misclassify 2,030 rows. (RC8.)
- **net_sales reliability (money-relationship check).** `gross_sales + dis_tax + add_dis
  - tax == net_sales` holds on only 90.4% (225,191 / 249,106) rows; 7 alternative formulas
  tested, none better. Of 23,915 mismatches, only 4,723 (19.7%) are rounding noise
  (<= 0.01); the remaining 19,192 carry a material residual (avg 6.18, range
  -921.04 .. 5225.44). Mismatch rate ~10-11% uniformly across every `billing_type` -- not
  a returns artifact. **RULED: landed `net_sales` is UNRELIABLE and rejected as a trusted
  measure. `gross_sales` is THE sales measure.**
- **Blank semantics.** `item_cluster` blank (32.01%) and `assignment` blank (46.88%) both
  RULED = unknown/missing (data owner). `certification` (67.96% blank) DROPPED entirely.
- **PII.** `person_name` (staff/employee) -> MASK. `customer_name` (predominantly
  B2B/institutional) -> KEEP as-is (low risk). `insurance_tel` / `insurance_no` (genuine
  third-party insurance data) -> DROP entirely.
- **Single-value drop candidates (RC3).** `knumv`, `cosm_mg`, `area_mg`, `site`,
  `site_name`, `ref_return_date` each carry 1 distinct value (or 100% blank).

---

## Candidate grain & candidate PK

- **Candidate grain:** one row = one invoice line item.
- **Grain ratio:** 249,106 rows vs 102,818 distinct `reference_no` = 2.42 lines per invoice.
- **Candidate PK:** `( reference_no, item_no )`.
- **Uniqueness proof (on the landed data, measured 2026-07-19 via `seshat.profile.profile()`):**
  - `COUNT(*)            = 249,106`
  - `COUNT(DISTINCT pk)  = 249,106`   *(equals `COUNT(*)` -> PK holds)*
  - `NULLs in PK columns = 0`
  - `is_unique           = True`
- **`billing_document` REJECTED as PK:** mixes two prefixes (`'0'` on 215,297 rows vs
  letter `'O'` on 33,809), and the O-prefix rows exactly match where `fi_document_no` is
  blank (33,809) -- it conflates two document/channel types under one column.

> RC2 forward seam: this candidate PK is on the LANDED data; the silver build must
> re-prove it on the TRANSFORMED output (after TRIM/cast/filter).

---

## Top data-quality issues

1. `net_sales` unreliable -- identity holds on only 90.4% (225,191 / 249,106); rejected as a measure.
2. `certification` 67.96% blank (169,302 rows), no clean applicability pattern -- dropped.
3. `assignment` 46.88% blank (116,776) and `item_cluster` 32.01% blank (79,734) -- ruled unknown/missing.
4. PII present: `person_name` (mask), `insurance_tel`/`insurance_no` (drop).
5. `billing_document` conflates two document/channel types (215,297 `'0'` vs 33,809 `'O'`).

---

## Exit gate

- [OK] Grain stated, with the row-vs-entity ratio (2.42 lines per invoice).
- [OK] Candidate PK stated and proven unique on the landed data (249,106 = 249,106, 0 NULL PK).
- [OK] Returns rule stated, from the authoritative column `billing_type` (not a measure sign).
- [OK] Top data-quality issues listed, each with a measured count.
- [OK] Missingness measured as `'' OR NULL` for every column.

---

## Next artifact

Once this exit gate passes, proceed to **`source-map.yaml`** -- the machine-readable spine
recording per-column keep/drop/rename/type decisions, the grain + PK decided first, the
target silver column, and the gold star placement. This profile is its evidence base.
