# GPT Image 2.5 — Flare and Sunburst (OpenAI, via Kie AI and fal.ai)

The one to reach for when **text inside the picture has to be readable** —
signs, packaging, posters, UI mockups, lettering. Letterforms hold, and Swedish
å ä ö come back intact. Also the model for **transparent backgrounds**.

OpenAI ships 2.5 in two variants. **Flare** is faster and the default.
**Sunburst** is slower and holds finer detail; see *Sunburst* below. The two
share every request field on both providers (schemas compared 27 Sep 2026), so
switching is only a change of id.

Send the body with the patterns in SKILL.md, *Running a job*.

| Field | Kie AI | fal.ai (first — cheaper at every size) |
|---|---|---|
| Text → image | `gpt-image-2-5-flare-text-to-image` | `openai/gpt-image-2.5/flare/text-to-image` |
| With references | `gpt-image-2-5-flare-image-to-image`, refs in **`input_urls`** | `openai/gpt-image-2.5/flare/edit`, refs in `image_urls` (max 16) |
| Size | `resolution`: `1K` `2K` `4K` | `image_size`: preset or `{width, height}` |
| Quality | none | `low` `medium` `high` `xhigh` `max` — **defaults to `high`**, always send `medium` |
| Background | `transparent` `opaque` `auto` | same |
| Seed | none | none |
| Cost 1K · 2K · 4K | **0.03 · 0.05 · 0.08 USD** (6 / 10 / 16 credits) | text only: **0.0136** at 1K · with one reference: **0.0207 · 0.0275 · 0.0389 USD**, all at `medium` |
| Docs | https://docs.kie.ai/market/gpt/gpt-image-2-5-flare-text-to-image | https://fal.ai/models/openai/gpt-image-2.5/flare/text-to-image/api |

**Confirmed on Kie, 26 Sep 2026:** image-to-image, one reference, 16:9 at 2K →
2736×1536 PNG, 10 credits = 0.05 USD as listed. It held the subject exactly
while replacing the whole background.

**Confirmed on fal, 27 Sep 2026**, all at `quality: medium`, measured from
`x-fal-billable-units` (one unit = 1 USD), Flare and Sunburst alike:

| Job | Size | Cost | Time |
|---|---|---|---|
| Text → image, 1:1 | 1K, 1024×1024 | **0.0136 USD** | about 21–26 s |
| `/edit`, one reference, 9:16 | 1K, 768×1360 | **0.0207 USD** | about 27–30 s |
| `/edit`, one reference, 9:16 | 2K, 1536×2736 | **0.0275 USD** | about 28 s |
| `/edit`, one reference, 9:16 | 4K, 2160×3840 | **0.0389 USD** | about 33 s |

**fal is the cheaper route at every size** — at 4K with a reference it is half
Kie's 0.08 — so start there when `FAL_KEY` is set. A reference adds about 0.007
USD at 1K. Text-to-image above 1K is not measured, but should sit below the edit
prices. Use the queue at every size: a synchronous `fal.run` call at 4K ran over
60 seconds and was cut off, and fal probably billed the lost image anyway.

An edit can also move people into a new scene: "Use the people from the reference
image — same faces, … Make a new photograph of them, not an edit of this frame",
then the new scene, kept the faces and changed the camera angle entirely.

## Kie AI

**The model id is chosen by whether you have references**, and the references go
in **`input_urls`** — not Nano Banana's `image_input`. The wrong key is accepted
and silently ignored, so the run succeeds with an image that ignored the
references.

```bash
jq -n --arg p "PROMPT" '{model:"gpt-image-2-5-flare-text-to-image",
  input:{prompt:$p, aspect_ratio:"1:1", resolution:"1K"}}' > /tmp/cc-req.json
# references:  model "gpt-image-2-5-flare-image-to-image", add input.input_urls:["https://…"]
# cut-out:     add input.background:"transparent"
```

