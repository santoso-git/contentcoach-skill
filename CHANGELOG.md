# Changelog

Changes to the skill, its recipes and its prices, newest first. Prices are in
USD and come from real runs unless marked as listed. To get the latest, run
`git -C ~/.contentcoach-skill pull`; the no-install route always reads `main`.

## 9 Oct 2026

- **Tested with a small agent model.** Claude Haiku, given only `SKILL.md` and
  the recipes, ran four requests end to end (three images, 0.12 USD, and one
  `prompt`). Two rules were added first, from the app's own Haiku tests: a
  reference image is either *edit this picture* or *put this person/product in
  a new scene* — the latter must open "Using the person (or product) in the
  reference image" — and every part of the brief must be in the prompt, with
  counts matching ("six arms" gets six jobs). Haiku followed both, invented no
  text and wrote a five-shot list. One gap it found is fixed: with a product
  whose label is part of the reference, keep the label and add no other text.

- **The docs are written for agents to read.** README gains *For agents: read
  the docs before you run* — which file holds what, how to read "confirmed",
  "measured", "listed" and "unrun", and that a run beats the docs. `SKILL.md`
  says the same under *Models*. Seedance 2.0 Mini's recipe compares it with
  2.5 (cheap and quick, less lifelike), and MiniMax H3 marks Kie's
  reference-to-video as not yet run.

- **No invented text.** When the user gave no words for the picture, a
  written-out prompt now ends with "No text, signs, labels or logos anywhere
  in the image." In a before/after test, Nano Banana 2 turned "the hero image
  for a landing page for my bakery" into a web page with a made-up bakery
  name, headline and button. A hero image is a photograph with room for a
  headline, never a page layout. In `SKILL.md`, *Writing the prompt*, and the
  Nano Banana 2 and 2.1 recipes.

## 8 Oct 2026

- **Prompt writing is opt-in.** By default the user's words are the prompt.
  The first time a short ask arrives, the agent asks once whether to write it
  out — yes this time, yes always, or no — and never again after a no; it then
  points to `prompt` and `auto on`. `auto on` / `auto off` save the choice in
  `generations/.contentcoach.json` in the project.
- **Commands.** Start a message with `prompt <brief>` (after `/contentcoach`
  in Claude Code) and the agent writes the full prompt and names the model,
  provider and price — **without running anything**. Edit it, then say run.
  Also `models`, `prices`, `balance` and `help`.
- **New model: Nano Banana 2.1**, Google's successor to Nano Banana 2 (released
  6 Oct). Same body under a new id: `nano-banana-2-1` on Kie AI,
  `google/nano-banana-2.1` on fal.ai. Measured on Kie at **0.02 USD for 1K**
  (2K 0.03 and 4K 0.045 listed), half Nano Banana 2's price there; 0.08 USD at
  1K on fal, which also takes a seed. Nano Banana 2 stays the default until the
  two have been compared side by side. Editing with references is not yet run.

## 7 Oct 2026

From a first run by an agent that knew only `SKILL.md` and the recipes:

- **Your agent can write the prompt.** A new section in `SKILL.md`, *Writing
  the prompt*, and a *How to prompt it* section in every recipe, from each
  model's own guide. A short ask ("a blog header about remote work") can be
  written out — subject, light, camera, motion, sound, a timed shot list for a
  short story — by the agent you already use: no extra model, call or key. The
  prompt is shown before anything paid runs, and the quote for a clip includes
  it. A finished prompt, pasted or marked "exactly", is sent untouched.
- **Full prompts on the homepage.** Every example has a "Full prompt" button and
  a "Copy prompt" button, so you can paste the same prompt into your agent.
- **New homepage example, No. 11:** five shots in one 15-second Seedance 2.0
  Mini clip from a single prompt with a timed shot list (720p, sound, text only,
  0.615 USD), with one frame per shot beside it.
- **Seedance 2.0 Mini keeps its price:** 0.21 USD for 5 seconds at 720p,
  measured again on 7 Oct, and Kie no longer calls it a discount. The recipe
  gains a text-to-video body and a section on **several shots in one clip**: up
  to 15 seconds, a timed shot list in one prompt plays in order, with no
  stitching (measured: 15 s at 720p with sound, 0.615 USD).
- **Safer job steps in `SKILL.md`:**
  - Each job is named once (`NAME`), and its temporary files are
    `/tmp/cc-$NAME.*`, so two agents on one machine no longer overwrite each
    other's request or result.
  - A failed submit stops at once instead of polling an empty task id.
  - A poll that is not finished says "run this block again".
  - The saved file takes its extension from the result URL, since Kie returns a
    JPEG for Grok Imagine 2.0.
  - Kie's cost is rounded.
  - `creditsConsumed` reads 0 while a job waits; it is not a free run.
- **A sidecar template**, built with `jq -n --arg`, with what goes in each
  field.
- Rule 2 says outright that a single 1K draft needs no quote.
- Grok Imagine 2.0 lists Kie's aspect ratios (`1:1 2:3 3:2 16:9 9:16`).
- `providers.md` gives the catalogue lookup as one pattern for every model id.

## 4 Oct 2026

- **New model: Seedance 2.0 Mini** (Kie AI). 4–15 seconds at 480p or 720p,
  with sound. Measured: 0.21 USD for 5 seconds at 720p. Kie calls this price a
  discount that ends on 7 Oct 2026 at 06:00 UTC, so it may rise after that.
- **MiniMax H3 now starts on Kie AI.** 768P costs 0.20 USD for 5 seconds
  (measured), half fal's price at the sizes both providers offer. fal is used
  only for 480P, 4K or a seed.
- **MiniMax H3 always returns sound**, on both providers. Neither has a switch
  for it, and it is not billed separately.
- Kie's upload host is `kieai.redpandaai.co`; uploaded files are kept for one
  to three days.

## 2 Oct 2026

- **Free catalogue lookups.** Between updates, the skill can check a
  provider's current price and fields in Kie AI's and fal.ai's free model
  catalogues: for a renamed id, a rejected field, a stale price or a model
  without a recipe.

## 27 Sep 2026

- **New model: GPT Image 2.5 Sunburst**, the slower variant with finer detail.
  Measured on Kie at 0.03 USD for 1K; a transparent background works.
- **GPT Image 2.5 now starts on fal.ai**, the cheaper route at every size:
  0.0136 USD for 1K from text, and 0.021, 0.028 and 0.039 USD for an edit at 1K,
  2K and 4K (measured, quality medium).
- More measured prices across the recipes: Seedance 2.5 on fal at 0.70 USD for
  5 seconds at 480p (listed at 1.10), Grok Imagine 2.0 on Kie at 0.02 USD.
- Kie's file upload for reference images is confirmed.
- Two new examples on the homepage, Nos. 9 and 10, made with the skill's own
  recipe.

## 26 Sep 2026

- **First release.** The skill calls Kie AI and fal.ai directly with your own
  key. Recipes for Nano Banana 2, GPT Image 2.5, Grok Imagine 2.0, Kling 3.0,
  MiniMax H3, Grok Video 1.5 and Seedance 2.5.
- Homepage at https://skill.contentcoach.se/ with a price list.
