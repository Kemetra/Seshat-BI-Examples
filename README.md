# Retail-BI workspace

A fresh Retail-BI project workspace, scaffolded by `seshat init-project` (spec 107).

## Layout

- `mappings/` -- source-to-mapping evidence (the source-mapping gate populates this)
- `warehouse/{bronze,silver,gold}/` -- medallion SQL homes
- `powerbi/` -- the PBIP project home
- `reports/` -- dashboard-spec / blueprint homes
- `evidence/` -- evidence-pack output home

## Next steps

1. Copy `.env.example` to `.env` and fill in your database connection parameters (never commit `.env`).
2. Initialize git and make a first commit -- the checker reads git-tracked state, so it must run inside a git repo with at least one commit:
   `git init && git add -A && git commit -m "chore: scaffold workspace"`
3. Run `retail check` to confirm the workspace baseline is clean.
4. Follow the readiness flow: profile your first table, then map it (the source-mapping gate) before building silver/gold SQL.
