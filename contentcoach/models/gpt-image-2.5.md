# GPT Image 2.5 — Flare and Sunburst (OpenAI, via Kie AI and fal.ai)

The one to reach for when **text inside the picture has to be readable** —
signs, packaging, posters, UI mockups, lettering. Letterforms hold, and Swedish
å ä ö come back intact. Also the model for **transparent backgrounds**.

OpenAI ships 2.5 in two variants. **Flare** is faster and the default.
**Sunburst** is slower and holds finer detail; see *Sunburst* below. The two
share every request field on both providers (schemas compared 27 Sep 2026), so
switching is only a change of id.

Send the body with the patterns in SKILL.md, *Running a job*.

| Field | Kie AI (first) | fal.ai (fallback) |
|---|---|---|
| Text → image | `gpt-image-2-5-flare-text-to-image` | `openai/gpt-image-2.5/flare/text-to-image` |
| With references | `gpt-image-2-5-flare-image-to-image`, refs in **`input_urls`** | `openai/gpt-image-2.5/flare/edit`, refs in `image_urls` (max 16) |
| Size | `resolution`: `1K` `2K` `4K` | `image_size`: preset or `{width, height}` |
| Quality | none | `low` `medium` `high` `xhigh` `max` — **defaults to `high`**, always send `medium` |
| Background | `transparent` `opaque` `auto` | same |
| Seed | none | none |
| Cost 1K · 2K · 4K | **0.03 · 0.05 · 0.08 USD** (6 / 10 / 16 credits) | **0.0136 USD at 1K**, `medium`, 1:1 · 2K and 4K unmeasured |
| Docs | https://docs.kie.ai/market/gpt/gpt-image-2-5-flare-text-to-image | https://fal.ai/models/openai/gpt-image-2.5/flare/text-to-image/api |

**Confirmed on Kie, 26 Sep 2026:** image-to-image, one reference, 16:9 at 2K →
2736×1536 PNG, 10 credits = 0.05 USD as listed. It held the subject exactly
while replacing the whole background.

**Confirmed on fal, 27 Sep 2026:** text-to-image, 1:1 at 1K, `quality: medium`
→ **0.0136 USD** measured from `x-fal-billable-units`. At 1K that makes **fal the
cheaper route** — less than half Kie's 0.03 — so start there for 1K drafts when
`FAL_KEY` is set. fal's `/edit` with references is unrun, and so are 2K and 4K:
fal bills this model by tokens, so quote larger sizes as "unmeasured, likely
under 0.10 USD at `medium`" until a run says otherwise.

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
jq -n --arg p "PROMPT" '{prompt:$p, image_size:"landscape_16_9", quality:"medium",
  output_format:"png"}' > /tmp/cc-req.json
# submit to openai/gpt-image-2.5/flare/text-to-image
```

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

| Field | Kie AI (first) | fal.ai (fallback) |
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
**Confirmed on fal, 27 Sep 2026:** text-to-image, 1:1 at 1K, `medium` →
**0.0136 USD**, the same as Flare, in about **26 seconds** — much faster than on
Kie. fal's `/edit` is unrun. Kie's price for 2K and 4K is from its pricing page,
27 Sep 2026 (search the table for the model name; the page loads prices in the
browser, so `curl` does not see them). fal's page lists, at `high`, 0.0527 USD
at 1024², 0.0396 at 1920×1080 and 0.1001 at 3840×2160 — figures that do not add
up; quote 2K and 4K on fal as unmeasured.

## Notes

- State the layout literally — what sits where, at what size — rather than a
  mood.
- For a word with diacritics, quote it and name them: `the word "TÄVLING" with
  an umlaut over the A`.
