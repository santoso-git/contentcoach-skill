# Seedance 2.5 (ByteDance, via Kie AI and fal.ai)

High-end video with native sound, and the only model here that goes past 15
seconds — up to 30. Use it when asked for, or when the clip must be long.

> **The most expensive thing in this skill by far.** A 5-second 720p clip is
> 1.58 USD on Kie and listed at 2.37 on fal; 30 seconds at 720p is 9.45 USD on
> Kie and listed at 14.19 on fal. Quote it in full every time. Never route here to be helpful, and
> never assume an earlier yes carries over.

Send the body with the patterns in SKILL.md, *Running a job*; long clips take
many minutes, so expect to run the poll block several times.

| Field | Kie AI (first) | fal.ai |
|---|---|---|
| Model id | `bytedance/seedance-2-5` | `bytedance/seedance-2.5/text-to-video` · `…/image-to-video` |
| Body | nested in `input` | flat |
| Duration | a number, up to 30 | `"4"`–`"30"`, a string; default `"auto"` — **always set it**, or the price is unknowable |
| Resolution | `480p` `720p` | `480p` `720p`, default `720p` |
| Start frame | `first_frame_url`, optional | `image_url`, **required** on image-to-video |
| End frame | `last_frame_url` — works **without** a start frame | `end_image_url` |
| Likeness | `reference_image_urls[]` — never appears as a frame | not supported |
| Aspect "follow the image" | `adaptive` | `auto` |
| Sound | `generate_audio`, default true | `generate_audio`, default true |
| Seed | none | none — reports the one it used |
| Docs | https://kie.ai/model/bytedance/seedance-2-5.md | https://fal.ai/models/bytedance/seedance-2.5/text-to-video |

Sound does not change the price on either route. Both list 1080p too, unpriced
here; quote it only after reading the provider's price page.

**Confirmed on both** for text-to-video, 5 s at 480p: **0.70 USD actual on
each** (18 Aug 2026). On fal that is 0.14 USD/s, well below the 0.2205 its page
listed, so fal's 720p price is probably lower than listed too — quote the listed
figure until a 720p run is measured. Start frames, end frames and references are
unrun on both.

## Cost — quote one of these

| Duration | Kie 480p | Kie 720p | fal 480p | fal 720p |
|---|---|---|---|---|
| 5 s | 0.70 USD | 1.58 USD | 0.70 USD | 2.37 USD (listed) |
| 10 s | 1.40 USD | 3.15 USD | 1.40 USD | 4.73 USD (listed) |
| 30 s | 4.20 USD | 9.45 USD | 4.20 USD | **14.19 USD** (listed) |

Duration and resolution are the only levers. Draft at 480p. The rows past 5 s
are the measured per-second rate multiplied out.

## Kie AI

Kie ignores unknown fields, so fal's `image_url` here would bill a clip that
quietly dropped the image. Use Kie's names.

```bash
jq -n --arg p "PROMPT" --arg f "https://…first-frame.png" '{model:"bytedance/seedance-2-5",
  input:{prompt:$p, duration:5, resolution:"480p", first_frame_url:$f}}' > /tmp/cc-req.json
```

For "keep this character, new motion", pass the photo in `reference_image_urls`
instead of as a frame — only Kie can do that.

## fal.ai

```bash
jq -n --arg p "PROMPT" '{prompt:$p, resolution:"480p", duration:"5", aspect_ratio:"16:9"}' > /tmp/cc-req.json
# submit to bytedance/seedance-2.5/text-to-video
```

Image-to-video takes `image_url` (one image, max 30 MB) and optional
`end_image_url`, and only `aspect_ratio: "auto"`. Record the returned `seed` in
the sidecar. fal prices Seedance on a token formula, so a billable unit may not
be a second — check before trusting it.
