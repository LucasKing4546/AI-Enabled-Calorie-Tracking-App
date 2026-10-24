# /ai (Roles 3 & 4: FastAPI AI service)

One Python FastAPI service, one router per subfolder. Each router starts as a stub returning fixed sample JSON that matches `/contracts/ai`, then gets the real model.

| Subfolder | Owner | Endpoint(s) |
|---|---|---|
| `meal/` | Role 3 | `POST /ai/meal/photo`, `POST /ai/meal/text` |
| `label/` | Role 4 | `POST /ai/label` |
| `nutrition/` | Role 4 | FDC food search/lookup + macro calculator (internal, called by `meal/`) |

Each role edits only its own subfolders. Shared service setup (app entrypoint, config, dependencies) changes via a PR the other AI role reviews.

## meal/ (Role 3)

Pipeline: vision-language model → foods + estimated grams → match each via `nutrition/` (not FDC directly) → per-item confidence → list of ingredient items. When matching is uncertain, return the top 3 candidates. Owns the meal test set (photos with weighed portions); report gram and kcal error.

## label/ (Role 4)

OCR the nutrition table → LLM structures it into `per_100g`, `per_serving`, `serving_grams` → list unreadable fields → check kcal ≈ 4·protein + 4·carbs + 9·fat. Owns the label test set (blurry, curved, non-English); report field accuracy.

## nutrition/ (Role 4)

FoodData Central search and lookup, used by `meal/`. Cache results; model and API costs have a monthly budget cap.

## May not touch

`/app`, `/supabase`, `/contracts`.

## Commands

Add run, test and lint commands here once the FastAPI service is scaffolded.
