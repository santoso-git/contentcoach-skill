# GPT Image 2.5 Flare (OpenAI, via Kie AI and fal.ai)

The one to reach for when **text inside the picture has to be readable** —
signs, packaging, posters, UI mockups, lettering. Letterforms hold, and Swedish
å ä ö come back intact. Also the model for **transparent backgrounds**.

OpenAI ships two variants at the same price on Kie: **Flare** (faster, used
here) and **Sunburst** (slower, finer detail). For Sunburst, swap `flare` for
`sunburst` in every id below.

| Field | Kie AI (first) | fal.ai (fallback) |
|---|---|---|
| Text → image | `gpt-image-2-5-flare-text-to-image` | `openai/gpt-image-2.5/flare/text-to-image` |
| With references | `gpt-image-2-5-flare-image-to-image`, refs in **`input_urls`** (max 16) | `openai/gpt-image-2.5/flare/edit`, refs in `image_urls` (max 16) |
| Method | Async — createTask, poll recordInfo | Sync |
| Size | `resolution`: `1K` `2K` `4K` | `image_size`: preset or `{width, height}` |
| Quality | none | `low` `medium` `high` `xhigh` `max` — **defaults to `high`**, always send `medium` |
| Background | `transparent` `opaque` `auto` | same |
| Seed | none | none |
| Cost 1K · 2K · 4K | **0.03 · 0.05 · 0.08 USD** (6 / 10 / 16 credits) | quote about 0.05 · 0.11 · 0.18 USD at `medium` |
| Docs | https://docs.kie.ai/market/gpt/gpt-image-2-5-flare-text-to-image | https://fal.ai/models/openai/gpt-image-2.5/flare/text-to-image/api |

**Confirmed on Kie, 26 Sep 2026:** image-to-image, one reference, 16:9 at 2K →
2736×1536 PNG, 10 credits = 0.05 USD as listed. It held the subject exactly
while replacing the whole background. **The fal route is unrun**; its quote is
GPT Image 2's measured price on fal and probably over-states 2.5.

## Kie AI

**The model id is chosen by whether you have references**, and the references go
in **`input_urls`** — not Nano Banana's `image_input`. The wrong key is accepted
and silently ignored, so the run succeeds with an image that ignored the
references.

```bash
jq -n --arg p "PROMPT" '{model:"gpt-image-2-5-flare-text-to-image",
  input:{prompt:$p, aspect_ratio:"1:1", resolution:"1K"}}' > /tmp/cc-req.json
# references:  model "gpt-image-2-5-flare-image-to-image", input.input_urls:["https://…"]
# cut-out:     input.background:"transparent"
curl -sS -X POST https://api.kie.ai/api/v1/jobs/createTask \
  -H "Authorization: Bearer $KIE_API_KEY" -H "Content-Type: application/json" \
  --data @/tmp/cc-req.json | jq -r '.data.taskId'
```

Poll exactly as in `nano-banana-2.md`; `resultJson` is a JSON string.

Aspect ratios, text-to-image: `auto 1:1 3:2 2:3 4:3 3:4 16:9 9:16 21:9 27:16
16:27 9:8 8:9`. The last four are 1K only. `4:5`, `5:4`, `2:1` and `3:1` exist on
image-to-image but not text-to-image.

## fal.ai

The prefix is **`openai/`, not `fal-ai/`**. `fal-ai/…` returns `model not found`.

```bash
OUT=generations/desc_$(date +%s).png
jq -n --arg p "PROMPT" '{prompt:$p, image_size:"landscape_16_9", quality:"medium",
  output_format:"png"}' > /tmp/cc-req.json
curl -sS -m 300 -X POST https://fal.run/openai/gpt-image-2.5/flare/text-to-image \
  -H "Authorization: Key $FAL_KEY" -H "Content-Type: application/json" \
  --data @/tmp/cc-req.json -o /tmp/cc-resp.json -D /tmp/cc-headers.txt
curl -sS -o "$OUT" "$(jq -r '.images[0].url' /tmp/cc-resp.json)"
grep -i x-fal-billable-units /tmp/cc-headers.txt
```

- `image_size` presets: `square_hd` `square` `portrait_4_3` `portrait_16_9`
  `landscape_4_3` `landscape_16_9` `auto`. An explicit `{width, height}` must be
  multiples of 16, longest edge at most 3840, ratio at most 3:1, and total pixels
  between 655,360 and 8,294,400. That ceiling rules out a 4096 px square
  entirely; the largest square is 2880×2880.
- **One billable unit is one US dollar** on this model — not Nano Banana's 0.08.
- `quality` scales the price roughly fourfold per step. Draft at `medium` or
  `low`; rerun only the winner higher.

## Notes

- State the layout literally — what sits where, at what size — rather than a
  mood.
- For a word with diacritics, quote it and name them: `the word "TÄVLING" with
  an umlaut over the A`.
