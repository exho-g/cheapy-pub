# Vendored Tesseract.js OCR assets

Self-hosted (not fetched from a third-party CDN at runtime) so § 14 Receipt
Scanning works fully offline after its one-time first download — see
`src/utils/receiptOcr.js`, Docs.md § 14, and CLAUDE.md hard constraint #4.

- `worker.min.js` — from `node_modules/tesseract.js/dist/worker.min.js`
- `tesseract-core-*-lstm.wasm(.js)` — from `node_modules/tesseract.js-core`.
  All three variants (plain / SIMD / relaxed-SIMD) are shipped; Tesseract.js's
  own feature detection picks exactly one per device at runtime — a given
  user only ever downloads one pair, not all three.
- `lang-data/eng.traineddata.gz` — the "fast" (LSTM-only) English model,
  downloaded from `https://cdn.jsdelivr.net/npm/@tesseract.js-data/eng/4.0.0_best_int/eng.traineddata.gz`
  (Tesseract.js's own default source) and committed here instead of fetched
  live.

**License:** Tesseract.js and tesseract.js-core are Apache License 2.0
(https://github.com/naptha/tesseract.js). The English trained data is from
the Tesseract OCR project, also Apache 2.0
(https://github.com/tesseract-ocr/tessdata_fast).

**Not precached with the core app shell** (vite.config.js's Workbox
`globIgnores` excludes this whole directory) — instead served via a
`runtimeCaching` CacheFirst rule, fetched once on the first "Scan Receipt"
use and cached indefinitely after that. Deliberately not part of the
app-shell precache: ~10MB+ per device is too much to force onto every
install just for a feature most opens of the app won't touch.

**To upgrade:** re-run the copy commands from `node_modules/tesseract.js` /
`node_modules/tesseract.js-core` after bumping the `tesseract.js` npm
dependency, and re-download `eng.traineddata.gz` from the URL above if
Tesseract.js's own default model version changes.
