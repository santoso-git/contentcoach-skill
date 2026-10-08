---
name: contentcoach
description: Generate images and short videos with Nano Banana 2 and 2.1, GPT Image 2.5, Grok Imagine, Kling 3.0, MiniMax H3, Grok Video, Seedance 2.0 Mini and Seedance 2.5, calling Kie AI and fal.ai directly with the user's own API keys, cheapest route first, with a price quote before anything expensive runs. Use when the user asks to generate, create or edit an image, a thumbnail, a blog header, a social image, a product shot or a mockup, to animate a picture or make a video clip, or mentions ContentCoach.
license: MIT
---

# ContentCoach — images and video with your own keys

Makes images and short videos by calling two providers, **Kie AI** and **fal.ai**,
over HTTP with `curl`. The user brings their own keys and pays the providers
directly; nothing goes through any other server. Every output lands in one flat
folder with a JSON sidecar recording how it was made.

Answer the user in their own language.

## Commands

The user can start a message with one of these words, after `/contentcoach` in
Claude Code or on its own anywhere. Anything else is an ordinary request.

| Command | What you do |
|---|---|
| `prompt <brief>` | Write the prompt for the brief (*Writing the prompt*), name the model, provider, size and price you would use, and **stop. Run nothing, call no provider.** The user edits it or says to run it; a run then follows the usual rules. Works whether auto mode is on or off. |
| `auto on` · `auto off` | Turn auto mode on or off: save `auto_prompt` as `true` or `false` (*Writing the prompt*) and confirm in one line. |
| `models` | List the models in the table under *Models*, one line each on what it is for, and which ones the set keys can run. |
| `prices` | Show *Cost at a glance*. No network call. |
| `balance` | Show the Kie balance (*Balance*). Say that fal has no balance lookup. |
| `help` | List these commands, one line each. |

`prompt` costs nothing but a reply. Use it whenever the user wants to see or
shape a prompt before paying for it. When the user asks in their own words —
"improve this prompt", "write it out", "always do that" — treat it as the
matching command.

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
| Image — the newer Nano Banana, half the price on Kie; on request or for cheap drafts | Nano Banana 2.1 | `nano-banana-2.1.md` |
| Image — readable text in the picture, transparent background; Sunburst for fine detail | GPT Image 2.5 (Flare, Sunburst) | `gpt-image-2.5.md` |
| Image — a different look | Grok Imagine 2.0 | `grok-imagine-2.md` |
| Video — default | Kling 3.0 | `kling-3.md` |
| Video — cheap with sound, keeps the subject from a start frame | Seedance 2.0 Mini | `seedance-2-mini.md` |
| Video — cheap, dramatic motion, or needs a seed; always returns sound, with no switch on either route | MiniMax H3 | `minimax-h3.md` |
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

## Writing the prompt

You can write the prompt the model gets out of the user's brief: subject,
light, camera, motion, sound, the way each recipe's *How to prompt it* says.
It costs nothing extra — no second model, no extra call, no extra key, just
your own reply. **But only when the user wants it.** By default the user's
words are the prompt.

**The setting** is `auto_prompt` in `generations/.contentcoach.json` in the
current project. Read it before the first job:

```bash
jq -r 'if has("auto_prompt") then .auto_prompt else "unset" end' generations/.contentcoach.json 2>/dev/null || echo unset
```

- **`true` — auto is on.** Write out every short brief before it runs.
- **`false` — the user said no.** Send their words as they are. Do not ask
  again.
- **`unset` — never asked.** The first time a short brief arrives (not a
  finished prompt), ask once, before running anything:

  > Want me to write this out into a full prompt first — light, camera,
  > composition — the way this model's guide recommends? I'd show it to you
  > before it runs. I can also do that automatically from now on (auto mode).
  > Yes, this time · Yes, always · No

  - *Yes, this time:* write it out, show it, run on their go-ahead. Leave the
    setting unset, and do not ask again in this conversation.
  - *Yes, always:* save `true`, then write it out.
  - *No:* save `false`, send their words as written, and tell them: "You can
    get a prompt written out any time with `prompt <what you want>`, and turn
    on auto mode later with `auto on`."

Save the setting with `jq` (create the file if it is missing):

