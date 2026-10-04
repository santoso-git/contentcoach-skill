---
name: contentcoach
description: Generate images and short videos with Nano Banana 2, GPT Image 2.5, Grok Imagine, Kling 3.0, MiniMax H3, Grok Video, Seedance 2.0 Mini and Seedance 2.5, calling Kie AI and fal.ai directly with the user's own API keys, cheapest route first, with a price quote before anything expensive runs. Use when the user asks to generate, create or edit an image, a thumbnail, a blog header, a social image, a product shot or a mockup, to animate a picture or make a video clip, or mentions ContentCoach.
license: MIT
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
for v in KIE_API_KEY FAL_KEY; do printenv "$v" >/dev/null && echo "$v set" || echo "$v missing"; done
```

Tools you need: `curl` and `jq`. If `jq` is missing, tell the user to install it
(`brew install jq` on a Mac, the package manager on Linux).

## Models

**Read the recipe before every generation.** Do not call an API from memory: the
recipe holds the endpoint, the body shape and the traps. The recipes sit next to
this file in `models/`. If you are reading this from a URL rather than an
installed copy, fetch them from
`https://raw.githubusercontent.com/santoso-git/contentcoach-skill/main/contentcoach/models/<file>`.

| Task | Model | Recipe |
|---|---|---|
| Image — default, drafts, edits with references | Nano Banana 2 | `nano-banana-2.md` |
| Image — readable text in the picture, transparent background; Sunburst for fine detail | GPT Image 2.5 (Flare, Sunburst) | `gpt-image-2.5.md` |
| Image — a different look | Grok Imagine 2.0 | `grok-imagine-2.md` |
| Video — default | Kling 3.0 | `kling-3.md` |
| Video — cheap with sound, keeps the subject from a start frame | Seedance 2.0 Mini | `seedance-2-mini.md` |
| Video — cheap, dramatic motion, or needs a seed | MiniMax H3 | `minimax-h3.md` |
| Video — cheapest of all on Kie, Grok's look, or a person from a photo | Grok Imagine Video 1.5 | `grok-imagine-video-1-5.md` |
| Video — hero shot, or longer than 15 s | Seedance 2.5 | `seedance-2.5.md` |

Before any reference image, and before handing a still to a video model, read
`models/providers.md`. A still means three different things to a video model, and the
two providers disagree on nearly every convention.

## Routing

Cheapest route first, and **say which route ran and why** in the reply.

- **Kie first** when `KIE_API_KEY` is set. It is cheaper for nearly everything.
- **fal** when only `FAL_KEY` is set, when Kie fails, or when the job needs
  something only fal does: a seed on Nano Banana, Grok Imagine edits with
  your own pictures, or MiniMax H3 at 480P, 4K or with a seed. One exception
  starts on fal because it is cheaper there: **GPT Image 2.5** (0.0136–0.039
  USD measured at every size, against 0.03–0.08 on Kie).
- If the only key set cannot run the job, say so and name the key that would.

Never hide a swap between providers or models.

## Reference images

Anything the result must contain exactly — a logo, a product, a face, a photo to
edit — goes in as a file. **Never describe a logo, a face or a brand colour in
words**; the model gets it wrong every time. If a needed reference is missing,
ask for it rather than approximating.

- **fal** takes a base64 data URI directly. No upload.
- **Kie** takes only public HTTPS URLs. Upload the local file first with the
  user's own Kie key and use the URL right away; Kie keeps uploads for one to
  three days. The host is `kieai.redpandaai.co` — the same path on `api.kie.ai`
  returns 404, whatever Kie's docs show:

```bash
curl -sS -X POST https://kieai.redpandaai.co/api/file-stream-upload \
  -H "Authorization: Bearer $KIE_API_KEY" \
  -F "file=@logo.png" -F "uploadPath=contentcoach-refs" | jq -r .data.downloadUrl
```

Confirmed 27 Sep 2026: the upload returns a public URL on
`tempfile.redpandaai.co` that anyone with the link can fetch, so upload nothing
the user would not want reachable while it lasts.

Downscale files over about 4 MB first (`sips -Z 2048 file.png` on macOS).

## Rules

1. **Quote before video.** Before every video run, state model, provider,
   duration, resolution, sound on or off, and the cost in USD from the recipe.
   Then stop and wait for an explicit yes. Quoting is not approval. **One yes
   covers exactly one run** — if the clip is wrong, quote again before a retry.
   Video costs roughly ten times an image; Seedance up to sixty.
2. **Quote before anything above a plain draft.** Several images at once,
   anything at 2K or 4K, or GPT Image 2.5 on fal: say the price first and
   wait for a yes.
3. **Draft cheap, finish pretty.** Iterate at 1K. Rerun only the winning prompt
   at 2K or 4K once the user has picked a favourite. Never draft at 4K.
4. **Always send the fields whose defaults are dear.** Several models default to
   their most expensive resolution, quality or audio setting. The recipes say
   which.
5. **One at a time.** Run generations sequentially, not in parallel.
6. **Never loop on a failing request.** Every accepted submit is billed. On
   `model not found` the id has been renamed: look it up in the provider's free
   catalogue (`models/providers.md`, *Checking a price or a field live*) or on
   its model page, copy the id fresh, and run once. Do not probe candidate ids.
