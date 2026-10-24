# /contracts

The shared source of truth for all four roles. **Read-only for every Claude session.** If a change is needed, write a proposal for the user to raise with the team. Changes merge only through a PR approved by all four.

| Path | Contents | Owner |
|---|---|---|
| `db/` | SQL migrations for every table | Role 2 |
| `ai/` | JSON schemas: ingredient item, label result | Roles 3 & 4 |
| `openapi.yaml` | AI endpoints + `GET /products/barcode/{code}` | All |
| `ts/` | Shared TypeScript types + the single macro function (`per_100g × grams / 100`) | All |

The mock server (role 2) and the AI service are checked against `openapi.yaml` in CI. If you change the spec, both must change in the same PR.
