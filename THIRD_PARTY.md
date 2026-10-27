# Third-party work and sources

Application code is MIT. Exact dependency versions and integrity hashes are in package-lock.json.

| Work | Version | License | Use |
| --- | --- | --- | --- |
| Xenova/all-MiniLM-L6-v2 | 751bff37182d3f1213fa05d7196b954e230abad9 | Apache-2.0 | Browser-local semantic retrieval |
| Transformers.js | 4.3.1 | Apache-2.0 | Model and tokenizer runtime |
| ONNX Runtime Web | 1.31.0-dev.20260914-8d85527a0 | MIT and bundled notices | CPU WASM inference |
| Express | 5.3.0 | MIT | Stateless evidence tool and front-end hosting |
| Vite | 8.3.4 | MIT | Build |
| Playwright | 1.64.1 | Apache-2.0 | Isolated browser tests |

Upstream ONNX notices and commit provenance are retained in vendor/onnxruntime. The build copies model/runtime library notices to /licenses. The model is downloaded from its pinned Hugging Face revision, not bundled under this project's MIT license.

## Prior code

The generic model worker, worker wrapper, WASM preparation/compression, Vite configuration and Express/service-worker scaffolding derive from [RecallSloth](https://github.com/faraz-shamim/recallsloth), with earlier loader/scaffolding origins in [ReferralGlass](https://github.com/faraz-shamim/referralglass), MIT, Copyright 2026 Faraz Shamim. The copyright notice is retained in LICENSE. This reuse is infrastructure; the museum's archive, fictional voices, gameplay, verifier, passport, UI and tests are new Week 4 work.

## Historical sources

All exhibit summaries, questions, fictional whispers, curator comments and endings are newly written. They summarize limited facts from the following linked institutional references. No source illustrations, formatted publications or official logos are reproduced. External sites and their text retain their respective rights. Linking and summarizing do not imply endorsement or permission to reuse their full content.

- [National Park Service: Scurvy at Saint Croix](https://www.nps.gov/articles/000/scurvy-at-saint-croix.htm): Lind's comparison and earlier citrus use.
- [Russell Museum at Massachusetts General Hospital: The Ether Dome](https://www.russellmuseum.org/the-ether-dome/): 16 October 1846 demonstration and participants.
- [CDC MMWR: 150th Anniversary of John Snow and the Pump Handle](https://www.cdc.gov/mmwr/preview/mmwrhtml/mm5334a1.htm): water-exposure investigation and pump-handle episode.
- [Science Museum Group: Joseph Lister](https://collection.sciencemuseumgroup.org.uk/people/cp37522/joseph-lister): antiseptic dressings, 1867 papers, Pasteur's influence and Agnes Lister's research assistance.
- [University of Oxford, Dunn School: The Discovery of Penicillin](https://www.path.ox.ac.uk/centenary/the-discovery-of-penicillin/): Fleming's observation and subsequent Oxford development.
- [WHO: Smallpox](https://www.who.int/emergencies/situations/smallpox/): vaccine milestone, intensified programme, last natural case and 1980 eradication declaration.

Sources were checked on 27 October 2026. Curator notes distinguish the narrow game exercise from a complete history. No medical treatment procedures or dosing instructions are provided.

## Artwork and assistance

Built-in OpenAI image generation in Codex produced the original imagined museum illustration. Exact prompt and paths: docs/artwork.md. WebP encoding preserved the generated content. The icon is original SVG; screen images show the running app. AI coding assistance was used.
