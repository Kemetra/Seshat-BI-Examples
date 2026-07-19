# Unresolved questions -- `sales_c086_raw`

> Principle-V decision ledger. The mapping stage recorded no OPEN blocking
> questions for this table: every build-blocking mapping decision was resolved
> with the data owner (Ahmed Shaaban) in the 2026-07-16 governed session and
> replayed here. A named-human `mapping_ready` approval is recorded in
> `readiness-status.yaml`. This file grants nothing itself; it is the committed
> audit record the downstream dbt and Dagster gates require.

- **Table id:** `sales_c086_raw`
- **Date raised:** 2026-07-19
- **Raised by:** `seshat mapping-mirror` (generated stub), cleared by data owner
- **Gate status:** CLEARED
- **Cleared by:** Ahmed Shaaban (data_owner), 2026-07-19

## Questions

No OPEN blocking questions remain. The mapping decisions and their resolutions
(all decided 2026-07-16, replayed 2026-07-19) are recorded as evidence in
`readiness-status.yaml` (source_ready + mapping_ready) and detailed in
`source-map.yaml` (per-column keep/drop/rename + RC deviations RC4/RC6/RC9/RC12).

| ID | Question | Resolution | Decided by | Status |
|----|----------|------------|------------|--------|
| Q1 | Grain / primary key | `(reference_no, item_no)`; `billing_document` rejected (format-split). Proven unique on landed data (249,106 = 249,106, 0 null). | Ahmed Shaaban (data_owner) 2026-07-16 | answered |
| Q2 | Net-sales measure | Landed `net_sales` unreliable (90.4% identity) -> rejected; `gross_sales` is THE sales measure. | Ahmed Shaaban (data_owner) 2026-07-16 | answered |
| Q3 | Blank semantics (item_cluster / assignment / certification) | item_cluster + assignment = unknown/missing; certification dropped (no clean applicability). | Ahmed Shaaban (data_owner) 2026-07-16 | answered |
| Q4 | PII handling | person_name -> MASK; customer_name -> KEEP (B2B, low risk); insurance_tel/insurance_no -> DROP. | Ahmed Shaaban (data_owner) 2026-07-16 | answered |
