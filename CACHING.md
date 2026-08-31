# Why the image URLs carry `?v=N`

**GitHub caches a profile README's rendered output AND proxies every image
through camo, which caches separately from `raw.githubusercontent.com`.**

Measured on 2026-08-31: a corrected banner reached `raw` (verified by fetching
it and diffing the path data) while the org page kept showing the old one, and
a `mermaid` block that had been replaced by an `<img>` kept rendering as the
old empty mermaid frame — controls and all. Both were caches, not mistakes, and
both looked exactly like a change that had failed to save.

**So: when you change an asset, bump `?v=N` on its URL in `profile/README.md`.**
It changes the camo key, which forces a refetch, and editing the README
invalidates the page render at the same time.

⚠️ **Do not conclude an edit did not work because the page looks unchanged.**
Check the source of truth first:

```bash
curl -s https://raw.githubusercontent.com/samomdkku/.github/main/profile/assets/banner.svg | head -40
gh api repos/samomdkku/.github/contents/profile/README.md --jq '.content' | base64 -d
```

If those are right, the file is right and you are looking at a cache.