```bash
mkdir -p generations; F=generations/.contentcoach.json
jq -n --argjson v true '{auto_prompt:$v}' > "$F"   # true or false
```

**Rules for writing a prompt** — in auto mode, after a yes, or for `prompt`:

1. **A finished prompt goes through untouched**, even in auto mode. If the
   user pasted a full prompt — for example one copied from the homepage — or
   says "exactly", "verbatim" or "as written", send it as given. Only the
   fields (ratio, resolution, duration, sound) are yours to set.
2. **Write in English, following the recipe's *How to prompt it*.** In short:
   - **Image:** subject, setting, light, lens or style, composition, and room
     for a headline if it is a header. Words in the picture go to GPT Image 2.5,
     in quotes, every word spelled out — and only words the user gave. **If
     the user gave no words, end the prompt with "No text, signs, labels or
     logos anywhere in the image."** Otherwise Nano Banana invents signs,
     prices and brand names in shops, cafés and hero images. A hero image for a
     landing page is a photograph, never a page layout with a headline, buttons
     or a menu — but leave the empty space a headline needs.
   - **Edit:** what changes, then "keep everything else exactly as it is", and
     name what must survive (the face, the label, the composition).
   - **Video:** subject, motion, setting, look, one camera move, and sound when
     sound is on. One continuous shot by default. For a short ad or story on
     Seedance 2.0 Mini, up to 15 s: a summary sentence, then a timed shot list
     (`models/seedance-2-mini.md`, *Several shots in one clip*).
   - Never invent a brand name, a tagline or a spoken line the user did not
     give.
3. **Never describe a logo, a face or a brand colour** (*Reference images*
   above). If the brief names a product page, fetch the product picture and
   pass it as a file.
4. **Show the prompt before it runs.** Put it in your reply; for anything that
   is quoted first (*Rules* 1 and 2), put it in the quote, so the yes covers the
   prompt as well as the price. In auto mode, show a single 1K image's prompt
   as you run it. The sidecar records the prompt exactly as sent.

What writing the prompt buys is control — no invented copy, the layout you
asked for — more than beauty; for a simple scene the difference is small
(before/after test, 9 Oct 2026).

Even without auto mode, every prompt you send — the user's own words or one
you wrote — is the one shown in the quote for a clip.

## Rules

1. **Quote before video.** Before every video run, state model, provider,
   duration, resolution, sound on or off, the cost in USD from the recipe, and
   the prompt you will send.
   Then stop and wait for an explicit yes. Quoting is not approval. **One yes
   covers exactly one run** — if the clip is wrong, quote again before a retry.
   Video costs roughly ten times an image; Seedance up to sixty.
2. **Quote before anything above a plain draft.** Several images at once,
   anything at 2K or 4K, or GPT Image 2.5 on fal: say the price first and
   wait for a yes. Any other single image at 1K needs no quote — say what it
   cost in the reply afterwards.
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
7. **Build all JSON with `jq -n --arg`** — the request body and the sidecar —
   never by string interpolation. Prompts contain quotes that corrupt a
   hand-built body.
8. **Every tool call starts a fresh shell.** Variables set in one call are gone
   in the next, so each block below starts by setting what it needs, and the
   job's name is pasted in as a literal (see *Running a job*). Poll one job per
   call, in loops of about 100 seconds; if it is still running, the block says
   so — run it again.

## Running a job

Every recipe gives the model id, the endpoint and the request body. Each job
goes through the same steps, one tool call each: build the body, submit, poll
and download, write the sidecar.

**Name the job first, once.** Run `date +%s` and choose
`NAME="{short-description}_{timestamp}"`, for example
`NAME="lighthouse-riso_1791400000"` — lowercase, hyphens inside the
description. Every block below and in the recipes starts with that line; paste
the same literal into each one, since a fresh shell has forgotten it. All of
the job's temporary files are `/tmp/cc-$NAME.*`, so two agents on one machine
never read each other's body or result.

**Kie AI — submit:**

