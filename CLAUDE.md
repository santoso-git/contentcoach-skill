# contentcoach-skill

The public ContentCoach skill and its homepage. Public repository — anything
committed here is published.

## Layout

- `contentcoach/SKILL.md` — the skill. **This file is the source**; it is not
  generated from anywhere. Installs fetch it raw from `main`, so a push here is a
  release to everyone who reinstalls.
- `docs/` — the homepage, served by GitHub Pages from `docs/` on `main`.
  Static, no build step, fonts self-hosted in `docs/fonts/`.
- `docs/examples/` — real generations with a `.json` sidecar each. Never show an
  image without its sidecar, and never caption it with words the prompt did not say.
- `PRODUCT.md`, `DESIGN.md`, `.impeccable/` — design context for the homepage.
  Change the page through the `impeccable` skill.

## The boundary

The skill calls the ContentCoach API at `https://create.contentcoach.se/api/*`
(`me`, `upload`, `generate`, `status`). That API lives in a **separate, private
repository** and is the only thing the two share. Nothing from the app belongs
here, and nothing here belongs in the app. When the API changes, update
SKILL.md here in the same breath.

## Rules

- Never commit a key. The skill reads `CONTENTCOACH_KEY` from the environment.
- In SKILL.md, write prices as `0.08 USD`: a dollar sign followed by a digit is
  substituted away when Claude Code loads a skill.
- Video costs roughly ten times an image. SKILL.md must keep the rule that the
  agent quotes every clip and waits for an explicit yes.
