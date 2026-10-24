# AI-Enabled Calorie Tracking App

AI calorie and macro tracker: photograph a meal, scan a barcode or a nutrition label, edit the detected ingredients, log it. Built by a 4-person team. See `CLAUDE.md` for the architecture, roles and contracts, and `docs/STATUS.md` for progress.

## Team setup (once per machine)

The repo ships the shared Claude Code config in `.claude/settings.json`. When you first open the repo in Claude Code and trust the folder, it will prompt you to install:

- **ecc** (marketplace `affaan-m/ECC`)
- **token-optimizer** (marketplace `alexgreensh/token-optimizer`)

Accept both. If the prompt doesn't appear, run `/plugin` and install them from there.

**graphify** is a Python CLI, not a plugin, so install it yourself:

```sh
pip install graphifyy
graphify install        # adds the /graphify skill to Claude Code
graphify update .       # build the code graph (graphify-out/, git-ignored)
```

The project hooks call `graphify` from your PATH.

**Tailscale:** the AI model runs in LM Studio on Sebi's laptop. Install [Tailscale](https://tailscale.com/download) on your laptop and phone and accept Sebi's invite to the tailnet. Then copy `ai/.env.example` and `app/.env.example` to `.env` and fill in Sebi's Tailscale host name.

## Starting a Claude session

Paste this, plus your role block from the dev plan, then the latest `docs/STATUS.md`:

> You're working on a 4-person team project: an AI calorie and macro tracking app. Read CLAUDE.md at the repo root, then the CLAUDE.md in my folder, then everything in /contracts. I own only my folder. Do not edit other folders or /contracts; if a contract needs to change, stop and write me a short proposal I can raise with the team. Code against /contracts, and use the mocks for anything my teammates haven't shipped yet. Work in small steps on a feature branch, with tests, and tell me what to put in docs/STATUS.md when we merge.
