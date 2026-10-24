# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Product

AI calorie and macro tracking app, built by a 4-person team where each person works in their own Claude session. The sessions can't see each other, so **the repo is the only shared memory**: contracts in `/contracts`, progress in `docs/STATUS.md`.

Core flow: user photographs a meal (or types it, scans a barcode, or photographs a nutrition label) → AI returns an **editable ingredient list** (name, grams, confidence, per-100 g macros) → user edits grams with live recalculation → saves the meal.

## Session start

1. Read this file, then the `CLAUDE.md` in the folder you're working in, then everything in `/contracts`.
2. Read `docs/STATUS.md` for what has landed, what changed in a contract and what's blocked.
3. Work only in your own folder (see ownership below). If a contract needs to change, **stop and write a short proposal** for the user to raise with the team. Don't edit `/contracts` yourself.

## Stack (proposed, confirm at kickoff)

| Layer | Choice |
|---|---|
| Mobile app | React Native + Expo, TypeScript |
| Backend/data | Supabase (Postgres, Auth, Storage, RLS) |
| AI service | Python FastAPI, **one service** with separate routers |
| Meal recognition | Self-hosted open vision-language model, prompted for strict JSON |
| Label reading | OCR (ML Kit or Tesseract) + the same model for structuring |
| Model hosting | **No paid APIs.** LM Studio on Sebi's laptop, reached by the team over Tailscale. OpenAI-compatible API; the AI service reads `AI_BASE_URL` / `AI_MODEL` from `.env`, so moving the model elsewhere is config only. Details in `ai/CLAUDE.md` |
| Nutrition data | USDA FoodData Central (generic foods), Open Food Facts (barcodes) |
| Shared | GitHub monorepo, GitHub Actions CI, OpenAPI spec |

## Architecture

```
Mobile app (/app) ──data, auth──> Supabase (/supabase) ──> Open Food Facts
       │
       └──AI requests──> FastAPI AI service (/ai)
                           ├─ meal      ──> vision model API
                           │    └─ calls nutrition to match foods
                           ├─ nutrition ──> FoodData Central
                           └─ label     ──> OCR / LLM APIs
```

- The app **only** talks to Supabase and the AI service, never to an external API directly. That way each owner can swap a model or data source without touching anyone else's code.
- Meal AI turns detected foods into database entries by calling the nutrition module, not FDC directly.
- Everything except the AI endpoints and `GET /products/barcode/{code}` is plain Supabase table access under RLS.

## Ownership

| Role | Person | Folder(s) | Owns |
|---|---|---|---|
| 1. Mobile app | Mihai | `/app` | All screens, live macro recalculation |
| 2. Backend & data | Kevin | `/supabase`, `/contracts/db` | Schema, migrations, RLS, storage, barcode lookup, account deletion, **mock API** |
| 3. Meal AI | Sebi | `/ai/meal` | `POST /ai/meal/photo`, `POST /ai/meal/text`, meal test set |
| 4. Label AI & nutrition | Lucas | `/ai/label`, `/ai/nutrition` | `POST /ai/label`, FDC lookup and macro calculator, label test set |

Nobody edits another person's folder without a PR that person reviews. Shared by everyone: README, course reports A1–A5.

## Contracts (`/contracts`)

Single source of truth. Code against these files, not against what you assume a teammate built.

- `contracts/db/`: SQL migrations (tables: `profiles`, `foods`, `products`, `recipes`, `recipe_items`, `meals`, `meal_items`, `scans`)
- `contracts/ai/`: JSON schemas for AI output
- `contracts/openapi.yaml`: AI endpoints + barcode lookup
- `contracts/ts/`: shared TypeScript types and the macro function

**Ingredient item** (both meal endpoints return a list of these):
```json
{ "name": "scrambled eggs", "food_id": "fdc:173424", "grams": 120, "confidence": 0.72,
  "per_100g": { "kcal": 148, "protein": 10.0, "carbs": 1.6, "fat": 11.0 } }
```
`food_id` is prefixed by source: `fdc`, `off`, `label`, `manual`. The label endpoint returns `per_100g`, `per_serving`, `serving_grams`, and a list of fields it couldn't read.

**Macro rule:** macros = `per_100g × grams / 100`. The app recalculates on every edit; the server stores a snapshot at save (`meal_items`). This lives in **one function in `/contracts/ts`** that everything uses. Don't reimplement it.

**Label sanity check:** kcal ≈ 4·protein + 4·carbs + 9·fat.

## Mocks first

Nobody waits on anyone. The app runs against a mock server generated from `openapi.yaml` (owned by role 2). The AI routers start as stubs returning fixed sample JSON that matches `/contracts/ai`. Swap mocks for real endpoints in Phase 2.

## Git workflow

- `main` is protected and always passes CI.
- Branches: `<role>/<feature>`, e.g. `app/meal-edit-screen`, `ai/label-ocr`.
- Every PR needs one teammate's review. **A PR touching `/contracts` needs all four.**
- CI on every PR: lint, type-check, unit tests, and an OpenAPI check that the AI service and the mock still match the spec.
- Merge every 2–3 days; rebase on `main` before opening a PR.
- After merging, tell the user what to add to `docs/STATUS.md` (what landed, contract changes, blockers).
- Secrets (model API keys, Supabase service key) go in git-ignored `.env` files, with a committed `.env.example` beside each one.

## Privacy

Food photos and eating habits are personal data: RLS on every table, private storage buckets, `scans` rows (raw AI output + user correction) only with user consent, and full account + data deletion.

## Phases

0. Kickoff (wk 1): repo, CLAUDE.md files, contracts, mocks, CI. **Gate: contracts merged.**
1. Build in parallel (wk 2–4) against mocks. **Gate: real endpoints live.**
2. Integrate (wk 5–6): swap mocks for real ones. **Gate: demo on one phone.**
3. Evaluate & polish (wk 7–8): accuracy numbers, privacy, demo, report.

## graphify

This project has a knowledge graph at graphify-out/ with god nodes, community structure, and cross-file relationships.

Rules:
- For codebase questions, first run `graphify query "<question>"` when graphify-out/graph.json exists. Use `graphify path "<A>" "<B>"` for relationships and `graphify explain "<concept>"` for focused concepts. These return a scoped subgraph, usually much smaller than GRAPH_REPORT.md or raw grep output.
- If graphify-out/wiki/index.md exists, use it for broad navigation instead of raw source browsing.
- Read graphify-out/GRAPH_REPORT.md only for broad architecture review or when query/path/explain do not surface enough context.
- After modifying code, run `graphify update .` to keep the graph current (AST-only, no API cost).
