# ContentCoach — an image and video skill for your AI agent

**Homepage, with examples: https://santoso-git.github.io/contentcoach-skill/**

A skill that makes images and short videos with several models — Nano Banana 2,
GPT Image 2.5 and Grok Imagine for images; Kling 3.0, MiniMax H3, Grok Video and
Seedance 2.5 for video — straight from Cursor, Codex, Claude Code or any agent
that can run `curl`. It calls the providers **Kie AI** and **fal.ai** directly
with your own API keys. You pay them directly; nothing goes through anyone else.

## 1. Get a key

One is enough. With both, the skill falls back from one to the other.

- **Kie AI** — cheapest for nearly every model. Create a key at
  https://kie.ai/api-key and top up some credits.
- **fal.ai** — the fallback, plus a few things only fal does (a seed on Nano
  Banana, Grok Imagine edits, MiniMax H3 at 480P). Create a key at
  https://fal.ai/dashboard/keys and add a payment method.

Put them in your shell profile (`~/.zshrc` on a Mac, `~/.bashrc` on Linux) —
not in a project, and never in git:

```bash
export KIE_API_KEY="your-kie-key"
export FAL_KEY="your-fal-key"
```

Open a new terminal, and restart your editor or agent so it picks them up.

## 2. Use it — no install needed

Tell your agent:

> Read https://raw.githubusercontent.com/santoso-git/contentcoach-skill/main/contentcoach/SKILL.md and make a 16:9 image of a
> coffee cup on a windowsill in morning light.

It reads the instructions and the recipe for the model it picks, and does the
rest. The image lands in `generations/` in the project you have open, with a
`.json` beside it recording the prompt, model, provider and cost.

## 3. Or install it as a skill

Installed, the agent knows the tool exists and you never need the link again —
*"make a blog header about remote work"* is enough.

The skill is a folder: [`contentcoach/SKILL.md`](contentcoach/SKILL.md) and one
recipe per model in [`contentcoach/models/`](contentcoach/models/). Clone it
once and link it into your agent's skills folder:

```bash
git clone --depth 1 https://github.com/santoso-git/contentcoach-skill ~/.contentcoach-skill
```

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

## What it costs

You pay Kie and fal at their own prices. A draft image is 0.03–0.08 USD; a
five-second video clip is 0.06–2.37 USD depending on model and provider. **Video
costs roughly ten times an image, so the skill quotes every clip and waits for
your yes before it runs**, and it quotes before anything at 2K or 4K. The full
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
temporary file storage, which deletes them within days; fal receives them inline.
Results are downloaded into your project straight away, because the providers'
links expire within hours. Your keys stay in your shell; the skill never writes
them anywhere.
