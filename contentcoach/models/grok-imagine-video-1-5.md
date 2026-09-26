# Grok Imagine Video 1.5 (xAI, via Kie AI and fal.ai)

Short video with Grok's look, on two routes with very different prices. **Sound
is always on**; no route has a switch.

**Quote before running** (SKILL.md, rule 1).

| Field | Kie AI (first) | fal.ai |
|---|---|---|
| Model id | `grok-imagine-video-1-5-preview` — one endpoint | `xai/grok-imagine-video/v1.5/text-to-video` · `…/image-to-video` · `…/reference-to-video` |
| Duration | number 1–15, default **8** | integer 1–15, default **6** |
| Resolution | `480p` `720p`, default `480p` | `480p` `720p`, default `720p` |
| Aspect | `auto` `1:1` `16:9` `9:16` `3:2` `2:3` | on text- and reference-to-video, never image-to-video |
| Images | `image_urls[]`, up to 7 | `image_url` (first frame) or `reference_image_urls[]` (likeness, up to 7) |
| Docs | https://kie.ai/model/grok-imagine-video-1-5-preview | https://fal.ai/models/xai/grok-imagine-video/v1.5/text-to-video |

The defaults disagree, so **always send duration and resolution**.

## Cost

Measured 21 Aug 2026, one 5-second 480p image-to-video clip on each route:
**0.06 USD on Kie, 0.41 USD on fal.** The gap is real.

| Per second | Kie | fal |
|---|---|---|
| 480p | 0.012 USD | 0.08 USD |
| 720p | 0.0225 USD | 0.14 USD |

5 s at 720p: 0.11 USD on Kie, 0.70 on fal. fal adds 0.01 USD per image,
including the required frame. The 720p rows are extrapolated from the published
tables, which held exactly at 480p.

## What an image means

- **Kie:** `image_urls` carries up to seven pictures of who the video is about.
  With exactly one, `aspect_ratio` is ignored and the picture's own ratio wins
  (measured: a 4:3 picture asking for 9:16 returned 624×480); with two or more
  the ratio applies.
- **fal `image-to-video`:** one `image_url`, always the first frame.
- **fal `reference-to-video`:** the picture is *who the video is about*, not
  where it starts. Address them in the prompt as `@Image1`, `@Image2`. This is
  the route that behaves like grok.com.

A prompt that describes a person the reference does not show makes the model
drop the reference. That is prompt behaviour, not a missing capability.

## Calls

```bash
# Kie — text to video, or add image_urls for a frame
jq -n --arg p "PROMPT" '{model:"grok-imagine-video-1-5-preview",input:{
  prompt:$p, duration:5, resolution:"480p", aspect_ratio:"16:9"}}' > /tmp/cc-req.json
curl -sS -X POST https://api.kie.ai/api/v1/jobs/createTask \
  -H "Authorization: Bearer $KIE_API_KEY" -H "Content-Type: application/json" \
  --data @/tmp/cc-req.json | jq -r '.data.taskId'

# fal — likeness
jq -n --arg p "@Image1 walks through rain" --arg r "https://…" '{prompt:$p,
  reference_image_urls:[$r], duration:5, resolution:"480p", aspect_ratio:"9:16"}' > /tmp/cc-req.json
curl -sS -X POST https://queue.fal.run/xai/grok-imagine-video/v1.5/reference-to-video \
  -H "Authorization: Key $FAL_KEY" -H "Content-Type: application/json" \
  --data @/tmp/cc-req.json | jq -r .request_id
```

Poll Kie as in `nano-banana-2.md`. Poll fal at
`https://queue.fal.run/xai/grok-imagine-video/requests/{id}/status`, then
`…/requests/{id}`, as in `minimax-h3.md`; the clip is at `.video.url`.

Kie calls the model `preview` — the kind of id that gets renamed. On `model not
found`, open the model page and copy the id fresh.
