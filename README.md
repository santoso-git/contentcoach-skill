# ContentCoach — an image and video skill for your AI agent

**Homepage, with examples: https://santoso-git.github.io/contentcoach-skill/**

A skill that makes images and short videos with several models — Nano Banana 2,
GPT Image 2.5 and Grok Imagine for images; Kling 3.0, MiniMax H3, Grok Video and
Seedance 2.5 for video — straight from Cursor, Codex, Claude Code or any agent
that can run `curl`. No accounts with the image providers, no API keys of theirs: just one
personal ContentCoach key.

## 1. Set your key

You get a key from whoever shared this with you. Put it in your shell profile
(`~/.zshrc` on a Mac, `~/.bashrc` on Linux) — not in a project, and never in
git:

```bash
export CONTENTCOACH_KEY="your-key"
```

Open a new terminal, and restart your editor or agent so it picks the variable
up.

The key has a daily budget in US dollars that resets at midnight Stockholm
time. Video costs roughly ten times an image, so the skill quotes every clip
and waits for your yes before it runs.

## 2. Use it — no install needed

Tell your agent:

> Read https://raw.githubusercontent.com/santoso-git/contentcoach-skill/main/contentcoach/SKILL.md and make a 16:9 image of a
> coffee cup on a windowsill in morning light.

It reads the instructions and does the rest. The image lands in
`generations/` in the project you have open, with a `.json` beside it
recording the prompt, model and cost.

## 3. Or install it as a skill

Installed, the agent knows the tool exists and you never need the link again —
*"make a blog header about remote work"* is enough.

The skill is one file, [`contentcoach/SKILL.md`](contentcoach/SKILL.md). The
simplest install is to ask your agent:

> Install the skill from https://github.com/santoso-git/contentcoach-skill

or run the command for your tool yourself.

### Cursor

```bash
mkdir -p ~/.cursor/skills/contentcoach
curl -fsSL https://raw.githubusercontent.com/santoso-git/contentcoach-skill/main/contentcoach/SKILL.md -o ~/.cursor/skills/contentcoach/SKILL.md
```

Restart Cursor. The skill is used by Agent mode.

### Codex

```bash
mkdir -p ~/.agents/skills/contentcoach
curl -fsSL https://raw.githubusercontent.com/santoso-git/contentcoach-skill/main/contentcoach/SKILL.md -o ~/.agents/skills/contentcoach/SKILL.md
```

Restart Codex. The skill calls the ContentCoach API over the network, so Codex
must be allowed network access — approve the `curl` calls when asked, or run
with a sandbox mode that permits network.

### Claude Code

```bash
mkdir -p ~/.claude/skills/contentcoach
curl -fsSL https://raw.githubusercontent.com/santoso-git/contentcoach-skill/main/contentcoach/SKILL.md -o ~/.claude/skills/contentcoach/SKILL.md
```

Start a new session. Ask for an image in plain words, or type `/contentcoach`.

### Updating

Run the same command again. The file the app serves is always the current
version.

## Tips

- **Logos, products, faces:** give the agent the file and ask it to use it as a
  reference. A logo described in words comes out wrong every time.
- **Editing an image:** give the agent the image and say what to change —
  *"swap the background for a bright studio, keep everything else"*.
- **Text inside the image** (signs, packaging, posters): ask for GPT Image 2.5.
- **Video:** ask it to animate an image you already like — *"animate that, a slow
  push-in, five seconds"*. It tells you the price first.
- **Draft first:** everything is made at 1K by default. Ask for 2K or 4K once
  you have a favourite.
- **Budget:** ask the agent how much is left today — it checks for free.

## What goes where

Your prompt and any reference images are sent to ContentCoach, which passes
them on to an image provider (Kie AI, fal.ai, WaveSpeed or Google). Reference
images are stored at a public address so the provider can fetch them — don't
upload anything you would not want publicly reachable. Generated images are
not kept by ContentCoach: the link to the result expires within hours, which
is why the agent downloads the file straight away.
