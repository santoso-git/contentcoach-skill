# Grok Imagine Video 1.5 (xAI, via Kie AI and fal.ai)

Short video with Grok's look, on two routes with very different prices — on Kie
the cheapest video in this skill. **Sound:** on Kie a switch since 10 Nov 2026
(`enable_audio`, default `true`); on fal always on, with no field for it. A
silent clip or an end frame therefore means Kie.

**Quote before running** (SKILL.md, rule 1). Send the body with the patterns in
SKILL.md, *Running a job*.

| Field | Kie AI (first) | fal.ai |
|---|---|---|
| Model id | `grok-imagine-video-1-5-preview` — one endpoint | `xai/grok-imagine-video/v1.5/text-to-video` · `…/image-to-video` · `…/reference-to-video` |
| Duration | number 1–15, default **8** | integer 1–15; default **6** on text- and image-to-video, **8** on reference-to-video |
| Resolution | `480p` `720p` `1080p`, default `480p` | `480p` `720p` `1080p`; default `720p`; reference-to-video stops at `720p`, default **`480p`** |
| Aspect | `auto` `1:1` `16:9` `9:16` `3:2` `2:3` | on text- and reference-to-video, never image-to-video |
| Images | `image_urls[]`, up to 7; or `first_frame_url`, `last_frame_url`, `loop_url`, `keyframes` | `image_url` (first frame) or `reference_image_urls[]` (likeness, up to 7); no end frame |
| Sound | `enable_audio`, default `true` | always on |
| Docs | https://kie.ai/model/grok-imagine-video-1-5-preview.md | https://fal.ai/models/xai/grok-imagine-video/v1.5/text-to-video |

The defaults disagree, so **always send duration and resolution**. 1080p is
listed but not yet run on either provider; see the cost table.

## Cost

Measured 21 Aug 2026, one 5-second 480p clip from a picture on each route:
**0.06 USD on Kie, 0.41 USD on fal.** The gap is real.

| Per second | Kie | fal |
|---|---|---|
| 480p | 0.012 USD | 0.08 USD |
| 720p | 0.0225 USD | 0.14 USD |
| 1080p, listed | 0.04 USD | 0.25 USD |

5 s at 720p: 0.11 USD on Kie, 0.70 on fal. Also measured on Kie: 8 s and 10 s
at 480p, 0.096 and 0.12 USD — the same 0.012 per second. Runs so far: Kie text-
and image-to-video, fal text-to-video; fal's image- and reference-to-video are
unrun. fal adds 0.01 USD per image,
including the required frame. The 720p rows are extrapolated from the published
tables, which held exactly at 480p.

**1080p (listed, unrun):** Kie 0.04 USD per second, 0.20 for 5 s — Kie
corrected its price text by 10 Nov 2026, which had labelled this rate "720p";
fal 0.25 USD per second, 1.25 for 5 s.

## What an image means

- **Kie `image_urls`:** up to seven pictures of who the video is about.
  With exactly one, `aspect_ratio` is ignored and the picture's own ratio wins
  (measured: a 4:3 picture asking for 9:16 returned 624×480); with two or more
  the ratio applies. **It does not hold a person reliably:** on 31 Oct 2026 a
  dark 9:16 selfie frame in `image_urls` (10 s, 720p) came back as a different
  man in front of a different house, who then turned into a CGI wolf. When the
  person in the picture must survive, use Seedance 2.0 Mini or Kling 3.0.
- **Kie `first_frame_url` and `last_frame_url`** (since 10 Nov 2026): the clip
  opens on the first picture and lands on the last. **Measured 11 Nov 2026:**
  both fields, no `image_urls` and no `aspect_ratio`, 6 s at 720p with sound —
  accepted, and the clip took the pictures' 4:5 shape (832×1104, 6.04 s). The
  schema also has `loop_url` (start and end on the same picture) and
  `keyframes`, up to four mid-clip pictures as `{url, timestamp_s}` with
  0 < t < duration; neither is run yet. The schema still lists `image_urls` as
  required, so the schema check warns; go on.
- **Never send an empty frame URL to Kie.** It accepts `""` in
  `first_frame_url` and `last_frame_url` and bills a plain text-to-video
  (measured 11 Nov 2026: after a failed upload the clip came back 1280×720
  with neither picture). Check that every URL is non-empty before submitting,
  and leave a field out rather than sending it blank.
- **fal `image-to-video`:** one `image_url`, always the first frame.
- **fal `reference-to-video`:** the picture is *who the video is about*, not
  where it starts. Address them in the prompt as `@Image1`, `@Image2`. This is
  the route that behaves like grok.com.

A prompt that describes a person the reference does not show makes the model
drop the reference. That is prompt behaviour, not a missing capability.

## Bodies

```bash
NAME="NAME"   # the job name, see SKILL.md, Running a job
# Kie — text to video; add image_urls:["https://…"] for pictures of the subject,
# and enable_audio:false for a silent clip
jq -n --arg p "PROMPT" '{model:"grok-imagine-video-1-5-preview",input:{
  prompt:$p, duration:5, resolution:"480p", aspect_ratio:"16:9"}}' > "/tmp/cc-$NAME.req.json"

# Kie — from a first picture to a last one; no aspect_ratio, the pictures set it
F="https://…first.png"; L="https://…last.png"
[ -n "$F" ] && [ -n "$L" ] || echo "STOP: a frame URL is empty — upload again"
jq -n --arg p "PROMPT" --arg f "$F" --arg l "$L" '{model:"grok-imagine-video-1-5-preview",input:{
  prompt:$p, first_frame_url:$f, last_frame_url:$l, duration:6, resolution:"720p"}}' > "/tmp/cc-$NAME.req.json"

# fal — likeness; submit to xai/grok-imagine-video/v1.5/reference-to-video
jq -n --arg p "@Image1 walks through rain" --arg r "https://…" '{prompt:$p,
  reference_image_urls:[$r], duration:5, resolution:"480p", aspect_ratio:"9:16"}' > "/tmp/cc-$NAME.req.json"
```

Kie calls the model `preview` — the kind of id that gets renamed. On `model not
found`, open the model page and copy the id fresh.

## How to prompt it

From xAI's own guide, which is unusually specific:

- **Order matters:** the motion verb or camera move first, then light, then
  atmosphere, then sound. Earlier words weigh more.
- **Length is a choice:** with a reference that already carries the light and
  colour, 1–8 words; without one, 20–60. Never 10–15 — neither the picture nor
  the text is in control.
- **Always a motion verb** (turn, walk, drift, rise, spin); without one the
  clip barely moves.
- **Camera:** it follows explicit moves well — "camera slowly pushes in". To
  hold still, write exactly "camera not moving"; "steady shot" drifts.
- **Sound** is on unless Kie gets `enable_audio: false`: physical impacts and
  ambience come through best. Put it last. Spoken lines are unreliable; add
  one only if the user asked.
- Don't ask for 4K or 8K (it stops at 1080p), and don't re-describe the
  reference.
