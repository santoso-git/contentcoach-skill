# contentcoach-skill

The public ContentCoach skill and its homepage. Public repository — anything
committed here is published.

## Layout

- `contentcoach/SKILL.md` and `contentcoach/models/*.md` — the skill and one
  recipe per model. **These files are the source**; they are not generated from
  anywhere. Installs clone or fetch them raw from `main`, so a push here is a
  release to everyone who updates.
- `docs/` — the homepage, served by GitHub Pages from `docs/` on `main`.
  Static, no build step, fonts self-hosted in `docs/fonts/`.
- `docs/examples/` — real generations with a `.json` sidecar each. Never show an
  image without its sidecar, and never caption it with words the prompt did not say.
- `LICENSE` — MIT, for the skill, recipes and homepage code. The logo, the
  fonts (`docs/fonts/OFL.txt`) and the example generations are outside it; the
  README's License section says so.
- `PRODUCT.md`, `DESIGN.md`, `.impeccable/` — design context for the homepage.
  Change the page through the `impeccable` skill.

## The boundary

The skill calls **Kie AI** and **fal.ai** directly, with keys the user creates
and pays for themselves (`KIE_API_KEY`, `FAL_KEY`). It has nothing to do with the
ContentCoach web app or its API at create.contentcoach.se: no shared keys, no
shared code, no calls. Users are not guests of anything; they are customers of
the providers. When a provider changes a model id, a field or a price, fix the
recipe in `contentcoach/models/` from a real run.

## Rules

- Never commit a key. The skill reads `KIE_API_KEY` and `FAL_KEY` from the environment.
- In the skill files, write prices as `0.08 USD`: a dollar sign followed by a digit is
  substituted away when Claude Code loads a skill.
- Video costs roughly ten times an image. The skill must keep the rule that the
  agent quotes every clip and waits for an explicit yes.
