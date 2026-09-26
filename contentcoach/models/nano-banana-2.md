# Nano Banana 2 (Google Gemini 3.1 Flash Image, via Kie AI and fal.ai)

Everyday images. Fast, strong prompt adherence, and good at holding a reference
image's identity — the default for anything carrying a logo or a product. Weak
spot: long stretches of small text inside the picture; that is GPT Image 2.5's
job.

Send the body with the patterns in SKILL.md, *Running a job*.

| Field | Kie AI (first) | fal.ai (fallback) |
|---|---|---|
| Model id | `nano-banana-2` — **no `google/` prefix** | `fal-ai/nano-banana-2` · with refs `fal-ai/nano-banana-2/edit` |
| Body | nested in `input` | flat |
| References | `image_input`, up to 14 **public URLs** | `image_urls`, public URLs **or base64 data URIs** |
| Seed | **accepted and silently ignored** | works |
| Cost 1K · 2K · 4K | **0.04 · 0.06 · 0.09 USD** (8 / 12 / 18 credits) | 0.08 · 0.12 · 0.16 USD |
| Docs | https://docs.kie.ai/market/google/nanobanana2 | https://fal.ai/models/fal-ai/nano-banana-2/api |

Both routes confirmed by real runs, Kie again on 26 Sep 2026 (1K, 16:9 →
1376×768 PNG, 8 credits, 30 s). On Kie, `google/nano-banana-2` returns 422; use
the exact string above.

## Kie AI

| Field | Notes |
|---|---|
| `prompt` | required, up to 20,000 characters |
| `aspect_ratio` | `1:1` `4:5` `3:4` `4:3` `16:9` `9:16` `21:9` `2:3` `3:2` …, default `auto` |
| `resolution` | `1K` (default) · `2K` · `4K` |
| `output_format` | `png` or `jpg`. Default is **`jpg`** — send `png` |
| `image_input` | array of public HTTPS URLs; upload local files first (SKILL.md) |

```bash
jq -n --arg p "PROMPT HERE" '{model:"nano-banana-2",input:{
  prompt:$p, aspect_ratio:"16:9", resolution:"1K", output_format:"png"}}' > /tmp/cc-req.json
# with references, add to input:  image_input:["https://…"]
```

A 1K image takes 15–45 seconds.

**Seed does nothing here.** The same prompt with the same seed returned two
unrelated images. For reproducible output, use fal.

## fal.ai

| Field | Notes |
|---|---|
| `prompt` | required |
| `aspect_ratio` | default `auto`. `16:9` headers, `1:1` social, `9:16` vertical |
| `resolution` | `1K` (default) · `2K` · `4K` |
| `output_format` | `png` (default) or `jpeg` |
| `num_images` | default 1. Each image bills separately |
| `seed` | same seed reproduces a result |
| `image_urls` | `/edit` only. URLs or `data:image/png;base64,…` |

```bash
jq -n --arg p "PROMPT HERE" '{prompt:$p, aspect_ratio:"16:9", resolution:"1K", output_format:"png"}' > /tmp/cc-req.json
# submit to fal-ai/nano-banana-2
```

With a local reference, inlined — submit to `fal-ai/nano-banana-2/edit`:

```bash
jq -n --arg p "PROMPT" --rawfile b64 <(base64 < ref.png | tr -d '\n') \
  '{prompt:$p, image_urls:["data:image/png;base64,"+$b64], aspect_ratio:"16:9", resolution:"1K"}' > /tmp/cc-req.json
```

Keep inlined references under a few MB; downscale first. `width` and `height` in
the response come back `null` — read dimensions from the downloaded file. One
billable unit is one 1K image, 0.08 USD.

## Changing an image you already made

No seed comes back, so rerunning a prompt gives a *new* picture, not the same
one with a tweak. Instead, **pass the image as its own reference** and ask for
exactly one change:

> Edit the FIRST reference image. Keep it exactly as it is: same camera angle,
> same layout, same objects in the same places, same light, same background. Do
> not move, add or remove anything, and do not change the framing. Make one
> change only: <the change>. Match the SECOND reference image for material and
> style.

- **List what must stay**, concretely. "Keep it the same" is not enough.
- **One change per run.** Two changes in one step often become a new picture.
- **Order matters.** Refer to the references as FIRST and SECOND in the prompt.

It costs the same as a new image. Measured on fal: a detailed landscape kept
every object in place while only the roof material changed.

## Notes

- Short words come out well despite text being the weak spot. Spell a word out
  letter by letter to lock it ("reading exactly P-E-L-L-E").