```bash
NAME="NAME"
curl -sS -X POST https://api.kie.ai/api/v1/jobs/createTask \
  -H "Authorization: Bearer $KIE_API_KEY" -H "Content-Type: application/json" \
  --data @"/tmp/cc-$NAME.req.json" -o "/tmp/cc-$NAME.submit.json"
TID=$(jq -r '.data.taskId // empty' "/tmp/cc-$NAME.submit.json")
if [ -n "$TID" ]; then echo "task id: $TID"; else echo "SUBMIT FAILED — nothing to poll:"; jq -c '{code, msg}' "/tmp/cc-$NAME.submit.json"; fi
```

Stop on `SUBMIT FAILED`: there is no job, so do not poll. Read the error with
the table at the end of this file.

**Kie AI — poll and download** (fill in `NAME` and the task id):

```bash
NAME="NAME"; TID="TASK_ID"
REC="/tmp/cc-$NAME.rec.json"; ST=""
case "$TID" in ""|TASK_ID) echo "no task id — submit first"; ST=none;; esac
[ "$ST" = none ] || for i in $(seq 1 12); do
  curl -sS -H "Authorization: Bearer $KIE_API_KEY" \
    "https://api.kie.ai/api/v1/jobs/recordInfo?taskId=$TID" -o "$REC"
  ST=$(jq -r '.data.state // empty' "$REC")
  case "$ST" in success|fail) break;; esac
  sleep 8
done
case "$ST" in
  success)
    URL=$(jq -r '.data.resultJson // "{}"' "$REC" | jq -r '.resultUrls[0] // empty')
    EXT=$(printf '%s' "${URL%%\?*}" | sed -n 's/.*\.\([A-Za-z0-9]*\)$/\1/p' | tr 'A-Z' 'a-z')
    mkdir -p generations; OUT="generations/$NAME.${EXT:-png}"
    if [ -n "$URL" ]; then curl -sS -o "$OUT" "$URL" && echo "saved: $OUT"; else echo "no result URL:"; jq -r .data.resultJson "$REC"; fi
    jq -r '"cost: \(.data.creditsConsumed) credits = \((.data.creditsConsumed * 0.005 * 10000 | round) / 10000) USD"' "$REC" ;;
  fail) jq -r '"FAILED: \(.data.failMsg)"' "$REC" ;;
  none) ;;
  *) echo "still running (state: ${ST:-unknown}) — run this block again" ;;
esac
```

`resultJson` is a JSON **string**, so it is parsed twice. The file extension
comes from the result URL: Kie returns a `.jpg` for some image models even when
the recipe asks for nothing, so never assume `.png`. Cost is
`creditsConsumed × 0.005` USD, rounded as above (plain multiplication prints
`0.20500000000000002`). **`creditsConsumed` reads 0 while a job is waiting** —
it is not a free run; read it only after `success`.

Kie reports errors in the body: check `code`, not the HTTP status — a 429 or a
rejected job can arrive with HTTP 200. Keep Kie replies in files as above and
never pipe them through `echo`; `recordInfo` carries JSON inside JSON, and the
shell mangles its backslashes. Kie keeps results for 14 days, but download at
once anyway.

**fal.ai — submit to the queue:**

```bash
NAME="NAME"
curl -sS -X POST "https://queue.fal.run/MODEL_ID" \
  -H "Authorization: Key $FAL_KEY" -H "Content-Type: application/json" \
  --data @"/tmp/cc-$NAME.req.json" -o "/tmp/cc-$NAME.submit.json"
jq -r '.status_url // empty, .response_url // empty' "/tmp/cc-$NAME.submit.json" | grep . \
  || { echo "SUBMIT FAILED — nothing to poll:"; jq -c . "/tmp/cc-$NAME.submit.json"; }
```

**fal.ai — poll and download** (fill in `NAME` and the two URLs the submit printed):

