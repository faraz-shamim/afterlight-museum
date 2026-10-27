---
title: "Afterlight: A Haunted Museum Where Medical History Has to Show Its Sources"
published: true
tags: devchallenge, hf26challenge, ai, education
cover_image: https://raw.githubusercontent.com/faraz-shamim/afterlight-museum/main/web/public/art/afterlight-hero.webp
description: "Six fictional ghosts. Six real medical histories. A local AI archive guide and a lantern for every story you restore."
---

_This is a submission for the [Hacktoberfest Open-Source AI Challenge Week 4: Ghost in the Machine](https://dev.to/challenges/hacktoberfest-week4-2026-10-26)._

The ghost in the ship’s infirmary does not ask you for a diagnosis.

It remembers full barrels, a long ocean crossing, and a kind of nourishment that was missing. Somewhere in the museum is an exhibit that can explain its story.

You have a lantern. You also have an AI guide.

Neither gets to decide what is true without evidence.

## What I Built

**Afterlight is a small, playable museum of medical history.** Six rooms hold fictional whispers inspired by documented episodes. You investigate a whisper, inspect source-linked evidence, and choose a careful interpretation. Restore the memory and a lantern lights up in your passport.

The museum is for curious visitors and learners who like medicine but might remember a story more readily than a list of dates. It is an educational prototype, not a clinical tool or a validated teaching intervention.

I interpreted **Ghost in the Machine** as the past lingering inside the medicine we recognize today: discarded explanations, surprising observations, quiet collaborators, and public-health work that cannot fit into one famous portrait.

There are no jump scares, graphic procedures or simulated conversations with deceased patients. Every whisper is clearly labeled as newly written fiction. The archive is where the historical claims live.

![The real Afterlight museum, with six medical-history rooms and a lantern-bearing illustrated ghost](https://raw.githubusercontent.com/faraz-shamim/afterlight-museum/main/docs/afterlight-desktop.png)

The rooms connect to six episodes:

| Room | The history behind it |
| --- | --- |
| The ship’s infirmary | Lind’s shipboard citrus comparison |
| The upstairs theatre | The influential Ether Dome demonstration |
| The fogbound street | Snow’s investigation of water exposures |
| The closed surgical ward | Lister’s antiseptic system |
| The forgotten laboratory | Penicillin’s observation and subsequent development |
| The room with no borders | The worldwide smallpox eradication effort |

Each exhibit has an original short summary, a curator’s note and an external institutional source. The archive links to the [National Park Service](https://www.nps.gov/articles/000/scurvy-at-saint-croix.htm), [Russell Museum at Massachusetts General Hospital](https://www.russellmuseum.org/the-ether-dome/), [CDC](https://www.cdc.gov/mmwr/preview/mmwrhtml/mm5334a1.htm), [Science Museum Group](https://collection.sciencemuseumgroup.org.uk/people/cp37522/joseph-lister), [Oxford’s Dunn School](https://www.path.ox.ac.uk/centenary/the-discovery-of-penicillin/) and [WHO](https://www.who.int/emergencies/situations/smallpox/). They are references, not project endorsers.

The goal is a little more than recognizing a name. The penicillin room asks you to distinguish noticing something from developing it into a medicine. The final room asks who a worldwide achievement ought to remember. You need the right exhibit **and** the supported interpretation to restore a memory.

## Demo

**[Enter Afterlight →](https://afterlight-museum.onrender.com/)**

Try the ship’s infirmary first:

1. Read its fictional whisper.
2. Use the editable plain-language clue, or replace it with your own description.
3. Click **Find evidence with local AI**. First use downloads the open model and runtime.
4. Open a suggested exhibit’s curator note and source before selecting it.
5. Answer the interpretation question and restore the memory.

If model loading is unavailable, **Browse without AI** opens all six exhibits. That route is labeled as manual browsing; the interface does not pretend an inference happened.

There is no account. Your lanterns stay in your browser. A small JSON passport can be downloaded and imported later; reset asks for confirmation. The passport is personal, editable progress, not a certified score. No timer, streak penalty or leaderboard turns a quiet museum into homework.

![A real Afterlight investigation with local-model steps and source-linked evidence suggestions](https://raw.githubusercontent.com/faraz-shamim/afterlight-museum/main/docs/afterlight-investigation.png)

## Code

**[GitHub: faraz-shamim/afterlight-museum](https://github.com/faraz-shamim/afterlight-museum)** · MIT application code

The repository includes the six-room archive, verifier, passport validation, browser worker, deployment instructions, tests, actual retrieval results and image provenance. It was started for this Week 4 challenge on 27 October 2026.

## How I Built It

### A guide that retrieves, rather than improvises

Afterlight uses the open [Xenova/all-MiniLM-L6-v2](https://huggingface.co/Xenova/all-MiniLM-L6-v2) model through Transformers.js, running q8 CPU WASM inference inside a browser worker.

The exact revision is `751bff37182d3f1213fa05d7196b954e230abad9`. The loader pins both tokenizer assets and model requests to that revision. The ONNX engine and its license notices are served with the application.

The model embeds each exhibit’s subject, title and summary, then embeds the visitor’s clue. Cosine similarity ranks the six exhibits. The AI-assisted search can connect a description to an episode without requiring the visitor to know the correct historical name first.

The archive guide exposes its work: what was embedded, the embedding duration, model revision and the limited job of the verification tool. The first embedding step can include loading time. No generative model fills in historical facts, and the similarity number is never presented as the probability that an exhibit is correct.

The human still opens the source and chooses.

### The ghost that fooled my guide

I tested six poetic whispers and six plain paraphrases against the real browser model. Across those **twelve development smoke probes**, the expected exhibit ranked:

| Retrieval result | Count |
| --- | --- |
| First | 11 / 12 |
| Within the top three | 12 / 12 |

Those numbers describe a tiny, authored six-exhibit collection. They are **not** an independent holdout, general search benchmark, medical accuracy result or evidence of improved learning. [Every query and ranking is recorded here.](https://github.com/faraz-shamim/afterlight-museum/blob/main/docs/retrieval-diagnostic.json)

The interesting failure was the final room. Its whisper mentions knocking on doors, tracing missing stories and an empty ward. MiniLM placed penicillin first and smallpox third.

I kept the failure. The vague poetry is a harder retrieval problem than the plain clue, and the mistake makes the interaction’s boundary visible: a suggestion should lead you to investigate, not quietly award itself the authority of a source.

For an unrelated clue, the model still ranks the same six exhibits. This is a finite museum guide, not an open-ended medical search engine.

### Render is the public doorway and the bounded tool

The human-guided retrieval workflow has a small server-side tool:

```text
fictional whisper
    → local model retrieves source cards
    → visitor selects evidence + interpretation
    → verification tool checks authored puzzle
    → local passport lights a lantern
```

**Render hosts the front end, compressed browser inference assets and the stateless `/api/verify` endpoint** on one Node web service. The model itself runs on the visitor’s device.

The tool accepts exactly three fields: a known room ID, a known exhibit ID and a bounded answer index. It rejects extra fields, arbitrary IDs, malformed JSON and oversized payloads. It checks the authored game answer; it does not verify a diagnosis or establish a new historical claim. The browser has the same deterministic verifier for offline fallback.

This division keeps the public demo deployable without a hosted model API key, database or GPU. No search text or embedding vectors go to the verification endpoint.

The deployment uses Render’s **Free** compute plan. Free hosting has cold starts and shared usage limits; I am treating it as prototype hosting, not promising production uptime or unlimited free traffic.

### Checking the whole walk through the museum

The project has **18 passing unit/API tests** and **19 passing browser checks** locally and against the public Render deployment, including actual model inference:

- All 108 room/exhibit/answer combinations, with only the six intended combinations restoring a room.
- Wrong evidence and unsupported interpretations refusing to award lanterns.
- All six room journeys, real verification requests, visible source notes and completed passport.
- Download/import, invalid-file rejection, reload persistence and cancelable reset.
- Museum, archive, passport and investigation layouts at 390px width.
- Offline reload, cached real-model inference and keyboard completion with the local verifier.
- A synthetic private marker in a clue never appearing in observed outbound requests; tool payloads containing only the allowed fields.
- Immutable model-revision requests and no uncaught browser exceptions.

[The complete suite also passed independently on Linux in GitHub Actions.](https://github.com/faraz-shamim/afterlight-museum/actions/runs/40442205177) The repository includes the local and live browser reports and the verified deployment commit.

These are synthetic software checks. I have not run a student trial, measured retention, or claimed that six compact exhibits constitute a complete history of medicine.

![The actual Afterlight passport after all six memories have been restored](https://raw.githubusercontent.com/faraz-shamim/afterlight-museum/main/docs/afterlight-passport.png)

## Why Does Open Innovation Matter?

For this museum, the useful kind of openness is practical: the guide can keep working after its network connection disappears.

Once the app and model are cached, the tested flow survives an offline reload and still performs inference. The model’s weights, revision and runtime are inspectable. The exhibit archive is an ordinary source file. Another builder can change a summary, add an interpretation, swap the retriever, or rerun the diagnostic without purchasing tokens from a closed inference service.

It also lets me keep the visitor’s clue off the application server. That is a specific data boundary, not a claim of anonymity: the host and model provider still see ordinary download/request metadata, and external source links require internet. Browser storage can be evicted, and a first visit needs a connection.

The archive’s source labels remain accessible even when AI is unavailable. That matters to the theme too. Remembering the past should not depend on a proprietary conversation endpoint continuing to exist.

I wanted the haunting to be gentle. The room grows lighter when an old explanation becomes clearer, and the final room belongs to communities and collaborators rather than one heroic portrait. The machine helps find the label; the visitor decides whether to hang it on the wall.

## Prize Categories

**Best Use of Render** — hosting the local-AI guide’s front end, browser runtime assets and bounded evidence-verification tool. This is the only partner category I am entering.

## Credits and boundaries

This is a new Week 4 game. Its rooms, fictional voices, archive, puzzle judgments, passport, UI and tests were written for this challenge. Generic pinned MiniLM loading, WASM packaging/compression and Express/service-worker scaffolding are adapted from my MIT projects [RecallSloth](https://github.com/faraz-shamim/recallsloth) and [ReferralGlass](https://github.com/faraz-shamim/referralglass), with notices retained. The earlier products are not resubmitted.

AI coding assistance was used. The cover and museum hero are an original AI-created illustration, not historical photography. The [exact image prompt and assets are recorded](https://github.com/faraz-shamim/afterlight-museum/blob/main/docs/artwork.md); the screenshots show the actual running app. No institutional logos or source illustrations were copied.

Afterlight provides no diagnosis, treatment advice or clinical use. It uses no real patient records, tracking analytics, microphone, camera, external fonts or ElevenLabs. Its lanterns celebrate finishing a short evidence exercise—not medical competence.

