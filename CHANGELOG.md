# Changelog

Changes to the skill, its recipes and its prices, newest first. Prices are in
USD and come from real runs unless marked as listed. To get the latest, run
`git -C ~/.contentcoach-skill pull`; the no-install route always reads `main`.

## 4 Oct 2026

- **New model: Seedance 2.0 Mini** (Kie AI). 4–15 seconds at 480p or 720p,
  with sound. Measured: 0.21 USD for 5 seconds at 720p. Kie calls this price a
  discount that ends on 7 Oct 2026 at 06:00 UTC, so it may rise after that.
- **MiniMax H3 now starts on Kie AI.** 768P costs 0.20 USD for 5 seconds
  (measured), half fal's price at the sizes both providers offer. fal is used
  only for 480P, 4K or a seed.
- **MiniMax H3 always returns sound**, on both providers. Neither has a switch
  for it, and it is not billed separately.
- Kie's upload host is `kieai.redpandaai.co`; uploaded files are kept for one
  to three days.

## 2 Oct 2026

- **Free catalogue lookups.** Between updates, the skill can check a
  provider's current price and fields in Kie AI's and fal.ai's free model
  catalogues: for a renamed id, a rejected field, a stale price or a model
  without a recipe.

## 27 Sep 2026

- **New model: GPT Image 2.5 Sunburst**, the slower variant with finer detail.
  Measured on Kie at 0.03 USD for 1K; a transparent background works.
- **GPT Image 2.5 now starts on fal.ai**, the cheaper route at every size:
  0.0136 USD for 1K from text, and 0.021, 0.028 and 0.039 USD for an edit at 1K,
  2K and 4K (measured, quality medium).
- More measured prices across the recipes: Seedance 2.5 on fal at 0.70 USD for
  5 seconds at 480p (listed at 1.10), Grok Imagine 2.0 on Kie at 0.02 USD.
- Kie's file upload for reference images is confirmed.
- Two new examples on the homepage, Nos. 9 and 10, made with the skill's own
  recipe.

## 26 Sep 2026

- **First release.** The skill calls Kie AI and fal.ai directly with your own
  key. Recipes for Nano Banana 2, GPT Image 2.5, Grok Imagine 2.0, Kling 3.0,
  MiniMax H3, Grok Video 1.5 and Seedance 2.5.
- Homepage at https://skill.contentcoach.se/ with a price list.
