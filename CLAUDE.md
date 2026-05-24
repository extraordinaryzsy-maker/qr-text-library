# QR 文本库

Clinical medical text QR-code library PWA. Stores structured text templates (post-op notes, admission records, surgical reports, etc.), renders QR codes for sharing, works fully offline.

## URLs

- **Production**: https://extraordinaryzsy-maker.github.io/qr-text-library/
- **Repo**: git@github.com:extraordinaryzsy-maker/qr-text-library.git

## Architecture

- **index.html** — The entire app (single file). QRCode.js is inlined (~23KB), presets hardcoded in `getPresets()`, no external network requests.
- **sw.js** — Service Worker for offline caching (cache-first with network update).
- **templates.json** — Reference copy of preset data (not used at runtime; presets are inline).
- **Data** — All user data stored in `localStorage` key `qrlib_v2`. No server-side storage.

## How to update & deploy

1. Edit `index.html` (or `sw.js`, `templates.json`)
2. Commit: `git add -A && git commit -m "..." && git push`
3. GitHub Pages auto-deploys from `main` — live in ~30s

## Local testing

```
cd /Users/docz/QR4EMR/qr-library
python3 -m http.server 3000 --bind 0.0.0.0
```
Then open http://localhost:3000 or http://192.168.1.13:3000 (same WiFi).

## Presets

Hardcoded in `getPresets()` function (~line 448). Categories: 术后记录, 入院记录, 手术记录, 出院小结, 会诊记录, 检验报告, 影像报告, 病程记录, 知情同意, 医嘱模板.

## Key design decisions

- QRCode.js inlined to eliminate CDN dependency
- No server required — all data is client-side
- File import (FileReader) replaced HTTP fetch for template loading
- SSH push preferred — HTTPS push may fail due to local proxy
