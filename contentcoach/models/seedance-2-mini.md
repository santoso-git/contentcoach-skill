# Seedance 2.0 Mini (ByteDance, via Kie AI)

The cheap Seedance. Text-to-video or image-to-video, 4–15 seconds, 480p or
720p, with sound included. In a side-by-side test on one start frame (4 Oct
2026) it was the model that kept the subject recognisable while transforming
it.

**Quote before running** (SKILL.md, rule 1). Send the body with the patterns in
SKILL.md, *Running a job*; a clip takes about three minutes, so expect to run the
poll block two or three times.

| Field | Value |
|---|---|
| Model id | `bytedance/seedance-2-mini` — one endpoint; images are optional fields |
| Provider | Kie AI only here. fal carries it (`bytedance/seedance-2.0/mini/…`), priced in tokens; no recipe |
| Duration | integer 4–15, default 5 |
| Resolution | `480p` `720p`, **default 720p** |
| Aspect | `1:1` `4:3` `3:4` `16:9` `9:16` `21:9` `adaptive`, **default 16:9** |
| Start frame | `first_frame_url` — rendered as frame one |
| End frame | `last_frame_url` |
| Likeness | `reference_image_urls[]` — never a frame. Frames and references at most 9 in all |
| Sound | `generate_audio`, default true; does not change the price |
| Seed, negative prompt | none |
| Docs | https://kie.ai/seedance-2-mini · schema via the catalogue (`models/providers.md`) |

**Confirmed on Kie, 4 Oct 2026:** 5 s at 720p from a start frame, sound on,
`aspect_ratio: "3:4"` → 834×1112, 5.09 s with an AAC audio track, in about three
minutes; 41 credits = **0.205 USD, as listed**. **7 Oct 2026:** 5 s at 720p with
sound, 41 credits again; and a 15 s text-to-video at 720p,
16:9, sound on → 1280×720, 15.1 s, 123 credits = **0.615 USD**.

## Cost — quote one of these

| Duration | 480p (0.019 USD/s) | 720p (0.041 USD/s) |
|---|---|---|
| 4 s | 0.08 USD | 0.16 USD |
| 5 s | 0.10 USD | **0.21 USD** |
| 10 s | 0.19 USD | 0.41 USD |
| 15 s | 0.29 USD | 0.62 USD |

About **an eighth of Seedance 2.5** at 720p (0.315 USD/s on Kie).

Kie first sold this price as a discount ending 7 Oct 2026; its catalogue still
showed the same rates after that date (7 Oct, 15:43 UTC) without calling them a
discount. If a run bills differently, check `pricingDesc` in Kie's free
catalogue (`models/providers.md`, *Checking a price or a field live*). The
"with video" rates in Kie's listing are for a *video input*, billed on input
plus output seconds — not the sound switch.

## Body

```bash
NAME="NAME"   # the job name, see SKILL.md, Running a job
jq -n --arg p "PROMPT" --arg u "https://…start-frame.jpg" '{model:"bytedance/seedance-2-mini", input:{
  prompt:$p, first_frame_url:$u, resolution:"720p", aspect_ratio:"adaptive",
  duration:5, generate_audio:true}}' > "/tmp/cc-$NAME.req.json"
```

- **Send `aspect_ratio: "adaptive"` with a start frame.** The default is 16:9,
  so a portrait picture sent without it is reframed to landscape.
- Frames must be public URLs: upload a local file first (SKILL.md, *Reference
  images*).
- Kie ignores fields it does not know, so fal's names (`image_url`,
  `end_image_url`) bill a clip that quietly dropped the picture.

**Text to video** — no picture, so a fixed ratio:

```bash
NAME="NAME"   # the job name, see SKILL.md, Running a job
jq -n --arg p "PROMPT" '{model:"bytedance/seedance-2-mini", input:{
  prompt:$p, resolution:"720p", aspect_ratio:"16:9",
  duration:5, generate_audio:true}}' > "/tmp/cc-$NAME.req.json"
```

## How to prompt it

- Subject → motion → setting → look → camera → sound, 40–90 words (per shot,
  in a shot list).
- **Verbs, not adjectives:** spend the words on what moves and how, and give
  the physics a consequence — "leaves scatter on each step".
- **One camera move per shot**, in precise terms: dolly, pan, tilt, crane,
  push-in, rack focus, locked-off, tracking alongside. Never "epic cinematic
  camera".
- **Over-direct the sound:** name each sound and what makes it ("the hiss of
  steam"). An open prompt gets a music score, so write "no music" when only
  ambience is wanted.
- Dialogue only if the user wrote it, short, in quotes, with a tone.
- Likeness references are cited as `@Image1`, `@Image2` in the order sent,
  each with its role: "@Image1 for her face and red coat".
- No quality filler (stunning, 8k, masterpiece).

## Several shots in one clip

Up to 15 seconds, a timed shot list in one prompt is enough — no stitching.
Open with a sentence on the whole piece and what stays the same throughout,
then one paragraph per shot with its time span and, if wanted, its sound —
**up to five shots in 15 seconds**, two to four seconds each, covering the clip
without gaps:

```
SHOT 1 [0–3s]: … Sound: …
SHOT 2 [3–7s]: …
```

In a test on 7 Oct 2026 (15 s, 720p, 16:9, sound on, 0.615 USD), the model
played five such shots in order in a single clip and kept the same cup and
clouds across all five, through scene changes from a café to a mountain farm and
back. Stitch clips with `ffmpeg` only for 20–30 seconds, or when each shot must
start from its own product picture.

## When to use it

- Drafts, and most image-to-video work where the picture has to survive the
  motion.
- Seedance 2.5 when the clip must be longer than 15 s or it is the hero shot;
  Mini stops at 15 s and 720p.
- In the same test MiniMax H3 at 768P on Kie (0.20 USD for 5 s) gave the more
  dramatic motion but drifted further from the subject. Both are worth having.
