# Nano Banana 2.1 (Google, via Kie AI and fal.ai)

Google's successor to Nano Banana 2, released 6 Oct 2026. Same family and the
same request shape as `nano-banana-2.md`, under a new id. On Kie it is **the
cheapest Nano Banana there is: 0.02 USD at 1K**, half of Nano Banana 2 there.

**Quality is not yet proven.** Google's own benchmarks put it above Nano Banana
2; no independent comparison exists yet. Nano Banana 2 stays the default until
the two have been compared side by side. Use 2.1 when the user asks for it, or
for cheap drafts. Google's launch coverage says Nano Banana 2 shuts down on 29
Oct 2026; Kie and fal resell Google's model, so expect theirs to stop too unless
they say otherwise.

Send the body with the patterns in SKILL.md, *Running a job*.

| Field | Kie AI (first) | fal.ai (fallback, seed) |
|---|---|---|
| Model id | `nano-banana-2-1` — dashes, **no `google/` prefix** | `google/nano-banana-2.1` · with refs `google/nano-banana-2.1/edit` — **`google/`, not `fal-ai/`** |
| Body | nested in `input` | flat |
| References | `image_input`, up to **10** public URLs | `image_urls`, public URLs or base64 data URIs |
| Seed | none | `seed` |
| Cost 1K · 2K · 4K | **0.02 · 0.03 · 0.045 USD** (4 / 6 / 9 credits) | 0.08 at 1K · 2K and 4K unmeasured |
| Docs | Kie catalogue (`models/providers.md`, *Checking a price or a field live*) | https://fal.ai/models/google/nano-banana-2.1 |

**Confirmed on Kie, 8 Oct 2026:** text-to-image, 1K, 3:2 → 1264×848 PNG,
4 credits = **0.02 USD, as listed**, in under 15 seconds. **Confirmed on fal, 6
Oct 2026:** text-to-image at 1K, 0.08 USD (`x-fal-billable-units`), PNG
1024×1024, quoted lettering rendered correctly. **Editing with references is
not yet run on either route.** Kie's 2K and 4K are its listed prices; fal's are
not known — read `x-fal-billable-units` after the run.

## Kie AI

| Field | Notes |
|---|---|
| `prompt` | required |
| `aspect_ratio` | as Nano Banana 2, plus `1:4` `4:1` `1:8` `8:1`; default `auto` — send one |
| `resolution` | `1K` (default) · `2K` · `4K`, **uppercase** |
| `output_format` | `png` or `jpg`. Default is **`jpg`** — send `png` |
| `image_input` | up to 10 public HTTPS URLs; upload local files first (SKILL.md) |

```bash
NAME="NAME"   # the job name, see SKILL.md, Running a job
jq -n --arg p "PROMPT HERE" '{model:"nano-banana-2-1",input:{
  prompt:$p, aspect_ratio:"16:9", resolution:"1K", output_format:"png"}}' > "/tmp/cc-$NAME.req.json"
# with references, add to input:  image_input:["https://…"]
```

Field names are Nano Banana 2's, so `google/nano-banana-2.1` or a fal field
name is accepted and ignored — copy the id exactly.

## fal.ai

The body is Nano Banana 2's on fal (`nano-banana-2.md`, *fal.ai*); only the
path changes.

```bash
NAME="NAME"   # the job name, see SKILL.md, Running a job
jq -n --arg p "PROMPT HERE" '{prompt:$p, aspect_ratio:"16:9", resolution:"1K",
  output_format:"png", num_images:1}' > "/tmp/cc-$NAME.req.json"
# submit to google/nano-banana-2.1 · with image_urls:[…] submit to google/nano-banana-2.1/edit
```

fal's schema also takes `seed` (same seed, same picture), and adds fields the
skill does not need: `thinking_level`, `system_prompt`, `enable_web_search`,
`safety_tolerance`. Poll at the URLs the submit returns; the sub-path is dropped
as for every fal app.

## How to prompt it

Google's Nano Banana guide applies unchanged; nothing specific to 2.1 has been
published. See `nano-banana-2.md`, *How to prompt it* — including the one
negative it needs: with no words from the user, end with "No text, signs,
labels or logos anywhere in the image."
