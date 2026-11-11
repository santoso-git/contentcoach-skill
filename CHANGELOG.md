# Changelog

Changes to the skill, its recipes and its prices, newest first. Prices are in
USD and come from real runs unless marked as listed. To get the latest, run
`git -C ~/.contentcoach-skill pull`; the no-install route always reads `main`.

## 11 Nov 2026

- **Kling 3.0 on Kie costs more.** Kie raised it by about 8–12 % by 10 Nov
  2026: 720p 0.0755 USD per second without sound and 0.1135 with, 1080p 0.101
  and 0.151, 4K 0.3775 (listed, read 11 Nov). Five seconds at 720p is now 0.38
  USD. Kie is still cheaper than fal at every tier. The recipe's duration row
  now says string, as the body already sent.
- **Kling 3.0 start frames go to Kie as JPG.** A 1080×1920 PNG failed with
  `failCode 400` "omni input upload failed" (not billed); the same frame as
  JPG ran, 10 s at 720p for 0.70 USD at the old price.
- **Grok Video 1.5 on Kie gained fields:** `enable_audio` (sound off),
  `1080p` at 0.04 USD per second (listed — Kie fixed the label that read
  "720p"), and `first_frame_url`, `last_frame_url`, `loop_url` and
  `keyframes`. First and last frame are measured: the clip opens on one and
  lands on the other, in the pictures' own shape. **Kie bills an empty frame
  URL as a text-to-video**, so the recipe checks both URLs before submitting.
  `image_urls` on Kie did not keep a person from a selfie frame; for a person
  who must survive, use Seedance 2.0 Mini or Kling 3.0. fal still always adds
  sound and has no end frame.
- **Seedance 2.0 Mini:** `first_frame_url` and `reference_image_urls` exclude
  each other on Kie (422, not billed). A selfie clip is written as "The camera
  itself is the phone; never show a phone in the frame" — describing the phone
  made Seedance draw one.
- **GPT Image 2.6 tried, not used.** One edit with two references: 2.6 cost
  about 1.6–1.7 times 2.5 and was faster on fal, but 2.5 on fal was judged the
  best, so 2.5 stays and 2.6 has no recipe.

These came from runs by the ContentCoach app between 31 Oct and 11 Nov.

## 2 Nov 2026

- **Seedance 2.5 on fal moved to `bytedance/seedance-2.5/us/…`.** The old
  ids are no longer listed in fal's catalogue; the `/us/` text-, image- and
  reference-to-video ids are active with the same schema and price. Not yet
  run through the new path.
- **Nano Banana 2 still runs.** Google's coverage said it would shut down on
  29 Oct; on 2 Nov Kie showed a 100 % success rate and fal listed it as
  active. It stays the default; the 2.1 recipe says to switch if it starts
  failing.
- **Grok Video at 1080p, listed:** 0.25 USD per second on fal, 1.25 for 5 s.
  Kie's price text labels its third rate "720p" (8 credits, 0.04 USD per
  second), so its 1080p price is not quoted until a run shows it.
- **The body is checked against the provider's schema before every paid
  submit.** Kie accepts a field it does not know, drops it and bills the run
  anyway, so a misnamed field (fal's `image_url` where Kie wants
  `first_frame_url`) returns a paid result without the picture. A new step in
  `SKILL.md`, *Running a job*, reads the model's published input schema from
  Kie or fal — free — and compares the body actually being sent, field by
  field, with plain `jq`. A field the schema lacks or a value outside its list
  stops the job; a missing required field or a wrong type only warns, since
  Kie over-declares required fields. The idea comes from the ContentCoach
  app, which took it from OpenMontage's fal adapter.
- **Every recipe body passed** against 17 endpoints except one: Kling 3.0 on
  Kie sent `duration: 5`, a number, where the schema lists strings `"3"` to
  `"15"`. The recipe now sends `"5"`.

## 15 Oct 2026

- **Prices checked against both providers' catalogues and model pages.** Every
  Kie price in the recipes matches Kie's list. On fal, **MiniMax H3 at 768P
  dropped from 0.08 to 0.06 USD per second** (listed): 0.30 USD for 5 seconds,
  0.90 for 15. Kie's 768P at 0.20 is still cheaper, so the route is unchanged.
  fal's catalogue lists Kling 3.0 at 0.14 USD per second, but its model page
  keeps 0.112 with sound off, the recipe's figure. `providers.md` now shows
  how to read a fal model page's own price text, free and without a key.

## 14 Oct 2026

From a review of the scripts by another model, each point checked against the
files:

- **A downscaled reference no longer overwrites the original.** `sips -Z 2048
  file.png` rewrote the user's file in place; it now writes a copy to
  `/tmp/cc-$NAME.ref.png` (`--out`), and Linux gets the same step with
  ImageMagick (`magick`, or `convert` on Debian and Ubuntu, with
  `-resize '2048x2048>'`).
- **Local references to fal always go through `--rawfile`.** A base64 string
  passed with `--arg` fails with "Argument list too long" once the picture
  passes about 100 KB on Linux. The pattern now sits in `SKILL.md`, *Reference
  images*, and the Kling 3.0 and GPT Image 2.5 recipes point to it.
- **Temporary files are removed** with the sidecar: `rm -f /tmp/cc-$NAME.*`.
  A request carrying base64 references runs to megabytes.
- **GPT Image 2.5 on fal at 1K needs no quote** when `quality` is `medium`
  (measured 0.0136–0.0207 USD, cheaper than Nano Banana 2's 0.04). The rule
  dated from before the price was measured. Above `medium` it is still quoted;
  fal defaults to `high`, roughly four times the price.

## 9 Oct 2026

- **New homepage example, No. 12:** the octopus barista from the Haiku test —
  six arms, six jobs, all six in the picture — shown with the one-line ask and
  the full prompt the agent wrote, as an example of spelling out every part.

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
