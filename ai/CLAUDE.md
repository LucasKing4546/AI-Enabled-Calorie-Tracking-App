# /ai (Roles 3 & 4: FastAPI AI service)

One Python FastAPI service, one router per subfolder. Each router starts as a stub returning fixed sample JSON that matches `/contracts/ai`, then gets the real model.

| Subfolder | Owner | Endpoint(s) |
|---|---|---|
| `meal/` | Role 3 (Sebi) | `POST /ai/meal/photo`, `POST /ai/meal/text` |
| `label/` | Role 4 (Lucas) | `POST /ai/label` |
| `nutrition/` | Role 4 (Lucas) | FDC food search/lookup + macro calculator (internal, called by `meal/`) |

## Model hosting

No paid APIs. One open vision-language model (handles photos and text) runs in **LM Studio on Sebi's laptop**, shared over **Tailscale**:

```
Phone app ──> AI service :8000 ──> LM Studio :1234 on Sebi's laptop (over Tailscale)
```

- Sebi: LM Studio → Developer → start server on port 1234 with "Serve on Local Network" on. Allow inbound 1234 and 8000 in the Windows firewall.
- Call it with the `openai` Python client: `OpenAI(base_url=AI_BASE_URL, api_key="lm-studio")`. Images go in as base64 `data:` URLs in an `image_url` content part. Use `response_format` with a JSON schema from `/contracts/ai` to force valid output.
- Run the service with `uvicorn ... --host 0.0.0.0 --port 8000` so phones on the tailnet can reach it.
- The model is only up when Sebi's laptop is on. Unit tests and the app use the stubs, never the live model.

Each role edits only its own subfolders. Shared service setup (app entrypoint, config, dependencies) changes via a PR the other AI role reviews.

## meal/ (Role 3, Sebi)

Pipeline: vision-language model → foods + estimated grams → match each via `nutrition/` (not FDC directly) → per-item confidence → list of ingredient items. When matching is uncertain, return the top 3 candidates. Owns the meal test set (photos with weighed portions); report gram and kcal error.

## label/ (Role 4, Lucas)

OCR the nutrition table → LLM structures it into `per_100g`, `per_serving`, `serving_grams` → list unreadable fields → check kcal ≈ 4·protein + 4·carbs + 9·fat. Owns the label test set (blurry, curved, non-English); report field accuracy.

## nutrition/ (Role 4, Lucas)

FoodData Central search and lookup, used by `meal/`. Cache results; model and API costs have a monthly budget cap.

## May not touch

`/app`, `/supabase`, `/contracts`.

## Commands

Add run, test and lint commands here once the FastAPI service is scaffolded.
