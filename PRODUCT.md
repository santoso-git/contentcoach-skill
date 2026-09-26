# Product

<!-- impeccable:product-schema 1 -->

## Platform

web

## Stack

The skill is a folder: `contentcoach/SKILL.md` plus one Markdown recipe per model
in `contentcoach/models/`. It calls Kie AI and fal.ai directly over HTTP with the
user's own keys; there is no ContentCoach server in between. The homepage is
static HTML/CSS with no build step, served by GitHub Pages from `docs/`.
Delegated: the owner asked for "static and very simple"; plain files were chosen
because Pages serves them with no toolchain.

## Users

- **Owner (Santoso):** publishes the skill and its homepage. Hands out nothing:
  there are no ContentCoach keys.
- **Users:** people who work in an AI coding agent — Cursor, Codex or Claude
  Code — and want images and video without learning a tool. They create their
  own key at Kie AI and/or fal.ai, pay those providers directly, and install the
  skill on their own machine. Nobody is a guest of anything.

## Product Purpose

Recipes that let an agent call several image and video models (Nano Banana 2,
GPT Image 2.5, Grok Imagine 2.0, Kling 3.0, MiniMax H3, Grok Video 1.5, Seedance
2.5) on Kie AI and fal.ai correctly the first time — right ids, right fields,
cheapest route, known prices. Success: from reading the page to a saved image in
their project in a few minutes, most of it spent creating a provider key.

## Positioning

The agent does the work: there is no app to learn. The user either points their
agent at the skill file's URL or installs the skill folder, and asks for a picture
in plain words. Cheapest-first routing between Kie and fal, and a price quote
before every clip, sit behind that.

## Operating Context

Users read the homepage in a browser, create a key at Kie AI or fal.ai, then copy
a command into a terminal or a sentence into an agent chat. Results land as files
in `generations/` in their open project, each with a JSON sidecar.

## Capabilities and Constraints

- Images and video. The skill quotes every clip and waits for a yes, and quotes
  before anything at 2K or 4K. There is no daily cap: the user's own provider
  balance is the limit.
- Keys are the user's own, from Kie AI or fal.ai. One is enough; both give a
  fallback.
- Reference images for Kie go to Kie's temporary file storage (deleted within
  days); fal receives them inline.
- Nothing passes through ContentCoach. Result links expire in hours, so the skill
  downloads straight away.

## Brand Commitments

- Name: ContentCoach. Logo: `assets/contentcoach-logo.png`.
- The web app's own palette is near-neutral with one gold (`#e6b84f`) reserved
  for the action that spends money. Inferred, not confirmed as binding for the
  skill homepage.
- Public-facing copy for the skill is in English; the owner works in Swedish.

## Evidence on Hand

- Six example images made for the homepage on 26 Sep 2026, with prompt
  sidecars, in `docs/examples/`. A seventh, an edit, failed and
  was not shipped. They are real generations with
  the same models and providers the skill calls, not stock.
- No testimonials, customer names, usage figures or benchmarks exist. Do not
  invent them.

## Product Principles

1. Show real output, never describe it.
2. Your own key, one sentence: setup must stay copy-paste short.
3. Say what things cost and where data goes, plainly.
4. Pass real files for logos and faces; never approximate a brand in words.
