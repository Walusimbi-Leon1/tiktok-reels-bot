# TikTok → Reels Downloader

Public GitHub Action that downloads **20 trending TikTok videos** per run (via
`yt-dlp`) and publishes them as a **GitHub Release** — so you can go download
them manually and post to IG/FB yourself.

## How it works

1. **Downloads** the 20 hottest videos from the **For You feed** (`tiktok.com/foryou`)
   — no TikTok auth required.
2. Saves each video with its **real title** as the filename:
   `downloaded/<video title> [<id>].mp4`.
3. Builds a **`manifest.json`** with title / uploader / id / size per video.
4. Creates (or reuses) a dated GitHub Release like `daily-2026-08-14` and
   uploads the MP4s + manifest as release assets.

## Schedule

Runs **daily at 09:00 UTC** (keeps the last batch as a release).
Also triggerable on demand:

```bash
gh workflow run download-and-release.yml
```

## Find your videos

Go to the repo's **Releases** tab:
https://github.com/Walusimbi-Leon1/tiktok-reels-bot/releases

Each release = one batch of 20 videos, named `daily-YYYY-MM-DD`.
Download any MP4, post it to Instagram/Facebook reels manually.

## Run history

Check a specific run:
https://github.com/Walusimbi-Leon1/tiktok-reels-bot/actions

## Files

- `.github/workflows/download-and-release.yml` — the whole pipeline.
- `README.md` — this file.

## Notes

- Videos are the **highest quality available ≤1080p** (avoids TikTok's 720p
  throttles); remuxed to clean `.mp4` for reel compatibility.
- yt-dlp respects TikTok's rate limits (`--sleep-interval`); retries are
  infinite on failure.
- The download job uses the auto-provided `GITHUB_TOKEN` (no extra secrets
  needed — the repo is public).
