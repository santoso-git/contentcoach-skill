---
name: contentcoach
description: Generate images and short videos with Nano Banana 2, GPT Image 2.5, Grok Imagine, Kling 3.0, MiniMax H3, Grok Video and Seedance 2.5, calling Kie AI and fal.ai directly with the user's own API keys, cheapest route first, with a price quote before anything expensive runs. Use when the user asks to generate, create or edit an image, a thumbnail, a blog header, a social image, a product shot or a mockup, to animate a picture or make a video clip, or mentions ContentCoach.
---

# ContentCoach — images and video with your own keys

Makes images and short videos by calling two providers, **Kie AI** and **fal.ai**,
over HTTP with `curl`. The user brings their own keys and pays the providers
directly; nothing goes through any other server. Every output lands in one flat
folder with a JSON sidecar recording how it was made.

Answer the user in their own language.

## Keys

Two environment variables. **One is enough; both give a fallback.**

| Variable | Get it at | Used for |
|---|---|---|
| `KIE_API_KEY` | https://kie.ai/api-key — top up credits first | Cheapest route for almost every model |
| `FAL_KEY` | https://fal.ai/dashboard/keys — add a payment method first | Fallback, base64 references, and a few things only fal does |

If neither is set, stop and tell the user to create a key at one of the links
above and put it in their shell profile (`~/.zshrc` on a Mac, `~/.bashrc` on
Linux), then restart the agent:

```bash
export KIE_API_KEY="…"
export FAL_KEY="…"
```

Never ask the user to paste a key into the chat. Never write a key into a file,
a commit, a log or your reply. Send `KIE_API_KEY` only to `api.kie.ai` and
`kieai.redpandaai.co`, and `FAL_KEY` only to `fal.run` and `queue.fal.run`.

Check what is set without printing the values:

```bash
for v in KIE_API_KEY FAL_KEY; do [ -n "${!v}" ] && echo "$v set" || echo "$v missing"; done
```

(`${!v}` is bash; in zsh use `${(P)v}`.)

## Models

**Read the recipe before every generation.** Do not call an API from memory: the
recipe holds the endpoint, the body shape and the traps. The recipes sit next to
this file in `models/`. If you are reading this from a URL rather than an
installed copy, fetch them from
`https://raw.githubusercontent.com/santoso-git/contentcoach-skill/main/contentcoach/models/<file>`.

| Task | Model | Recipe |
|---|---|---|
| Image — default, drafts, edits with references | Nano Banana 2 | `nano-banana-2.md` |
| Image — readable text in the picture, transparent background | GPT Image 2.5 | `gpt-image-2.5.md` |
| Image — a different look | Grok Imagine 2.0 | `grok-imagine-2.md` |
| Video — default | Kling 3.0 | `kling-3.md` |
| Video — cheapest, or needs a seed | MiniMax H3 | `minimax-h3.md` |
| Video — Grok's look, or a person from a photo | Grok Imagine Video 1.5 | `grok-imagine-video-1-5.md` |
| Video — hero shot, or longer than 15 s | Seedance 2.5 | `seedance-2.5.md` |

Before any reference image, and before handing a still to a video model, read
`models/providers.md`. A still means three different things to a video model, and the
two providers disagree on nearly every convention.

## Routing

Cheapest route first, and **say which route ran and why** in the reply.

- **Kie first** when `KIE_API_KEY` is set. It is cheaper for nearly everything.
- **fal** when only `FAL_KEY` is set, when Kie fails, or when the job needs
  something only fal does: a seed on Nano Banana, Grok Imagine edits with
  your own pictures, or MiniMax H3 at 480P or 4K.
- If the only key set cannot run the job, say so and name the key that would.

Never hide a swap between providers or models.

## Reference images

Anything the result must contain exactly — a logo, a product, a face, a photo to
edit — goes in as a file. **Never describe a logo, a face or a brand colour in
words**; the model gets it wrong every time. If a needed reference is missing,
ask for it rather than approximating.

- **fal** takes a base64 data URI directly. No upload.
- **Kie** takes only public HTTPS URLs. Upload the local file first with the
  user's own Kie key; the file is deleted after at most a few days:

```bash
curl -sS -X POST https://kieai.redpandaai.co/api/file-stream-upload \
  -H "Authorization: Bearer $KIE_API_KEY" \
  -F "file=@logo.png" -F "uploadPath=contentcoach-refs" | jq -r .data.downloadUrl
```

