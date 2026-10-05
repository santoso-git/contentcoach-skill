# contentcoach-skill

The public ContentCoach skill and its homepage. Public repository — anything
committed here is published.

## Layout

- `contentcoach/SKILL.md` and `contentcoach/models/*.md` — the skill and one
  recipe per model. **These files are the source**; they are not generated from
  anywhere. Installs clone or fetch them raw from `main`, so a push here is a
  release to everyone who updates.
- `docs/` — the homepage at https://skill.contentcoach.se/, served by GitHub
  Pages from `docs/` on `main`. `docs/CNAME` holds the domain; keep it. The old
  github.io address redirects there.
  Static, no build step, fonts self-hosted in `docs/fonts/`.
- `docs/examples/` — real generations with a `.json` sidecar each. Never show an
  image without its sidecar, and never caption it with words the prompt did not say.
- `LICENSE` — MIT, for the skill, recipes and homepage code. The logo, the
  fonts (`docs/fonts/OFL.txt`) and the example generations are outside it; the
  README's License section says so.
- `PRODUCT.md`, `DESIGN.md`, `.impeccable/` — design context for the homepage,
  **local only**: git-ignored and not on GitHub, since they are not part of the
  skill. Change the page through the `impeccable` skill, which reads them here.

## The boundary

The skill calls **Kie AI** and **fal.ai** directly, with keys the user creates
and pays for themselves (`KIE_API_KEY`, `FAL_KEY`). It has nothing to do with the
ContentCoach web app or its API at create.contentcoach.se: no shared keys, no
shared code, no calls. Users are not guests of anything; they are customers of
the providers. When a provider changes a model id, a field or a price, fix the
recipe in `contentcoach/models/` from a real run.

## Working here

- **Local only, git-ignored:** `.env` (copies of `KIE_API_KEY` and `FAL_KEY`
  from the app repo, for test runs: `set -a; source .env; set +a`) and
  `generations/` (test outputs with sidecars). Never commit either.
- **Pushing is a release.** Santoso approves each push explicitly; commit
  locally and ask.
- **Route tests are run by the "ContentCoach app" session** (the contentcoach
  repo), with Santoso's yes per paid run. It sends results here as commit id,
  file paths, call (endpoint, id, fields), measured price (actual or estimated)
  and size/length. Its open backlog is in
  `~/.claude/projects/-Users-santoso-Documents-contentcoach/memory/route-test-backlog.md`.
- **Adapting a recipe from the app** (`skills/generate/models/`): keep only Kie
  and fal, drop private paths, `.env`, the web app and WaveSpeed/Google, read
  keys from the user's shell, save to `generations/` in the user's project,
  write prices as `0.08 USD`. Nano Banana has one combined recipe here.
- **Prices live in three places that do not update each other:** the recipe,
  the cost table in `SKILL.md`, and the price-list dialog in `docs/index.html`
  (ticked box = measured). Change all three together.
- **Every user-facing change gets a dated entry** in `CHANGELOG.md` and in the
  "What changed" log at the bottom of `docs/index.html`, and moves the dates on
  the homepage: the stamp ("Updated"), the log's "Last updated" line and the
  price list's docket. The homepage log keeps short entries; CHANGELOG.md has
  the full ones.
- **Homepage examples:** Nos. 1–8 were made through the app on 26 Sep; Nos. 9
  and 10 with this skill's own recipe on 27 Sep. Update the pocket total ("9
  frames and a clip · 0.62 USD") and the intro count when adding one.
- GitHub settings (not in the repo): `main` is protected against force-push and
  deletion, the wiki is off, the description and homepage field name
  skill.contentcoach.se and the user's own Kie/fal key.

## Rules

- Never commit a key. The skill reads `KIE_API_KEY` and `FAL_KEY` from the environment.
- In the skill files, write prices as `0.08 USD`: a dollar sign followed by a digit is
  substituted away when Claude Code loads a skill.
- Video costs roughly ten times an image. The skill must keep the rule that the
  agent quotes every clip and waits for an explicit yes.
