# MiniMax H3 (via fal.ai and Kie AI)

The cheapest video here at 480P, 5–15 seconds, and the **only video model with a
seed**. Its own default resolution is 2K, which costs nearly twice Kling —
always set the resolution.

**Quote before running** (SKILL.md, rule 1).

| Field | fal.ai (first — has 480P) | Kie AI |
|---|---|---|
| Model id | `minimax/h3/text-to-video` · `minimax/h3/image-to-video` | `minimax-h3/text-to-video` · `minimax-h3/image-to-video` · `minimax-h3/reference-to-video` |
| Resolution | `480P` `768P` `2K` `4K`, **uppercase P**, default `2K` | `768P` `2K`, default `2K` |
| Duration | integer 5–15 — **not a string**, unlike Kling | integer 4–15 |
| Frames | `image_url`, `end_image_url` | `first_frame_url`, `last_frame_url` |
| Likeness | no | `reference-to-video`: `reference_image_urls[]` — no frame fields there |
| Seed | `seed` | — |
| Docs | https://fal.ai/models/minimax/h3/text-to-video | https://kie.ai/model/minimax-h3/text-to-video.md |

Text-to-video on fal confirmed by a real run. The Kie route is from docs, unrun.

## Cost — quote one of these

| Resolution | per second | 5 s | 15 s |
|---|---|---|---|
| `480P` (fal only) | 0.05 USD | **0.25 USD** | 0.75 USD |
| `768P` | 0.08 USD | 0.40 USD | 1.20 USD |
| `2K` (the default) | 0.13 USD | 0.65 USD | 1.95 USD |
| `4K` (fal only) | 0.16 USD | 0.80 USD | 2.40 USD |

Kie matches fal at the tiers they share, so fal's 480P makes **fal the first
route** for this model.

## fal.ai

```
POST https://queue.fal.run/minimax/h3/text-to-video
POST https://queue.fal.run/minimax/h3/image-to-video
GET  https://queue.fal.run/minimax/h3/requests/{request_id}/status
GET  https://queue.fal.run/minimax/h3/requests/{request_id}
```

| Field | Notes |
|---|---|
| `aspect_ratio` | text-to-video only: `21:9` `16:9` `4:3` `1:1` `3:4` `9:16` |
| `image_url` | first frame. Optional: with only `end_image_url`, H3 renders toward that frame |
| `end_image_url` | **image-to-video only.** `text-to-video` drops it silently and still bills — any job with a frame goes to `image-to-video` |
| `enable_prompt_expansion` | defaults **true**: the model rewrites your prompt. Send `false` to keep your wording |

```bash
BASE=https://queue.fal.run/minimax/h3
OUT=generations/desc_$(date +%s).mp4
jq -n --arg p "PROMPT" '{prompt:$p, duration:5, resolution:"480P", aspect_ratio:"16:9",
  enable_prompt_expansion:false}' > /tmp/cc-req.json
RID=$(curl -sS -X POST "$BASE/text-to-video" -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" --data @/tmp/cc-req.json | jq -r .request_id)
echo "$RID"
```

Poll in its own tool call:

```bash
for i in $(seq 1 40); do
  ST=$(curl -sS -H "Authorization: Key $FAL_KEY" "$BASE/requests/$RID/status" | jq -r .status)
  case "$ST" in COMPLETED|FAILED) break;; esac
  sleep 12
done
curl -sS -H "Authorization: Key $FAL_KEY" "$BASE/requests/$RID" -o /tmp/cc-resp.json -D /tmp/cc-headers.txt
jq -e '.video.url' /tmp/cc-resp.json >/dev/null || jq .detail /tmp/cc-resp.json
curl -sS -o "$OUT" "$(jq -r .video.url /tmp/cc-resp.json)"
```

`x-fal-billable-units` in the headers is the real cost, but confirm what one
unit means for this model before trusting it — it differs per model.

## Kie AI

Same body fields nested in `input`, but **image-to-video renames the frames**:
`first_frame_url` and `last_frame_url`. Kie ignores unknown fields, so sending
fal's `image_url` bills a clip that quietly dropped the frame. Poll as in
`nano-banana-2.md`.

Kie's `hailuo/…` models are Hailuo 02 and 2.3 — older models, not a cheaper
route to H3.