```bash
NAME="NAME"; STATUS_URL="…"; RESPONSE_URL="…"
ST=""
case "$STATUS_URL" in https://*) ;; *) echo "no status URL — submit first"; ST=none;; esac
[ "$ST" = none ] || for i in $(seq 1 10); do
  ST=$(curl -sS -H "Authorization: Key $FAL_KEY" "$STATUS_URL" | jq -r '.status // empty')
  [ "$ST" = COMPLETED ] && break
  sleep 10
done
if [ "$ST" = COMPLETED ]; then
  RESP="/tmp/cc-$NAME.resp.json"
  curl -sS -H "Authorization: Key $FAL_KEY" "$RESPONSE_URL" -o "$RESP" -D "/tmp/cc-$NAME.headers.txt"
  URL=$(jq -r '.images[0].url // .video.url // empty' "$RESP")
  if [ -n "$URL" ]; then
    EXT=$(printf '%s' "${URL%%\?*}" | sed -n 's/.*\.\([A-Za-z0-9]*\)$/\1/p' | tr 'A-Z' 'a-z')
    mkdir -p generations; OUT="generations/$NAME.${EXT:-png}"
    curl -sS -o "$OUT" "$URL" && echo "saved: $OUT"
  else echo "FAILED:"; jq -c .detail "$RESP"; fi
  grep -i x-fal-billable-units "/tmp/cc-$NAME.headers.txt"
elif [ "$ST" != none ]; then
  echo "still running (status: ${ST:-unknown}) — run this block again"
fi
```

Use the URLs fal returns rather than building them: the poll address drops the
endpoint's sub-path (`…/kling-video/v3/pro/image-to-video` is polled at
`…/kling-video/requests/{id}`), and a hand-built one returns a 404 that looks
like a lost job. A `COMPLETED` body with `detail` instead of `images` or `video`
is a failed job — report the `detail`.

## Save it — right away

Result URLs expire within hours; the patterns above download at once into
`generations/` in the current project, flat, no subfolders, as
`generations/NAME.{ext}`.

Then write the sidecar, `generations/NAME.json`, beside it — same basename.
Build it with `jq -n` too, since the prompt is in it:

```bash
NAME="NAME"
jq -n --arg prompt "THE FULL PROMPT EXACTLY AS SENT" \
  --arg model "MODEL_ID" --arg provider "kie" \
  --argjson refs '[]' --argjson params '{"aspect_ratio":"16:9","resolution":"1K"}' \
  --argjson cost 0.04 --arg created "$(date -u +%Y-%m-%dT%H:%M:%SZ)" \
  '{model:$model, provider:$provider, prompt:$prompt, refs:$refs, params:$params,
    cost_usd:$cost, created:$created}' > "generations/$NAME.json"
```

- `model`: the provider's model id exactly as sent, e.g.
  `grok-imagine-image-2-0/text-to-image` or `bytedance/seedance-2-mini`.
- `provider`: `kie` or `fal`.
- `refs`: the local file names of any reference images or frames, `[]` when
  there are none.
- `params`: the fields you sent besides the prompt and the images; add
  `"audio": true` for a clip with sound.
- `cost_usd`: the real cost where the provider reports it (`creditsConsumed` ×
  0.005 on Kie, rounded; `x-fal-billable-units` on fal — its unit differs per
  model, see the recipe), otherwise the quoted one.
- `created`: the time the job finished, in UTC.

Show the user the saved path, and the image if you can display it.

## Cost at a glance

Prices in USD, from the providers' pages and real runs; the recipes say which
is which. Check the provider's pricing page before relying on them.

| Job | Kie | fal |
|---|---|---|
| Nano Banana 2, 1K · 2K · 4K | 0.04 · 0.06 · 0.09 | 0.08 · 0.12 · 0.16 |
| Nano Banana 2.1, 1K · 2K · 4K | **0.02** · 0.03 · 0.045 | 0.08 at 1K |
| GPT Image 2.5, 1K · 2K · 4K | 0.03 · 0.05 · 0.08 | **0.0136** at 1K · with a reference **0.021 · 0.028 · 0.039**, `medium` |
| Grok Imagine 2.0, 1k | 0.02 | 0.04 `low` · 0.06 `medium` |
| Kling 3.0, 5 s, no sound | 0.35 at 720p · 0.45 at 1080p | 0.56 at 1080p |
| MiniMax H3, 5 s | **0.20 at 768P** · 0.33 at 2K | 0.25 at 480P · 0.40 at 768P |
| Grok Video 1.5, 5 s at 480p | 0.06 | 0.41 |
| Seedance 2.0 Mini, 5 s, with sound | 0.10 at 480p · **0.21 at 720p** | — |
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
