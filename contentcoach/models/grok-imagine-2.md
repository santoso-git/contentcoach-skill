# Grok Imagine Image 2.0 (xAI, via Kie AI and fal.ai)

A different look from Nano Banana. Reach for it when Nano Banana's reading of
the brief is wrong, or when asked for — not to save money on the default.

| Field | Kie AI (text → image) | fal.ai (text → image, and edits) |
|---|---|---|
| Model id | `grok-imagine-image-2-0/text-to-image` | `xai/grok-imagine-image/v2.0/text-to-image` · `xai/grok-imagine-image/v2.0/edit` |
| Method | Async — createTask, poll recordInfo | Async — queue submit, poll |
| References | **none** — Kie's edit takes a Kie task id, not your image | `/edit`: up to **3** via `image_urls`, +0.01 USD each |
| Seed | none | none |
| Cost | **0.02 USD** (4 credits) | 1k: 0.04 `low` · 0.06 `medium`; 2k: 0.06 · 0.08 |
| Docs | https://kie.ai/model/grok-imagine-image-2-0/text-to-image.md | https://fal.ai/models/xai/grok-imagine-image/v2.0/text-to-image |

fal route confirmed by a real run (`1k` / `low` / `3:2` → 1248×832). Kie's text
to image is the same version at half the price.

**Edits go to fal regardless of price.** Kie's `image-edit` endpoint takes a
`task_id` and edits a previous Kie job; it cannot see an image you supply.

## Kie AI

```bash
jq -n --arg p "PROMPT" '{model:"grok-imagine-image-2-0/text-to-image",
  input:{prompt:$p, aspect_ratio:"1:1"}}' > /tmp/cc-req.json
curl -sS -X POST https://api.kie.ai/api/v1/jobs/createTask \
  -H "Authorization: Bearer $KIE_API_KEY" -H "Content-Type: application/json" \
  --data @/tmp/cc-req.json | jq -r '.data.taskId'
```

Poll as in `nano-banana-2.md`.

## fal.ai

```
POST https://queue.fal.run/xai/grok-imagine-image/v2.0/text-to-image
POST https://queue.fal.run/xai/grok-imagine-image/v2.0/edit
GET  https://queue.fal.run/xai/grok-imagine-image/requests/{request_id}/status
GET  https://queue.fal.run/xai/grok-imagine-image/requests/{request_id}
```

Submit keeps the sub-path; status and result **strip it** back to
`xai/grok-imagine-image`. Keeping it on a poll returns a 404 that looks like a
lost job.

| Field | Notes |
|---|---|
| `aspect_ratio` | `auto` `2:1` `20:9` `19.5:9` `16:9` `4:3` `3:2` `1:1` `2:3` `3:4` `9:16` `9:19.5` `9:20` `1:2`. **No `4:5`** — the job is rejected |
| `resolution` | **lowercase** `1k` / `2k`. Nano Banana uses uppercase |
| `quality` | `low` or `medium`, default **`medium`** — send `low` for drafts |
| `output_format` | default **`jpeg`** — send `png` |
| `num_images` | 1–4, each billed |

```bash
BASE=https://queue.fal.run/xai/grok-imagine-image
OUT=generations/desc_$(date +%s).png
jq -n --arg p "PROMPT" '{prompt:$p, aspect_ratio:"1:1", resolution:"1k", quality:"low",
  output_format:"png"}' > /tmp/cc-req.json
RID=$(curl -sS -X POST "$BASE/v2.0/text-to-image" -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" --data @/tmp/cc-req.json | jq -r .request_id)

for i in $(seq 1 30); do
  ST=$(curl -sS -H "Authorization: Key $FAL_KEY" "$BASE/requests/$RID/status" | jq -r .status)
  case "$ST" in COMPLETED|FAILED) break;; esac
  sleep 5
done
curl -sS -H "Authorization: Key $FAL_KEY" "$BASE/requests/$RID" -o /tmp/cc-resp.json -D /tmp/cc-headers.txt
jq -e '.images[0].url' /tmp/cc-resp.json >/dev/null || jq .detail /tmp/cc-resp.json
curl -sS -o "$OUT" "$(jq -r '.images[0].url' /tmp/cc-resp.json)"
```

- Record `revised_prompt` in the sidecar when it is not `null`.
- **One billable unit is 0.01 USD** on this model: a `1k`/`low` image bills 4.
