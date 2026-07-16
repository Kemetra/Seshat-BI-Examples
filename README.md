# Seshat-BI-Examples

A Retail-BI workspace built on the Seshat governance kit, tracking one table
(`bronze.sales_c086_raw`, a C086 pharmacy branch sales export) through six of
the kit's seven readiness stages: source profiling, mapping, silver, gold,
a metric contract, and an approved dashboard design.

## What's here

- `mappings/sales_c086_raw/` -- the readiness spine for this table
  - `source-profile.md` -- mechanical profile of the 249,106-row bronze export
    (grain, PK proof, blank-value rates, outlier notes)
  - `source-map.yaml` -- the column-by-column keep/drop/rename/type decisions
    and the gold star design, reviewed with the data owner
  - `readiness-status.yaml` -- the machine-readable state of all seven stages
    (evidence, blockers, named-human approvals; never a numeric score)
  - `metrics/TotalSales.yaml` -- the one approved metric contract (formula
    intent, gold binding, the A1/A2 ambiguity rulings)
  - `design/` -- the dashboard layout, visual list, and the
    visual-contract binding map (the design-review sign-off artifact)
- `warehouse/silver/`, `warehouse/gold/` -- the SQL migrations that build
  `silver.sales_c086_raw` and the `gold.fct_sales_c086` star, applied against
  a live Postgres instance and independently re-verified (row-count parity,
  0 orphan FKs, exact total reconciliation)
- `powerbi/example-1.*` -- a blank Power BI Desktop (PBIP) project, added
  ahead of the Publish Ready stage; no DAX or report pages authored yet
- `.env.example` -- the connection-parameter template (copy to `.env`,
  gitignored, never commit real credentials)

## Readiness status

| Stage | Status |
|---|---|
| 1. Source Ready | pass |
| 2. Mapping Ready | pass |
| 3. Silver Ready | pass |
| 4. Gold Ready | pass |
| 5. Semantic Model Ready | pass (`TotalSales` only) |
| 6. Dashboard Ready | pass (one-visual minimal design) |
| 7. Publish Ready | not started |

Check the live state yourself:

```
seshat status --repo .
seshat next --repo .
```

## What this is not

This is a worked example of the governance flow, not a finished BI product.
Notably:

- Only one metric (`TotalSales`, gross of returns, tax treatment explicitly
  deferred) has an approved contract. Any other number -- a trend, a
  by-product or by-division breakdown, a returns rate -- needs its own
  contract and its own owner sign-off before it can appear on a dashboard.
- The dashboard design is deliberately a single KPI card, matching the single
  approved contract -- not a finished report.
- No DAX has been written and the PBIP project is blank. Publish Ready
  (Stage 7) assembles a handoff pack for review; it does not build or push a
  live Power BI model. Live publish is a deliberately separate, deferred step
  the kit does not perform from this surface.

## Data sensitivity

This repo contains a real, if de-identified-where-required, pharmacy branch
sales export: product/drug names, staff role information (names are masked
in `silver`/`gold`, never in `bronze`), and B2B customer references. Treat
`bronze` and any local `.env` credentials accordingly.

## Layout

- `mappings/` -- source-to-mapping evidence (the source-mapping gate)
- `warehouse/{bronze,silver,gold}/` -- medallion SQL homes
- `powerbi/` -- the PBIP project home
- `reports/` -- dashboard-spec / blueprint homes
- `evidence/` -- evidence-pack output home

## Working with this repo

1. Copy `.env.example` to `.env` and fill in your database connection
   parameters (never commit `.env`).
2. Run `seshat check --repo .` to confirm the workspace baseline is clean.
3. Run `seshat next --repo .` to see the guarded next action for
   `sales_c086_raw`, or to onboard a new table from Source Ready.
