---
name: contentcoach
description: Generate images and short videos with several AI models (Nano Banana 2, GPT Image 2.5, Grok Imagine, Kling 3.0, MiniMax H3, Seedance 2.5) through the ContentCoach API, using a personal key. Use when the user asks to generate, create or edit an image, a thumbnail, a blog header, a social image, a product shot or a mockup, to animate a picture or make a video clip, or mentions ContentCoach.
---

# ContentCoach — images and video over HTTP

Makes images and short videos by calling one HTTP API. The app holds every provider key; the
user needs only a personal ContentCoach key. Everything below is `curl`.
Answer the user in their own language.

API base: `https://create.contentcoach.se` (override with `CONTENTCOACH_URL`).

## The key

Read it from the environment variable `CONTENTCOACH_KEY`. If it is not set,
ask the user for it and tell them to set it themselves, for example in their
shell profile:

```bash
export CONTENTCOACH_KEY="…"
```

Never write the key into a file in the project, a commit, a log, or your reply.
Send it only to the API base above, only as `Authorization: Bearer`.

```bash
CC="${CONTENTCOACH_URL:-https://create.contentcoach.se}"
AUTH="Authorization: Bearer $CONTENTCOACH_KEY"
```

## 1. Check the key and the budget — free

```bash
curl -sS -H "$AUTH" "$CC/api/me"
```

Returns the image models (`models`) and video models (`video_models`) this
key may use — id, what each is for, aspects, resolutions or durations, price
range in USD — and, for a guest key,
`cap_usd`, `spent_usd` and `remaining_usd` for today. Choose the model from
this list, not from memory.

## 2. Reference images — optional, free

Anything the image must contain exactly — a logo, a product, a face, a photo
to edit — goes in as a file. **Never describe a logo, a face or a brand colour
in words**; the model gets it wrong every time. PNG, JPEG or WebP, max 4 MB
(downscale larger files first, e.g. `sips -Z 2048` on macOS).

```bash
curl -sS -H "$AUTH" -F "file=@logo.png" "$CC/api/upload"
# → {"url":"https://…/refs/…png"}
```

Pass the returned `url` values in `refs`. Only URLs from this upload are
accepted.

## 3. Quote — free

The same request as step 4 with `"dryRun": true`. Nothing is generated.

```bash
curl -sS -H "$AUTH" -H "Content-Type: application/json" "$CC/api/generate" \
  -d '{"prompt":"…","model":"nano-banana-2","aspect":"16:9","resolution":"1K","dryRun":true}'
```

`charged_usd` is what the run will take from today's budget (the dearest
route it could fall back to); `remaining_usd` is what is left. Tell the user
the price before a run when they asked for several images or anything above
1K.

## 4. Generate — costs money

```bash
curl -sS -H "$AUTH" -H "Content-Type: application/json" "$CC/api/generate" \
  -d '{"prompt":"…","model":"nano-banana-2","aspect":"16:9","resolution":"1K"}'
# → {"job":"eyJ…","model":"nano-banana-2","provider":"kie","estimate":0.04,…}
```

| Field | Values |
|---|---|
| `prompt` | Required. English works best. Describe subject, setting, light, composition, style. Max 5000 characters. |
| `model` | `nano-banana-2` (default), `gpt-image-2.5`, `grok-2` — see `/api/me` |
| `aspect` | e.g. `1:1`, `16:9`, `9:16`, `4:5`; `auto` follows the first reference |
| `resolution` | `1K` (default), `2K`, `4K` |
| `refs` | Array of URLs from step 2 |
| `quality` | `low` or `medium`, `grok-2` only |

**Draft at 1K.** Rerun at 2K or 4K only once the user has picked a favourite.
To edit an existing image, upload it, pass it in `refs`, and write the prompt
as an instruction: "Change the background to …, keep everything else".

## Video

Same three calls, with `"kind":"video"`. **Video costs roughly ten times an
image. Before every video run, show the user the model, duration, resolution,
sound on or off, and the `estimate` from a `dryRun`, and wait for an explicit
yes. One yes covers one run** — if the clip is wrong, quote again before a retry.

```bash
curl -sS -H "$AUTH" -H "Content-Type: application/json" "$CC/api/generate" \
  -d '{"kind":"video","model":"kling-3","prompt":"…","refs":["<start frame url>"],
       "duration":"5","resolution":"720p","audio":false,"dryRun":true}'
```

| Field | Values |
|---|---|
| `model` | `kling-3` (default), `minimax-h3`, `grok-video-1-5`, `seedance-2.5` — see `video_models` |
| `duration` | seconds as a string, one of the model's `durations` |
| `resolution` | one of the model's `resolutions`; always set it — some defaults are dear |
| `audio` | `true`/`false`. On Kling 3.0 sound costs about 45 % more |
| `refs` | one uploaded image = the **first frame**. Best results come from animating a still the user already likes |
| `endRef` | optional last frame (URL from step 2) |
| `subjectRefs` | pictures of who the video is about, not a frame: `grok-video-1-5` and `seedance-2.5` |
| `aspect` | used only without a start frame; with one, the ratio follows the picture |

Describe motion, not only the scene: what moves, how the camera moves, the
light. A video takes 1–5 minutes; poll every 10 seconds. The result is an mp4.

## 5. Wait for it

Poll every 5 seconds until `done` is true. An image takes 20–70 seconds.

```bash
curl -sS -H "$AUTH" "$CC/api/status?job=$JOB"
# → {"done":false}   …then…   {"done":true,"item":{"url":"https://…","cost_usd":0.04,…}}
```

## 6. Save it — right away

`item.url` expires within hours. Download it immediately into
`generations/` in the current project, flat, no subfolders, named
`{short-description}_{unix-timestamp}.{ext}` with the extension taken from
the URL. Then write a sidecar `.json` with the same basename holding the whole
`item` object, so the prompt that made the file is never lost. Show the user
the saved path (and the image, if you can display it).

## Errors

| Status | Meaning |
|---|---|
| 401 | The key is missing, wrong or revoked. Ask the user to check it. |
| 403 | Not allowed with this key, or a job that belongs to another key. |
| 429 | Today's budget cannot cover this run. The message says how much; it resets at midnight Stockholm time. Do not retry. |
| 400 | The request is invalid; the `error` text says why (unsupported aspect, too many refs…). Fix it and try once more. |
| 502 | The providers failed. Nothing was charged if no job was created. Try once more, then tell the user. |

Never loop on a failing request: every accepted submit is billed.
