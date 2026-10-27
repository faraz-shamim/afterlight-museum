# Afterlight

**The medicine memory museum.** Follow a fictional whisper, find documented historical evidence with an open model on your own device, and leave a lantern lit.

Built from 27 October 2026 for [Hacktoberfest Week 4: Ghost in the Machine](https://dev.to/challenges/hacktoberfest-week4-2026-10-26). Target partner category: **Best Use of Render** only.

![An imagined medical-history museum with a lantern-bearing ghost](web/public/art/afterlight-hero.webp)

[Open the live museum](https://afterlight-museum.onrender.com/) · [Verified deployment](docs/deployment.json) · [Independent Linux CI](https://github.com/faraz-shamim/afterlight-museum/actions/runs/40442205177)

## The experience

Six original rooms explore medical history through short evidence puzzles: Lind's citrus comparison, the Ether Dome, John Snow's water investigation, Lister's antiseptic system, the development of penicillin, and the worldwide smallpox eradication effort. Dramatized voices are explicitly fictional, never attributed quotations or real patients' testimony. Original exhibit summaries link to institutional sources and include curator notes about historical interpretation.

1. Enter any room and read the whisper.
2. Use the plain-language clue or your own clue to search the six-exhibit archive with local MiniLM inference.
3. Inspect a suggested exhibit, its curator note and linked source. The similarity score is neither confidence nor a historical verdict.
4. Select evidence and a careful interpretation. The bounded verification tool checks the two selections against the authored puzzle.
5. Keep your six lanterns in a local passport. Export/import your progress; no account or streak required.

The full archive is also available without AI, so a failed download never locks visitors out of the museum. The AI-assisted path genuinely embeds text and ranks evidence; manual browsing is labeled separately.

## Open model and architecture

`Xenova/all-MiniLM-L6-v2`, revision `751bff37182d3f1213fa05d7196b954e230abad9`, Apache-2.0, q8 / CPU WASM, 384-dimensional mean-pooled normalized embeddings. Transformers.js 4.3.1 and the matching ONNX runtime are pinned. Tokenizer JSON and model requests use the immutable revision. Runtime notices ship with the app at `/licenses/`.

The model encodes each exhibit's subject, title and summary plus the visitor's clue. Cosine similarity orders the six results. The guide exposes the actual steps, embedding duration and revision. It does not generate answers or select the visitor's final evidence.

```text
Fictional whisper → visitor clue → browser-local MiniLM → ranked source cards
                                                ↓
                          human chooses evidence + interpretation
                                                ↓
                       Render /api/verify → authored result → local lantern
                            ↳ on-device equivalent when unavailable
```

Render serves the guide's front end, compressed WASM assets and stateless evidence-verification tool. The tool accepts only `{roomId, exhibitId, choice}`; it rejects unknown fields and IDs. It verifies an authored game answer, not a clinical claim. No search text or vectors are sent to the tool. No uploads, model API keys, database, paid workers or server-side personal records.

## Privacy, offline use and limits

- First use needs internet for the app and model (roughly 23 MB weights plus runtime). The model provider and host receive ordinary download/request metadata, such as IP address. This is not an anonymity claim.
- The tested cached workflow survives an offline reload, including genuine inference and on-device verification. Browser storage eviction, unsupported WASM features or private-mode restrictions can prevent this. External source links need a connection.
- Local storage contains room IDs. Search text and vectors are held in memory, not intentionally persisted by the app. Passports are plain, editable JSON, not encrypted or certified scores. Browser-level form/autofill behavior is outside this application's guarantees.
- No analytics, external fonts, autoplay audio, microphone, camera, location tracking or ElevenLabs. Keyboard controls and reduced-motion styling are supported.
- Six exhibits are a deliberately tiny archive. Ranking always returns the finite collection, even for irrelevant clues. The app provides no open-ended medical answers. A matching puzzle answer does not establish general historical or medical truth.
- This is an educational history game, not clinical software. No diagnosis, treatment advice, real patient records, validated educational benefit or clinical performance claims.

## Evaluation

18 unit/API tests cover all 108 room/exhibit/answer combinations, validation, progress imports, ranking math, headers, malformed/oversized requests and rejection of extra text without echoing it. 19 browser checks exercise real model inference, all six room flows, wrong answers, actual verification requests, sources, passport download/restore, reload persistence, confirmed reset, mobile layouts, inert input, offline reload, keyboard completion, pinned requests and a synthetic-text network check.

The [retrieval diagnostic](docs/retrieval-diagnostic.json) records all twelve development smoke probes: six original poetic whispers and six plain paraphrases. **11/12 top-one; 12/12 top-three.** The smallpox whisper ranks penicillin first and smallpox third. These are development examples over six curated exhibits, not an independent holdout or a validated accuracy benchmark. No learning-outcome study has been run. [Local browser report](docs/browser-qa.json) · [Public Render browser report](docs/live-qa.json) · [Live verification-tool check](docs/render-tool-check.json).

## Run locally

Node 24+:

```sh
npm ci --ignore-scripts --include=dev
npm test
npm run build
npm start
```

Open `http://localhost:4190`. `npm run dev` uses Vite middleware. `PORT` controls the server port; production binds to `0.0.0.0`. `/api/health` exposes readiness, app identity, Render runtime and deployed source commit, without secrets.

```sh
npx playwright install --with-deps chromium
npm run test:browser
```

The browser suite uses an isolated new profile. On Windows it defaults to the installed Chrome executable; elsewhere Playwright's Chromium. Set `AFTERLIGHT_BROWSER_PATH` for another test executable. Set `AFTERLIGHT_TEST_URL` to verify a deployed instance. It writes synthetic QA artifacts under `docs/` and ignored `test-results/`.

## Deploy

Single Node web service on Render's **Free** compute plan, using `npm ci --ignore-scripts --include=dev && npm run build`, `npm start`, and Node 24.18.0. No secrets are required. Render's hobby/free hosting has idle cold starts, shared monthly usage limits and no production availability promise. No paid services should be provisioned for this prototype.

## Provenance

The new Week 4 game, rooms, archive, source judgments, progress/passport, verifier, UI and tests were written for this challenge. Generic pinned MiniLM loader, WASM packaging/compression and Express/service-worker scaffolding are adapted from my earlier MIT projects [RecallSloth](https://github.com/faraz-shamim/recallsloth) and [ReferralGlass](https://github.com/faraz-shamim/referralglass); those earlier products are not resubmitted. MIT source notices are retained. See [THIRD_PARTY.md](THIRD_PARTY.md).

AI coding assistance was used. The imagined museum illustration was created with the built-in OpenAI image-generation tool; the [exact prompt and saved asset paths](docs/artwork.md) are recorded. All interface screenshots show the running app. The image is not an archival photograph. No institutional logos or source illustrations are reproduced, and the cited institutions do not endorse this project.

Code license: MIT. Third-party packages and models retain their respective licenses. Commits after the contest deadline, if any, must be explicitly identified here.

