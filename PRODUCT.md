# Product

<!-- impeccable:product-schema 1 -->

## Platform

web

## Stack

The skill is one Markdown file, `contentcoach/SKILL.md`. The homepage is static
HTML/CSS with no build step, served by GitHub Pages from `docs/`. Delegated: the
owner asked for "static and very simple"; plain files were chosen because Pages
serves them with no toolchain. The service the skill calls is a separate
repository and is not described here.

## Users

- **Owner (Santoso):** hands out keys and runs the ContentCoach service
  (create.contentcoach.se) that this skill calls. The service lives in its own
  repository; this one holds only the skill and its homepage.
- **Invited guests:** people the owner hands a personal key. They work in an AI
  coding agent — Cursor, Codex or Claude Code — and want images without signing
  up to Kie, fal or any other provider. They arrive at the skill homepage from a
  link the owner sent, key already in hand or on its way.

## Product Purpose

One HTTP API in front of several image models (Nano Banana 2, GPT Image 2.5, Grok
Imagine 2.0), so an agent can generate images with a single personal key. The
provider keys never leave the server. Success for a guest: from reading the page
to a saved image in their project in a couple of minutes.

## Positioning

The agent does the work: there is no app to learn. The guest either points their
agent at the skill file's URL or installs that one file, and asks for a picture in
plain words. Cheapest-first provider routing and cost caps sit behind that.

## Operating Context

Guests read the homepage in a browser, then copy a command into a terminal or a
sentence into an agent chat. Results land as files in `generations/` in their
open project, each with a JSON sidecar. Keys are handed out personally.

## Capabilities and Constraints

- Keys make images and video (video since 26 Sep 2026; the skill quotes every
  clip and waits for a yes), with a daily dollar cap that
  resets at midnight Stockholm time.
- Keys are **invitation only**: the homepage offers no sign-up and no contact
  route (owner's decision, 26 Sep 2026).
- Reference images uploaded by guests are stored at a public address.
- Generated images are not kept by ContentCoach; the result link expires in hours.

## Brand Commitments

- Name: ContentCoach. Logo: `assets/contentcoach-logo.png`.
- The web app's own palette is near-neutral with one gold (`#e6b84f`) reserved
  for the action that spends money. Inferred, not confirmed as binding for the
  skill homepage.
- Public-facing copy for the skill is in English; the owner works in Swedish.

## Evidence on Hand

- Six example images made for the homepage on 26 Sep 2026, with prompt
  sidecars, in `docs/examples/`. A seventh, an edit, failed and
  was not shipped. They are real generations from
  this product, not stock.
- No testimonials, customer names, usage figures or benchmarks exist. Do not
  invent them.

## Product Principles

1. Show real output, never describe it.
2. One key, one sentence: setup must stay copy-paste short.
3. Say what things cost and where data goes, plainly.
4. Pass real files for logos and faces; never approximate a brand in words.
