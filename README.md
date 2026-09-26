# ContentCoach — an image and video skill for your AI agent

**Homepage, with examples: https://skill.contentcoach.se/**

A skill that makes images and short videos with several models — Nano Banana 2,
GPT Image 2.5 and Grok Imagine for images; Kling 3.0, MiniMax H3, Grok Video and
Seedance 2.5 for video — straight from Cursor, Codex, Claude Code or any agent
that can run `curl`. It calls the providers **Kie AI** and **fal.ai** directly
with your own API keys. You pay them directly; nothing goes through anyone else.

**Instead of a generation app.** You don't need a subscription to Higgsfield,
Runway or a similar app. The skill runs the same kind of models through your own
Kie AI or fal.ai account, and you pay per image or clip at the provider's price.

**Alongside your other skills.** Your agent can use it in the same task as its
other skills. A design skill builds a landing page, and this one makes the hero
image, the product shots and a short clip for it, saved straight into the project.

## Short version: give this to your agent

> Install the ContentCoach skill from https://github.com/santoso-git/contentcoach-skill: clone it to ~/.contentcoach-skill and link its contentcoach folder into your skills folder. Then check whether KIE_API_KEY or FAL_KEY is set in my shell, without showing the value. If neither is, tell me where to create one and how to add it to my shell profile. Never ask me to paste a key into the chat.

Paste it into Claude Code, Cursor or Codex. The sections below do the same by
hand.

## Things to ask for

- *Make a 16:9 blog header about remote work: a small desk in a Nordic cabin,
  rain outside.*
- *Animate that image: a slow push-in, rain running down the window. Five
  seconds.*
- *Put our logo from logo.png on a square post announcing the autumn menu, with
  the words "Autumn menu" readable.*
- *Build a landing page for my bakery with your design skill, and make the hero
  image and three product shots for it with ContentCoach.*

## 1. Get a key

One is enough. With both, the skill falls back from one to the other.

- **Kie AI** — cheapest for nearly every model. Create a key at
  https://kie.ai/api-key and top up some credits.
- **fal.ai** — the fallback, plus a few things only fal does (a seed on Nano
  Banana, Grok Imagine edits, MiniMax H3 at 480P or 4K). Create a key at
  https://fal.ai/dashboard/keys and add a payment method.

Put them in your shell profile (`~/.zshrc` on a Mac, `~/.bashrc` on Linux) —
not in a project, and never in git:

```bash
export KIE_API_KEY="your-kie-key"
export FAL_KEY="your-fal-key"
```

Open a new terminal, and restart your editor or agent so it picks them up.

The skill runs `curl` and `jq`. Both come with most systems; if `jq` is
missing, install it (`brew install jq` on a Mac, your package manager on
Linux).

## 2. Use it — no install needed

Tell your agent:

> Read https://raw.githubusercontent.com/santoso-git/contentcoach-skill/main/contentcoach/SKILL.md and make a 16:9 image of a
> coffee cup on a windowsill in morning light.

It reads the instructions and the recipe for the model it picks, and does the
rest. The image lands in `generations/` in the project you have open, with a
`.json` beside it recording the prompt, model, provider and cost.

In Codex, allow network access for the `curl` calls.

## 3. Or install it as a skill

Installed, the agent knows the tool exists and you never need the link again —
*"make a blog header about remote work"* is enough.

The skill is a folder: [`contentcoach/SKILL.md`](contentcoach/SKILL.md) and one
recipe per model in [`contentcoach/models/`](contentcoach/models/). Clone it
once and link it into your agent's skills folder:

```bash
git clone --depth 1 https://github.com/santoso-git/contentcoach-skill ~/.contentcoach-skill
```

If `~/.contentcoach-skill` already exists, you have it — run the update command
below instead. The same goes for the link: if it exists, leave it.

Then, for your tool:

| Tool | Link | Then |
|---|---|---|
| Claude Code | `mkdir -p ~/.claude/skills && ln -s ~/.contentcoach-skill/contentcoach ~/.claude/skills/contentcoach` | Start a new session. Ask in plain words, or type `/contentcoach`. |
| Cursor | `mkdir -p ~/.cursor/skills && ln -s ~/.contentcoach-skill/contentcoach ~/.cursor/skills/contentcoach` | Restart Cursor. The skill is used by Agent mode. |
| Codex | `mkdir -p ~/.agents/skills && ln -s ~/.contentcoach-skill/contentcoach ~/.agents/skills/contentcoach` | Restart Codex, and allow network access for the `curl` calls. |

Or ask your agent: *"Install the skill from
https://github.com/santoso-git/contentcoach-skill"*.

### Updating

```bash
git -C ~/.contentcoach-skill pull
```

The recipes are kept up to date. Models and prices change every month: when a
provider renames a model or changes a field or a price, the recipe is fixed, and
new models get a recipe of their own as they come out. Pull to get them. The
no-install route always reads the latest version.

## What it costs

You pay Kie and fal at their own prices. A draft image is 0.02–0.08 USD; a
five-second video clip is 0.06–2.37 USD depending on model and provider. **Video
costs roughly ten times an image, so the skill quotes every clip and waits for
your yes before it runs**, and it does the same before anything at 2K or 4K. The full
table is in [`SKILL.md`](contentcoach/SKILL.md).

## Tips

- **Logos, products, faces:** give the agent the file and ask it to use it as a
  reference. A logo described in words comes out wrong every time.
- **Editing an image:** give the agent the image and say what to change —
  *"swap the background for a bright studio, keep everything else"*.
- **Text inside the image** (signs, packaging, posters): ask for GPT Image 2.5.
- **Video:** ask it to animate an image you already like — *"animate that, a
  slow push-in, five seconds"*.
- **Draft first:** everything is made at 1K by default. Ask for 2K or 4K once
  you have a favourite.

## What goes where

Your prompt and any reference images go straight from your machine to Kie AI or
fal.ai, under your own account. For Kie, reference images are uploaded to Kie's
temporary file storage, which deletes them after about a day; fal receives them inline.
Results are downloaded into your project straight away, because the providers'
links expire within hours. Your keys stay in your shell; the skill never writes
them anywhere.

## License

The skill, its recipes and the homepage code are released under the
[MIT License](LICENSE).

Not covered by that license:

- **The ContentCoach name and logo** (`docs/contentcoach-logo.png`). Please don't
  use them to present your own fork as this project.
- **The fonts** in `docs/fonts/` — Sofia Sans, Sofia Sans Extra Condensed and
  JetBrains Mono, under the [SIL Open Font License 1.1](docs/fonts/OFL.txt).
- **The example images and clip** in `docs/examples/`. They were generated with
  the AI models named in their `.json` sidecars and are shown as examples of
  output; reuse them at your own discretion.

ContentCoach is not affiliated with or endorsed by Kie AI, fal.ai, Google,
OpenAI, xAI, Kuaishou (Kling), MiniMax, ByteDance, Higgsfield or Runway. Model
and product names belong to their owners. You use the providers under their
own terms and pay them directly; the skill quotes prices from their pages and
real runs, but the provider's bill is what counts.