Aspect ratios: `auto 1:1 3:2 2:3 4:3 3:4 16:9 9:16 21:9 27:16 16:27 9:8 8:9`.
The last four are 1K only. There is no `4:5`.

## fal.ai

The prefix is **`openai/`, not `fal-ai/`**. `fal-ai/…` returns `model not found`.

```bash
jq -n --arg p "PROMPT" '{prompt:$p, image_size:{width:1360,height:768}, quality:"medium",
  output_format:"png", num_images:1}' > /tmp/cc-req.json
# submit to openai/gpt-image-2.5/flare/text-to-image
# with references: add image_urls:["https://… or data:image/png;base64,…"], submit to …/flare/edit
# cut-out: add background:"transparent"
```

- **Sizes by pixel budget:** about 1.05 MP for 1K, 4.2 MP for 2K and the
  8,294,400-pixel ceiling for 4K, edges in multiples of 16. Measured sizes: 1:1
  1024×1024; 9:16 768×1360, 1536×2736, 2160×3840 (16:9 is the same turned).
  For a square at 2K use 2048×2048, and at 4K 2880×2880.
- `image_size` presets: `square_hd` `square` `portrait_4_3` `portrait_16_9`
  `landscape_4_3` `landscape_16_9` `auto`. An explicit `{width, height}` must be
  multiples of 16, longest edge at most 3840, ratio at most 3:1, and total pixels
  between 655,360 and 8,294,400. That ceiling rules out a 4096 px square
  entirely; the largest square is 2880×2880.
- **One billable unit is one US dollar** on this model — not Nano Banana's 0.08.
- `quality` scales the price roughly fourfold per step. Draft at `medium` or
  `low`; rerun only the winner higher.

## Sunburst

OpenAI's precision variant: extra fidelity on intricate detail, in exchange for
longer generation times. Reach for it when fine detail carries the image and
there is time to wait — a product close-up, dense small lettering, a hero image
that will be printed or shown large. For drafts and everyday text-in-image work,
stay on Flare.

| Field | Kie AI | fal.ai (first) |
|---|---|---|
| Text → image | `gpt-image-2-5-sunburst-text-to-image` | `openai/gpt-image-2.5/sunburst/text-to-image` |
| With references | `gpt-image-2-5-sunburst-image-to-image`, refs in `input_urls` | `openai/gpt-image-2.5/sunburst/edit`, refs in `image_urls` (max 16), optional `mask_url` |
| Everything else | as Flare | as Flare — `quality` also defaults to `high`; send `medium` |
| Cost 1K · 2K · 4K | **the same as Flare:** 0.03 · 0.05 · 0.08 USD (6 / 10 / 16 credits), with or without references | **0.0136 USD at 1K**, `medium`, 1:1 · 2K and 4K unmeasured |
| Docs | https://docs.kie.ai/market/gpt/gpt-image-2-5-sunburst-text-to-image | https://fal.ai/models/openai/gpt-image-2.5/sunburst/text-to-image/api |

**Confirmed on Kie, 27 Sep 2026:** text-to-image, 1:1 at 1K → 1254×1254 PNG,
6 credits = **0.03 USD, as listed**, in **62–97 seconds** over three runs —
expect to run the poll block once or twice. Small label lettering, including å
and ä, came back exactly as asked. **`background: "transparent"` works:** it
returned an RGBA PNG with a real alpha channel, the can cut out cleanly with no
floor or shadow — say "isolated on a transparent background, no floor, no
shadow" in the prompt as well.
**Confirmed on fal, 27 Sep 2026:** the same prices as Flare at every size, and
**not slower** there — about 26 s for text-to-image and 27–30 s for an edit. The
slowness is Kie's. Kie's price for 2K and 4K is from its pricing page, 27 Sep
2026 (search the table for the model name; the page loads prices in the browser,
so `curl` does not see them).

## Notes

- State the layout literally — what sits where, at what size — rather than a
  mood.
- For a word with diacritics, quote it and name them: `the word "TÄVLING" with
  an umlaut over the A`.
