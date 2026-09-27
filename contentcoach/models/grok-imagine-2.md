# Grok Imagine Image 2.0 (xAI, via Kie AI and fal.ai)

A different look from Nano Banana, and the cheapest image here. Reach for it when
Nano Banana's reading of the brief is wrong, or when asked for.

Send the body with the patterns in SKILL.md, *Running a job*.

| Field | Kie AI (text → image) | fal.ai (text → image, and edits) |
|---|---|---|
| Model id | `grok-imagine-image-2-0/text-to-image` | `xai/grok-imagine-image/v2.0/text-to-image` · `xai/grok-imagine-image/v2.0/edit` |
| References | **none** — Kie's edit takes a Kie task id, not your image | `/edit`: up to **3** via `image_urls`, +0.01 USD each |
| Seed | none | none |
| Cost | **0.02 USD** (4 credits) | 1k: 0.04 `low` · 0.06 `medium`; 2k: 0.06 · 0.08 |
| Docs | https://kie.ai/model/grok-imagine-image-2-0/text-to-image.md | https://fal.ai/models/xai/grok-imagine-image/v2.0/text-to-image |

Both confirmed by real runs: fal text-to-image (`1k` / `low`, 0.04 USD, 26 Sep
2026) and `/edit` with references (0.05–0.09 USD); Kie text-to-image at **0.02
USD** measured. Kie's body has no resolution field — one size, one price.

**Edits go to fal regardless of price.** Kie's `image-edit` endpoint takes a
`task_id` and edits a previous Kie job; it cannot see an image you supply.

## Kie AI

```bash
jq -n --arg p "PROMPT" '{model:"grok-imagine-image-2-0/text-to-image",
  input:{prompt:$p, aspect_ratio:"1:1"}}' > /tmp/cc-req.json
```

## fal.ai

| Field | Notes |
|---|---|
| `aspect_ratio` | `2:1` `20:9` `19.5:9` `16:9` `4:3` `3:2` `1:1` `2:3` `3:4` `9:16` `9:19.5` `9:20` `1:2`. **No `4:5`** — the job is rejected |
| `resolution` | **lowercase** `1k` / `2k`. Nano Banana uses uppercase |
| `quality` | `low` or `medium`, default **`medium`** — send `low` for drafts |
| `output_format` | default **`jpeg`** — send `png` |
| `num_images` | 1–4, each billed |
| `image_urls` | `/edit` only, up to 3 |

```bash
jq -n --arg p "PROMPT" '{prompt:$p, aspect_ratio:"1:1", resolution:"1k", quality:"low",
  output_format:"png"}' > /tmp/cc-req.json
# submit to xai/grok-imagine-image/v2.0/text-to-image
```

- Record `revised_prompt` from the response in the sidecar when it is not `null`.
- **One billable unit is 0.01 USD** on this model: a `1k`/`low` image bills 4.
