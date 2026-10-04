# MiniMax H3 (via Kie AI and fal.ai)

Cheap clips, 4–15 seconds, and the **only video model with a seed** (on fal).
Since Kie's price cut, **768P on Kie is the cheapest H3 there is**: 0.20 USD for
five seconds, under fal's 480P at 0.25. Both providers default to the dear 2K
tier — always set the resolution. In a side-by-side test (4 Oct 2026) H3 gave
more dramatic motion than Seedance 2.0 Mini but drifted further from the
subject.

**Quote before running** (SKILL.md, rule 1). Send the body with the patterns in
SKILL.md, *Running a job*; poll every 10–15 s.

| Field | Kie AI (first — cheapest) | fal.ai (480P, 4K, seed) |
|---|---|---|
| Model id | `minimax-h3/text-to-video` · `minimax-h3/image-to-video` · `minimax-h3/reference-to-video` | `minimax/h3/text-to-video` · `minimax/h3/image-to-video` |
| Resolution | `768P` `2K`, default `2K` | `480P` `768P` `2K` `4K`, **uppercase P**, default `2K` |
| Duration | integer 4–15, **default 15** | integer 5–15, default 5 — **not a string**, unlike Kling on fal |
| Frames | `first_frame_url`, `last_frame_url` | `image_url`, `end_image_url` |
| Likeness | `reference-to-video`: `reference_image_urls[]` — no frame fields there | no |
| Prompt rewrite | none — Kie has no switch and does not rewrite | `prompt_expansion_mode`, default `balanced` |
| Seed | none | `seed` |
| Sound | always an audio track, though no field exists | — |
| Docs | https://kie.ai/model/minimax-h3/text-to-video.md | https://fal.ai/models/minimax/h3/text-to-video |

**Confirmed on Kie, 4 Oct 2026:** image-to-video, 5 s at 768P from a start
frame → 768×1024, 5.18 s with an AAC audio track; 40 credits = **0.20 USD**, and
the start frame was not charged as an extra image. **Confirmed on fal** (Aug
2026): text- and image-to-video, one with an end frame. Those runs sent the
older `enable_prompt_expansion: false`, which fal's current schema has replaced
with `prompt_expansion_mode`; the new field is unrun.

## Cost — quote one of these

| Route and resolution | per second | 5 s | 15 s |
|---|---|---|---|
| **Kie `768P`** | **0.04 USD** | **0.20 USD** | 0.60 USD |
| Kie `2K` (its default, listed) | 0.065 USD | 0.33 USD | 0.98 USD |
| fal `480P` | 0.05 USD | 0.25 USD | 0.75 USD |
| fal `768P` | 0.08 USD | 0.40 USD | 1.20 USD |
| fal `2K` (its default) | 0.13 USD | 0.65 USD | 1.95 USD |
| fal `4K` | 0.16 USD | 0.80 USD | 2.40 USD |

Kie is half fal's price at the tiers they share. Go to fal only for 480P, 4K or
a seed.

## Kie AI

Kie's defaults are 15 seconds at 2K, so **always send `duration`, `resolution`
and `aspect_ratio`**. The model id is chosen by what images the job carries:
none → `text-to-video`, first/last frame → `image-to-video`, likeness →
`reference-to-video`. Frames must be public URLs: upload local files first
(SKILL.md, *Reference images*).

```bash
jq -n --arg p "PROMPT" --arg u "https://…start-frame.jpg" '{model:"minimax-h3/image-to-video",
  input:{prompt:$p, first_frame_url:$u, duration:5, resolution:"768P"}}' > /tmp/cc-req.json
```

Kie ignores unknown fields, so fal's `image_url` bills a clip that quietly
dropped the frame. Kie's `hailuo/…` models are Hailuo 02 and 2.3 — older models,
not a cheaper route to H3.

## fal.ai

| Field | Notes |
|---|---|
| `aspect_ratio` | text-to-video only: `21:9` `16:9` `4:3` `1:1` `3:4` `9:16` |
| `image_url` | first frame. Optional: with only `end_image_url`, H3 renders toward that frame |
| `end_image_url` | **image-to-video only.** `text-to-video` drops it silently and still bills — any job with a frame goes to `image-to-video` |
| `prompt_expansion_mode` | `disabled` `fast` `balanced` `quality`, default `balanced`: the model rewrites your prompt. Send `disabled` to keep your wording |

```bash
jq -n --arg p "PROMPT" '{prompt:$p, duration:5, resolution:"480P", aspect_ratio:"16:9",
  prompt_expansion_mode:"disabled"}' > /tmp/cc-req.json
# submit to minimax/h3/text-to-video
```

`x-fal-billable-units` in the headers is the real cost, but confirm what one
unit means for this model before trusting it — it differs per model.