Downscale files over about 4 MB first (`sips -Z 2048 file.png` on macOS).

## Rules

1. **Quote before video.** Before every video run, state model, provider,
   duration, resolution, sound on or off, and the cost in USD from the recipe.
   Then stop and wait for an explicit yes. Quoting is not approval. **One yes
   covers exactly one run** — if the clip is wrong, quote again before a retry.
   Video costs roughly ten times an image; Seedance up to sixty.
2. **Quote before anything above a plain draft.** Several images at once,
   anything at 2K or 4K, or GPT Image 2.5 on fal: say the price first.
3. **Draft cheap, finish pretty.** Iterate at 1K. Rerun only the winning prompt
   at 2K or 4K once the user has picked a favourite. Never draft at 4K.
4. **Always send the fields whose defaults are dear.** Several models default to
   their most expensive resolution, quality or audio setting. The recipes say
   which.
5. **One at a time.** Run generations sequentially, not in parallel.
6. **Never loop on a failing request.** Every accepted submit is billed. On
   `model not found` the id has been renamed: open the provider's model page,
   copy the id fresh, and run once. Do not probe candidate ids.
7. **Build request JSON with `jq -n --arg`**, never by string interpolation.
   Prompts contain quotes that corrupt a hand-built body.
8. **Poll one job per tool call** with a bounded loop, so a long render cannot
   blow a tool timeout.

## Save it — right away

Result URLs expire within hours. Download immediately into `generations/` in the
current project: flat, no subfolders, named
`{short-description}_{unix-timestamp}.{ext}`, lowercase with hyphens inside the
description.

Then write `NAME.json` beside `NAME.ext` — same basename:

```json
{
  "model": "nano-banana-2",
  "provider": "kie",
  "prompt": "the full text prompt exactly as sent",
  "refs": ["logo.png"],
  "params": { "aspect_ratio": "16:9", "resolution": "1K" },
  "cost_usd": 0.04,
  "created": "2026-09-26T09:41:00Z"
}
```

Record the real cost where the provider reports it (`creditsConsumed` × 0.005
on Kie, `x-fal-billable-units` on fal — its unit differs per model, see the
recipe), otherwise the quoted one. Show the user the saved path, and the image
if you can display it.

## Cost at a glance

Prices in USD, from the providers' pages and real runs; the recipes say which
is which. Check the provider's pricing page before relying on them.

| Job | Kie | fal |
|---|---|---|
| Nano Banana 2, 1K · 2K · 4K | 0.04 · 0.06 · 0.09 | 0.08 · 0.12 · 0.16 |
| GPT Image 2.5, 1K · 2K · 4K | 0.03 · 0.05 · 0.08 | about 0.05 · 0.11 · 0.18 at `medium` |
| Grok Imagine 2.0, 1k | 0.02 | 0.04 `low` · 0.06 `medium` |
| Kling 3.0, 5 s, no sound | 0.35 at 720p · 0.45 at 1080p | 0.56 at 1080p |
| MiniMax H3, 5 s | 0.40 at 768P | **0.25 at 480P** · 0.40 at 768P |
| Grok Video 1.5, 5 s at 480p | 0.06 | 0.41 |
| Seedance 2.5, 5 s | 0.70 at 480p · 1.58 at 720p | 1.10 at 480p · 2.37 at 720p |

## Balance

```bash
curl -sS -H "Authorization: Bearer $KIE_API_KEY" -H "Content-Type: application/json" \
  https://api.kie.ai/api/v1/chat/credit     # Content-Type is required, even on a GET
```

fal has no balance endpoint for ordinary keys. An empty fal account shows up as
`403 User is locked. Reason: TOP_UP`; a rejected job on a valid Kie key is also
usually an empty balance, not a bad key. Check the balance before blaming auth.

## Errors

| What you see | Meaning |
|---|---|
| 401, or Kie `200` with `401` in the body | Key missing or wrong. On Kie's balance check, a missing `Content-Type` header also does this. |
| 402, 403 `TOP_UP`, or "insufficient credits" | Empty balance. Tell the user to top up at the provider. |
| 422 "model … not supported" / `model not found` | Renamed id. See rule 6. |
| fal `COMPLETED` but the body has `detail` instead of `images` or `video` | The job failed on the caller's side, usually a reference fal could not download. Report the `detail` text. |
| Kie `state: fail` | Read `failMsg` and tell the user. Try once more at most. |
