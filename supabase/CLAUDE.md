# /supabase (Role 2: Backend & data, owner Kevin)

Supabase in the **EU region**: Postgres, Auth, Storage, RLS, Edge Functions. Kevin also owns the schema in `/contracts/db`, the GitHub repo and CI.

| Path | Contents |
|---|---|
| `migrations/` | Tables `profiles`, `foods`, `products`, `recipes`, `recipe_items`, `meals`, `meal_items`, `scans` |
| `functions/barcode-lookup/` | Edge Function: Open Food Facts lookup, cached into `foods`/`products` |
| `functions/_shared/` | Code shared between Edge Functions |

## Scope

- RLS on **every** table so each user sees only their own rows. Photos in private buckets.
- Auth, meal saving (with the macro snapshot from `contracts/ts/macros.ts`), rate limits (including the per-user daily scan cap), account and full data deletion. `scans` rows are kept only with user consent.
- **Week 1:** seed data so the app can be built before real data exists.

## May not touch

`/app`, `/ai`, `/eval`, and `/contracts` other than `contracts/db` (still via an all-four PR).

## Commands

Add run, test and lint commands here once the Supabase project is scaffolded.
