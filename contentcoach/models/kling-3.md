# Kling 3.0 (via Kie AI and fal.ai)

General-purpose short video with optional native sound, and the default video
route. Best results come from animating a still the user already likes: make it
with Nano Banana 2, pick a winner, then animate it.

**Quote before running** (SKILL.md, rule 1). Send the body with the patterns in
SKILL.md, *Running a job*; poll every 10–15 s, a clip takes 1–5 minutes.

| Field | Kie AI (first) | fal.ai (fallback) |
|---|---|---|
| Model id | `kling-3.0/video` — one id for text and image | `fal-ai/kling-video/v3/pro/text-to-video` · `…/v3/pro/image-to-video` |
| Resolution | `mode`: `std` = 720p, `pro` = 1080p | 1080p only |
| Duration | 3–15, **a number** | `"3"`–`"15"`, **a string** |
| Sound | `sound`, **default true** — always send it | `generate_audio`, **default true** — always send it |
| Frames | `image_urls`: `[first]` or `[first, last]` | `start_image_url`, `end_image_url` |
| Aspect | `16:9` `9:16` `1:1`; ignored when a frame is given | same; image-to-video has none |
| Negative prompt | none | `negative_prompt` |
| Seed | none | none |
| Docs | https://docs.kie.ai/market/kling/kling-3-0 | https://fal.ai/models/fal-ai/kling-video/v3/pro/image-to-video/api |

**Confirmed on Kie, 26 Sep 2026:** `std`, 3 s, no sound, one start frame →
1284×716 at 24 fps, no audio track, **0.21 USD — exactly the quote**. The fal
route is unrun; its prices are published, not measured.

## Cost — quote one of these

| Per second | Sound off | Sound on |
|---|---|---|
| Kie 720p (`std`) | 0.07 USD | 0.10 USD |
| Kie 1080p (`pro`) | 0.09 USD | 0.135 USD |
| fal 1080p | 0.112 USD | 0.168 USD |

| 5 s clip | Sound off | Sound on |
|---|---|---|
| Kie 720p | 0.35 USD | 0.50 USD |
| Kie 1080p | 0.45 USD | 0.675 USD |
| fal 1080p | 0.56 USD | 0.84 USD |

## Kie AI

```bash
jq -n --arg p "PROMPT" --arg img "https://…first-frame.png" '{model:"kling-3.0/video",input:{
  prompt:$p, duration:5, mode:"std", sound:false, multi_shots:false, image_urls:[$img]}}' > /tmp/cc-req.json
```

- `sound`, `duration`, `mode` and `multi_shots` are all required. Send
  `multi_shots: false` explicitly, and `sound: false` unless sound was quoted.
- Without a frame, send `aspect_ratio` and drop `image_urls`.
- A last frame alone is not possible: index 0 is always the first frame.
- Frames must be public URLs — upload local files first (SKILL.md).

## fal.ai

Submit to `fal-ai/kling-video/v3/pro/image-to-video`:

```bash
jq -n --arg p "PROMPT" --arg img "https://… or data:image/png;base64,…" '{prompt:$p,
  start_image_url:$img, duration:"5", generate_audio:false,
  negative_prompt:"blur, distort, low quality"}' > /tmp/cc-req.json
```

One billable unit is one second.

## Prompting

Describe motion, not only the scene: what moves, how the camera moves, the
light. With a start frame, the picture already says what is there — spend the
prompt on what happens.
