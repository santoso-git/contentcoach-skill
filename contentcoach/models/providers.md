# Kie AI and fal.ai — what differs

Two providers carrying the same model families, and almost no agreement between
them. Copying a value from one provider's recipe to the other's is the most
reliable way to break a call — often silently, with a billed result that
ignored part of the request.

## The compatibility table

| | Kie AI | fal.ai |
|---|---|---|
| Auth header | `Authorization: Bearer $KIE_API_KEY` | `Authorization: Key $FAL_KEY` — the word `Key` |
| Body | nested in `input` | flat |
| Method | always async: `jobs/createTask`, poll `jobs/recordInfo` | queue: submit to `queue.fal.run/<model>`, poll the returned `status_url` |
| Result | `data.resultJson` — **a JSON string**, parse twice | `images[0].url` or `video.url` |
| References | public HTTPS URLs only — upload first | URLs **or** base64 data URIs |
| Unknown fields | **ignored silently** | usually rejected |
| Seed | **accepted and ignored** | honoured where the model has one |
| Reports cost | `creditsConsumed`, 1 credit = 0.005 USD | `x-fal-billable-units` header — **unit differs per model** |
| Balance | `GET /api/v1/chat/credit` (needs `Content-Type`) | none for ordinary keys; empty shows as `403 TOP_UP` |
| Catalogue | https://kie.ai/market · free API, see below | https://fal.ai/explore/search · free API, see below |

**`x-fal-billable-units` is priced per model.** One unit is 0.08 USD on Nano
Banana 2, one second on Kling, 0.01 USD on Grok Imagine, and **one whole US
dollar on GPT Image 2.5**. Never carry a unit rate from one model to another.

**fal's queue strips the sub-path on polls.** Submit to
`queue.fal.run/fal-ai/kling-video/v3/pro/image-to-video`, but poll
`queue.fal.run/fal-ai/kling-video/requests/{id}/status`. Use the `status_url` and
`response_url` the submit returns rather than building them by hand.

**fal does not always report a cost.** `x-fal-billable-units` came back on
Grok Imagine and GPT Image 2.5, but a Kling queue job carried none. When it is
missing, record the quoted cost and say so.

**fal's `COMPLETED` does not mean it produced anything.** When a job fails for a
reason fal blames on the caller — usually a reference it could not download —
the body carries `detail` instead of `images` or `video`. Check `detail` before
reporting a missing URL.

**Kie's docs are readable as markdown** at `https://kie.ai/model/<model>.md`,
but kie.ai returns 403 to plain fetch tools. Use `curl -A 'Mozilla/5.0 …'`.
Those pages give field names but no prices; the reliable price is
`creditsConsumed` from a real run.

## Checking a price or a field live

The recipes hold measured prices and field names; use those to quote. When
something looks off — a `model not found`, a field the provider rejects, a price
that seems stale, or a model the user names that has no recipe — both providers
have **free catalogue lookups**. They never generate anything and never bill.

**Kie** (`Authorization: Bearer $KIE_API_KEY`). The four lookups share **one
request per second** per account; over that, the reply is `code: 429` inside an
HTTP 200. Fetch the catalogue once and filter locally.

```bash
K="Authorization: Bearer $KIE_API_KEY"
curl -sS -H "$K" "https://api.kie.ai/api/v1/models?q=kling" -o /tmp/cc-cat.json   # also taskType=Text%20to%20Video, provider=
jq -r '.data.models[] | "\(.model)\t\(.pricingDesc | split("\n")[0])"' /tmp/cc-cat.json
curl -sS -H "$K" "https://api.kie.ai/api/v1/models/nano-banana-2/price" | jq -r '.data.pricingDesc'
curl -sS -H "$K" "https://api.kie.ai/api/v1/models/kling-3.0/video/schema" | jq '.data.openapi'
```

`pricingDesc` is prose that matches what Kie bills. An id with a slash goes in
unencoded, as above. The schema can be `null` for a model Kie has not synced;
`/success-rate` on the same path shows the last 24 hours, null meaning no
traffic.

**fal** (`Authorization: Key $FAL_KEY`). The id must be the full endpoint path —
`openai/gpt-image-2.5/sunburst/text-to-image`, not `…/sunburst`.

```bash
F="Authorization: Key $FAL_KEY"
curl -sS -H "$F" "https://api.fal.ai/v1/models/pricing?endpoint_id=fal-ai/nano-banana-2" | jq '.prices'
curl -sS -H "$F" "https://api.fal.ai/v1/models?endpoint_id=fal-ai/nano-banana-2&expand=openapi-3.0" \
  | jq '.models[0] | {status: .metadata.status, openapi}'
# search: https://api.fal.ai/v1/models?q=kling
```

Three traps in fal's catalogue:

- **It answers any well-formed id**, real or invented, with a stub priced per
  "compute second". Only an entry whose `metadata.status` is set is a real model.
- **Token-billed models are useless here.** GPT Image 2.5 comes back as
  `unit_price: 1` per "units" — one dollar per unit, no quote possible — and
  Seedance 2.5 is priced per 1,000 tokens. Use the recipe's measured prices.
- **A burst is throttled with an error reply.** Wait and retry; it does not mean
  there is no price.

A catalogue price is a list price. When a run reports a different figure,
`creditsConsumed` or `x-fal-billable-units` wins, and the recipe should be fixed
from it.

## A still means three things to a video model

| Route | First frame | Last frame alone | Likeness reference |
|---|---|---|---|
| Kling 3.0 · Kie | `image_urls[0]` | no | no |
| Kling 3.0 · fal | `start_image_url` | no | no |
| MiniMax H3 · fal | `image_url`, optional | **yes** | no |
| MiniMax H3 · Kie | `first_frame_url`, optional | **yes** | `reference-to-video` endpoint |
| Seedance 2.0 Mini · Kie | `first_frame_url`, optional | **yes** | `reference_image_urls[]` |
| Seedance 2.5 · fal | `image_url`, required | no | no |
| Seedance 2.5 · Kie | `first_frame_url`, optional | **yes** | `reference_image_urls[]` |
| Grok Video 1.5 · Kie | — | no | `image_urls[]`, up to 7 |
| Grok Video 1.5 · fal | `image-to-video` | no | `reference-to-video` |

**A first frame is not a reference.** It is rendered as literal frame one. Pass a
subject photo as a frame and the clip opens on that exact photograph. When the
user wants "this person, new scene", use a likeness route.

**An end frame needs the image-to-video endpoint.** `text-to-video` has no
end-frame field and drops it silently while billing in full.

## The same name is not always the same model

A provider can carry an older version under the family name — Kie's `hailuo/…`
are Hailuo 02 and 2.3, not MiniMax H3. And a model can look absent because of a
spelling difference. Check the version on the provider's own model page before
routing.

## Adding a model

1. Copy the id fresh from the provider's model page. Ids churn.
2. Write `models/<name>.md` in the shape of an existing recipe: table, endpoint,
   fields, a full `curl`, cost, notes.
3. Add a row to the model table in `SKILL.md`.
4. Run it once and correct the recipe from what actually came back.

**Never probe candidate model ids in a loop.** Every accepted submit is billed.