7. **Build request JSON with `jq -n --arg`**, never by string interpolation.
   Prompts contain quotes that corrupt a hand-built body.
8. **Every tool call starts a fresh shell.** Variables set in one call are gone
   in the next, so each block below starts by setting what it needs. Poll one
   job per call, in loops of about 100 seconds; if it is still running, run
   the poll block again.

## Running a job

Every recipe gives the model id, the endpoint and the request body. The body is
built with `jq -n --arg` into `/tmp/cc-req.json`; then one of these three
patterns sends it, waits and downloads. Choose the output name first,
`generations/{short-description}_{unix-timestamp}.{ext}`, and reuse it.

**Kie AI — submit:**

```bash
curl -sS -X POST https://api.kie.ai/api/v1/jobs/createTask \
  -H "Authorization: Bearer $KIE_API_KEY" -H "Content-Type: application/json" \
  --data @/tmp/cc-req.json | tee /tmp/cc-submit.json | jq -r '.data.taskId // empty'
# no task id printed: read /tmp/cc-submit.json for the error
```

**Kie AI — poll and download** (fill in the two values):

```bash
TID="TASK_ID"; OUT="generations/NAME.png"
for i in $(seq 1 12); do
  curl -sS -H "Authorization: Bearer $KIE_API_KEY" \
    "https://api.kie.ai/api/v1/jobs/recordInfo?taskId=$TID" -o /tmp/cc-rec.json
  ST=$(jq -r '.data.state' /tmp/cc-rec.json)
  case "$ST" in success|fail) break;; esac
  sleep 8
done
echo "state: $ST"
if [ "$ST" = success ]; then
  mkdir -p generations
  curl -sS -o "$OUT" "$(jq -r '.data.resultJson // "{}"' /tmp/cc-rec.json | jq -r '.resultUrls[0] // empty')"
fi
jq '.data | {creditsConsumed, failMsg}' /tmp/cc-rec.json
```

`resultJson` is a JSON **string**, so it is parsed twice. Cost is
`creditsConsumed × 0.005` USD.

Kie reports errors in the body: check `code`, not the HTTP status — a 429 or a
rejected job can arrive with HTTP 200. Keep Kie replies in files as above and
never pipe them through `echo`; `recordInfo` carries JSON inside JSON, and the
shell mangles its backslashes. Kie keeps results for 14 days, but download at
once anyway.

**fal.ai — submit to the queue:**

```bash
curl -sS -X POST "https://queue.fal.run/MODEL_ID" \
  -H "Authorization: Key $FAL_KEY" -H "Content-Type: application/json" \
  --data @/tmp/cc-req.json | tee /tmp/cc-submit.json | jq -r '.status_url, .response_url'
```

**fal.ai — poll and download** (paste the two URLs the submit printed):

```bash
STATUS_URL="…"; RESPONSE_URL="…"; OUT="generations/NAME.png"
for i in $(seq 1 10); do
  ST=$(curl -sS -H "Authorization: Key $FAL_KEY" "$STATUS_URL" | jq -r .status)
  [ "$ST" = COMPLETED ] && break
  sleep 10
done
echo "status: $ST"
if [ "$ST" = COMPLETED ]; then
  curl -sS -H "Authorization: Key $FAL_KEY" "$RESPONSE_URL" -o /tmp/cc-resp.json -D /tmp/cc-headers.txt
  URL=$(jq -r '.images[0].url // .video.url // empty' /tmp/cc-resp.json)
  if [ -n "$URL" ]; then mkdir -p generations; curl -sS -o "$OUT" "$URL"; else jq .detail /tmp/cc-resp.json; fi
  grep -i x-fal-billable-units /tmp/cc-headers.txt
fi
```

Use the URLs fal returns rather than building them: the poll address drops the
endpoint's sub-path (`…/kling-video/v3/pro/image-to-video` is polled at
`…/kling-video/requests/{id}`), and a hand-built one returns a 404 that looks
like a lost job. A `COMPLETED` body with `detail` instead of `images` or `video`
is a failed job — report the `detail`.

## Save it — right away

Result URLs expire within hours; the patterns above download at once into
`generations/` in the current project. Keep it flat, no subfolders, with names
like `{short-description}_{unix-timestamp}.{ext}`, lowercase with hyphens inside
the description.

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
| GPT Image 2.5, 1K · 2K · 4K | 0.03 · 0.05 · 0.08 | **0.0136** at 1K · with a reference **0.021 · 0.028 · 0.039**, `medium` |
| Grok Imagine 2.0, 1k | 0.02 | 0.04 `low` · 0.06 `medium` |
| Kling 3.0, 5 s, no sound | 0.35 at 720p · 0.45 at 1080p | 0.56 at 1080p |
| MiniMax H3, 5 s | **0.20 at 768P** · 0.33 at 2K | 0.25 at 480P · 0.40 at 768P |
| Grok Video 1.5, 5 s at 480p | 0.06 | 0.41 |
| Seedance 2.0 Mini, 5 s, with sound | 0.10 at 480p · **0.21 at 720p** (discount until 7 Oct 2026) | — |
| Seedance 2.5, 5 s | 0.70 at 480p · 1.58 at 720p | 0.70 at 480p · 2.37 at 720p (listed) |

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
