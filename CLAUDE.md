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

## Photo text recognition

No automatic OCR. Flow relies on system-level text recognition:

1. Camera or photo library captures an image → displayed on `<canvas>`
2. iOS Live Text: long-press photo → select text → copy (on-device ML, works offline)
3. Android: similar system-level text selection on images
4. Paste into editor → edit → generate QR code

Tesseract.js was removed in v21 after proving unreliable for Chinese OCR on iOS Safari (WASM/LSTM inference issues). iOS Live Text produces substantially better results with zero dependencies.

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

## Security & Privacy

**Intended use:** Clinical text template management tool for licensed medical professionals. Assists in composing standardized EMR entries by providing linguistic templates transferred via QR code optical bridge.

**Architecture guarantees:**
- Zero server architecture — all data stays in browser `localStorage`, no data leaves the device
- No patient identity fields stored by design — templates use `___` placeholders, not real patient data
- Fully offline PWA — functions without network connectivity
- QR codes encode only the template text visible on screen; no hidden metadata

**User responsibilities:**
- Do NOT include patient names, medical record numbers, ID numbers, or other PHI in template text
- Replace all `___` placeholders with actual patient values AFTER entering the EMR
- Review and verify all text before finalizing in the EMR system
- Clear browser data when changing devices or leaving shared workstations

**Regulatory classification:** This software is a clinical documentation auxiliary tool. It does NOT perform diagnosis, treatment recommendation, drug dosing, lab analysis, or any form of automated medical decision-making. It is NOT a medical device under NMPA (CFDA) classification.
