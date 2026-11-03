# G3 Ads automation

Runbook-driven Meta ads launches for **G3 Pro Tint** (Setapak and Setia Alam), built from Google Drive creatives.
Everything runs through the connected MCP tools (`mcp__Google_Drive__*`, `mcp__META__*`). There is no code to build and no API tokens.

- Launch workflow: `.claude/skills/launch-meta-ads/SKILL.md`. Use it for any "launch / upload ads / new campaign" request.
- Config: `config/accounts.json` (ad accounts and Pages), `config/defaults.json` (objective, Broad ad set, locales, naming), `config/cities.json` (city targeting presets).
- Copy format: `templates/copy-template.md`.
- Launch history: `launches/`.

## Rules
1. Never publish or activate anything without an explicit OK from the user in the current conversation. Two stop points: after the plan table, and after the draft and previews.
2. Ask for daily budget (RM) and location (Malaysia or a city) on every launch. Never reuse them silently.
3. Andromeda setup: 1 CBO campaign → 1 ad set named `Broad` → up to 10 ads (one per pic/video). Advantage+ audience and placements on. No interests.
4. Use ad copy from the Drive copy file exactly as written.
5. Only the two G3 ad accounts in `config/accounts.json`.
