# TikTok → Reels Bot

Public GitHub Action that downloads trending TikTok videos (via `yt-dlp`) and
stages them for Instagram Reel + Facebook Reel publishing.

## What it does

1. **Downloads** high-quality trending TikTok videos with `yt-dlp`.
2. **Stages** the downloaded videos as GitHub Actions artifacts (with metadata).
3. **Publishes** to Instagram Reels (main target) and Facebook Reels via the
   [Meta Graph API](https://developers.facebook.com/docs/instagram-api/guides/reels) —
   uses a human-generated long-lived access token.

## ⚠️ One human step required

Meta's Graph API for Instagram + Facebook **content publishing** requires:
- An Instagram Professional account linked to a Facebook Page.
- A user access token with scopes:
  `instagram-contentpublish`, `pages_manage_posts`, `pages_read_engagement`.
- The token **expires** (~60 days for long-lived) and must be refreshed by a human
  through Facebook Login. There is no fully-headless refresh.

So the **download stage runs fully automated**; the **publish stage** runs as long
as `IG_ACCESS_TOKEN` / `FB_PAGE_ID` secrets are set. When the token expires, you
regenerate it via the `scripts/get-ig-token.md` guide below and update the
secrets.

## Setup

```bash
# 1. On your machine
git clone https://github.com/Walusimbi-Leon1/tiktok-reels-bot.git
cd tiktok-reels-bot

# 2. Add your Meta credentials as GitHub repo/organization secrets:
#    - IG_ACCESS_TOKEN   (long-lived user token, IG + FB scopes)
#    - IG_ACCOUNT_ID     (Instagram Business/Creator user id)
#    - FB_PAGE_ID        (linked Facebook Page id)
#    - IG_USERNAME       (optional: a TikTok trending account to source from)

# 3. Trigger
gh workflow run download-and-stage.yml        # download only
gh workflow run publish-reels.yml             # after download artifacts exist
```

## Schedule

`download-and-stage.yml` runs **daily at 09:00 UTC** and keeps the last 3
runs' artifacts. Manual `workflow_dispatch` also available.

## Files

- `.github/workflows/download-and-stage.yml` — yt-dlp download + stage.
- `.github/workflows/publish-reels.yml` — Meta Graph API publish (needs secrets).
- `scripts/` — helpers (token refresh guide, caption templates).
- `downloaded/` — local video output dir (gitignored).

## Publish token refresh (read-only guide)

See [`scripts/get-ig-token.md`](scripts/get-ig-token.md) for the step-by-step
to generate a long-lived IG+FB token.
