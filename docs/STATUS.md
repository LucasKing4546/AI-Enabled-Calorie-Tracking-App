# Status

Update this when you merge. Paste it into your Claude session at the start of each work block.
Newest entries first. Format: `YYYY-MM-DD · role · landed / contract change / blocked`.

## Phase 0 checklist

- [x] Monorepo with `/app`, `/supabase`, `/ai`, `/contracts`, `/docs`
- [x] Root `CLAUDE.md` and one per folder
- [ ] DB schema, AI output JSON schemas and `openapi.yaml` agreed and merged
- [ ] Mock API and AI stubs returning fixed JSON
- [ ] CI and branch protection on
- [ ] Everyone runs the app on their phone against the mocks

## Open decisions (kickoff)

- [ ] Confirm the stack (or swap Supabase for our own backend)
- [x] Assign the four roles: Mihai (1, app), Kevin (2, backend), Sebi (3, meal AI), Lucas (4, label AI & nutrition)
- [x] Model hosting: no paid APIs. LM Studio on Sebi's laptop, shared over Tailscale (see `ai/CLAUDE.md`)
- [ ] Pick the model (proposal: Qwen2.5-VL-7B-Instruct or newer Qwen-VL, one model for photos + text)
- [ ] Map the phases onto the A1–A5 deadlines

## Log

- 2026-10-24 · all · Repo initialised with CLAUDE.md files and shared Claude Code plugin config.
