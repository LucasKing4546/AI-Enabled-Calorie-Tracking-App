# /supabase (Role 2: Backend & data)

Supabase: Postgres, Auth, Storage, RLS. Also owns the schema in `/contracts/db`.

## Scope

- Migrations for `profiles`, `foods`, `products`, `recipes`, `recipe_items`, `meals`, `meal_items`, `scans`.
- RLS on **every** table so each user sees only their own rows. Photos go in private buckets.
- `GET /products/barcode/{code}`: Open Food Facts lookup, cached into `foods`/`products`.
- Account and full data deletion. `scans` rows are kept only with user consent.
- **First task:** a mock server generated from `/contracts/openapi.yaml` so the app can start right away.

## May not touch

`/app`, `/ai`, and `/contracts` other than `contracts/db` (still via an all-four PR).

## Commands

Add run, test and lint commands here once the Supabase project is scaffolded.
