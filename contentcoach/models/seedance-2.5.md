# Seedance 2.5 (ByteDance, via Kie AI and fal.ai)

High-end video with native sound, and the only model here that goes past 15
seconds — up to 30. Use it when asked for, or when the clip must be long.

> **The most expensive thing in this skill by far.** A 5-second 720p clip is
> 1.58 USD on Kie and 2.37 on fal; the 30-second maximum on fal is 14.19 USD.
> Quote it in full every time. Never route here to be helpful, and never assume
> an earlier yes carries over.

| Field | Kie AI (first) | fal.ai |
|---|---|---|
| Model id | `bytedance/seedance-2-5` | `bytedance/seedance-2.5/text-to-video` · `…/image-to-video` |
| Body | nested in `input` | flat |
| Duration | a number | `"4"`–`"30"`, a string; default `"auto"` — **always set it**, or the price is unknowable |
| Resolution | `480p` `720p` | `480p` `720p`, default `720p` |
| Start frame | `first_frame_url`, optional | `image_url`, **required** on image-to-video |
| End frame | `last_frame_url` — works **without** a start frame | `end_image_url` |
| Likeness | `reference_image_urls[]` — never appears as a frame | not supported |
| Aspect "follow the image" | `adaptive` | `auto` |
| Sound | — | `generate_audio`, default true; does not change the price |
| Seed | none | none — reports the one it used |
| Docs | https://kie.ai/model/bytedance/seedance-2-5.md | https://fal.ai/models/bytedance/seedance-2.5/text-to-video |

**Confirmed on Kie** (480p, 5 s, 0.70 USD actual). The fal route is from docs,
unrun.

## Cost — quote one of these

| Duration | Kie 480p | Kie 720p | fal 480p | fal 720p |
|---|---|---|---|---|
| 5 s | 0.70 USD | 1.58 USD | 1.10 USD | 2.37 USD |
| 10 s | 1.40 USD | 3.15 USD | 2.21 USD | 4.73 USD |
| 30 s | 4.20 USD | 9.45 USD | 6.62 USD | **14.19 USD** |

Duration and resolution are the only levers. Draft at 480p. The Kie rows past
5 s are the measured per-second rate multiplied out; check Kie's maximum
duration on its model page before quoting a long clip there.

## Kie AI

Kie ignores unknown fields, so fal's `image_url` here would bill a clip that
quietly dropped the image. Use Kie's names.

```bash
jq -n --arg p "PROMPT" --arg f "https://…first-frame.png" '{model:"bytedance/seedance-2-5",
  input:{prompt:$p, duration:5, resolution:"480p", first_frame_url:$f}}' > /tmp/cc-req.json
curl -sS -X POST https://api.kie.ai/api/v1/jobs/createTask \
  -H "Authorization: Bearer $KIE_API_KEY" -H "Content-Type: application/json" \
  --data @/tmp/cc-req.json | jq -r '.data.taskId'
```

For "keep this character, new motion", pass the photo in `reference_image_urls`
instead of as a frame — only Kie can do that. Poll as in `nano-banana-2.md`;
long clips take many minutes.

## fal.ai

```
POST https://queue.fal.run/bytedance/seedance-2.5/text-to-video
POST https://queue.fal.run/bytedance/seedance-2.5/image-to-video
GET  https://queue.fal.run/bytedance/seedance-2.5/requests/{request_id}/status
GET  https://queue.fal.run/bytedance/seedance-2.5/requests/{request_id}
```

```json
{ "prompt": "…", "resolution": "480p", "duration": "5", "aspect_ratio": "16:9", "generate_audio": false }
```

Image-to-video takes `image_url` (one image, max 30 MB) and optional
`end_image_url`, and only `aspect_ratio: "auto"`. Poll as in `minimax-h3.md`;
record the returned `seed` in the sidecar. fal prices Seedance on a token
formula, so a billable unit may not be a second — check before trusting it.
