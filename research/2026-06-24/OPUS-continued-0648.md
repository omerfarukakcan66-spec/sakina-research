# Sakina Research — OPUS Deep Analysis (Wednesday Continuation #2) — Round 32
**Date:** 2026-06-24 | **Time:** 06:48 UTC | **Agent:** Opus deep-analysis
**Constraint:** Only 2025–2026 sources. Builds on Round 31 (OPUS-continued-0141.md).

---

## TL;DR — Incremental, not a new breakthrough

Round 31 already reached "every layer buildable from MIT code." This pass **re-verified the stack is stable** (no regressions, no newer package versions to chase) and added **two concrete new phoneme-assessment recipes** for the mispronunciation-scoring layer (Q5), both with hard numbers. No change to the action plan's core pipeline — the new material strengthens Q5 (mispronunciation scorer) with proven architectures + datasets.

**New this round:**
1. **Hafs2Vec** (ArabicNLP 2025, IqraEval) — a wav2vec2 phoneme-assessment recipe that ranked **#2** in the IqraEval 2025 shared task, with its exact training-data mix documented. Directly portable as Sakina's mispronunciation scorer.
2. **arXiv 2511.17477** (Nov 2025) — a **multimodal** Arabic-phoneme MDD model: UniSpeech acoustic + BERT(Whisper-transcript) text fusion → **PER 3.83%, MDD F1 70.53%**. Shows acoustic+text fusion beats acoustic-only.
3. **BAIC (Mattar et al., 2025)** — the IqraEval 2025 **#1** system; beat Hafs2Vec using **synthetic mispronunciation data**. Key signal: data augmentation, not model novelty, won.

---

## Q1 — Flutter real-time audio streaming (re-verified, stable)

No change from Round 31. Confirmed again this round:
- **`record`** remains PRIMARY: `startStream(RecordConfig(encoder: AudioEncoder.pcm16bits, sampleRate: 16000, numChannels: 1))` → `Stream<Uint8List>` of PCM16 chunks. Flutter's own cookbook ("Record or stream audio input", docs.flutter.dev) documents this exact path.
- **sherpa_onnx consumption pattern (verified integration detail):** feed the stream bytes to the recognizer via `stream.acceptWaveform(...)`; loop `recognizer.isReady(stream)` → `recognizer.decode(stream)`; read `recognizer.getResult(stream)`. This is the concrete glue between `record`'s byte stream and on-device ASR — matches the k2-fsa `flutter-examples/streaming_asr` reference.
- Community confirmation of the 16-bit/16kHz/mono PCM contract for on-device Flutter ASR (dasroot.net Dec 2025 write-up; Medium "Voice Control in Flutter" 2026).
- `mic_stream` continues to surface in generic search results but stays **AVOID** (GPL-3, dormant) — Round 31 finding holds.

**No newer package versions found** that change the recommendation.

## Q2 — Whisper / sherpa-onnx integration (re-verified, stable)

- **`sherpa_onnx` is still 1.13.3** (pub.dev changelog confirms latest, ~8 days old as of this check — i.e. ~Jun 16 2026). No 1.14.x. Round 31's integration path (assets-by-default, federated prebuilt sub-packages, `export-onnx.py` fixed-name workaround) stands unchanged.
- whisper_ggml_plus 1.5.2 remains the first-class fine-tune-conversion path. No new release observed.
- **Net:** nothing to re-plan; the two-path recommendation (sherpa for runtime/streaming, whisper_ggml_plus for fine-tune conversion) is current.

## Q3 — Who built Quran follow-along (re-verified + competitive note)

- **`yayaiu6/Real-Time-Quran-recitation-tracker-System`** remains the canonical open build guide and is **still the only live word-level recitation tracker** surfacing across multiple independent searches. Its README now explicitly states the fuzzy-matching algorithm is **"inspired by Tarteel AI's research"** — confirming Round 31's thesis that the position-tracking algorithm has **no secret sauce**; it's the same streaming-ASR-vs-canonical-text alignment, openly documented.
- All other "Quran + Flutter" repos remain **read/listen/prayer-times apps** (al_quran_v3, Moshaf_App, KajianHub, quran_app, etc.) — none do live recitation tracking. The category gap Sakina targets is real and unchanged.

## Q4 — Tarteel API / SDK (re-verified June 2026)

- **Still NO public ASR/inference API** — confirmed current via the live "Do you have an API I can use?" support article (redirects devs to QUL data only).
- A **"Developer Resources" collection** exists on support.tarteel.ai (collection 15105284) but its contents are gated (403 to anonymous fetch). Worth a manual human check, but the top-level API article already states the policy: no API, use QUL. No SDK, webhook, or endpoint disclosed.
- **`AliOsamaHassan/tarteel-api`** (readthedocs "tarteel-api") is an **old community backend** for tarteel.io, DeepSpeech-era — same archived-stack signal as `tarteel-ml`. Not the current NeMo/Riva production stack. Ignore for current intel.
- No reverse-engineering teardown of the production stack exists (consistent with Round 31). Closest remains `yazinsai/offline-tarteel` (speculative).

## Q5 — Arabic phonetic matching (NEW MATERIAL)

### ⭐ NEW — Hafs2Vec (ArabicNLP 2025, IqraEval shared task; aclanthology 2025.arabicnlp-sharedtasks.62)
A concrete, reproducible phoneme-assessment recipe — **ranked #2** in IqraEval 2025:
- **Architecture:** wav2vec2-based phoneme-level assessment (speech → Tajweed-aware phoneme sequence, compared to reference).
- **Training data (documented exactly — directly reusable):**
  - **EveryAyah/QUL**: 94h, 28 professional reciters, filtered to verses <10s → **54k clips**.
  - **IqraEval train set**: 79h → **74k clips**.
- **Labels:** generated with a custom **Quranic phonemizer** producing context- + Tajweed-aware phonemes aligned to the **IqraEval phoneme set** (same family as `quranic-phonemizer` from Round 31).
- **Sakina takeaway:** This is a published, working blueprint for the mispronunciation-scorer layer: fine-tune wav2vec2 (or AraS2P/Wav2Vec2-BERT) on EveryAyah+IqraEval with `quranic-phonemizer` labels → phoneme posteriors → align to reference phonemes for error detection. Replaces "design from scratch" with "reimplement a #2-ranked system."

### ⭐ NEW — BAIC / Mattar et al. 2025 (IqraEval #1)
- Beat Hafs2Vec to **#1 (best in 5 of 9 metrics)** primarily by adding **synthetic mispronunciation data**.
- **Strategic signal for Sakina:** at the current frontier, the differentiator in MDD is **augmentation/data**, not architecture. Investing in synthetic mispronunciation generation (perturb phonemes per El-Kheir-2025 confusion pairs) likely yields more than chasing a fancier model.

### ⭐ NEW — arXiv 2511.17477 (Nov 2025) "Enhancing Quranic Learning: A Multimodal DL Approach for Arabic Phoneme Recognition"
- **Approach:** fuse **UniSpeech acoustic embeddings** + **BERT text embeddings (from Whisper transcriptions)**; tested early/intermediate/late fusion.
- **Data:** 29 Arabic phonemes incl. 8 hafiz sounds, 11 native speakers + YouTube samples.
- **Results:** **PER 3.83%, MDD F1 70.53%, AF detection-error-rate 2.6%.**
- **Insight:** multimodal (acoustic + textual context) fusion outperforms acoustic-only for phoneme MDD — useful if Sakina already has the ASR transcript in hand; the transcript text can be fed as a second modality essentially for free.
- **Note:** no released pip package found (research artifact). Architecture is reproducible; code not confirmed open.

### Also noted (IqraEval 2025 ecosystem)
- **ANPLers** (2025.arabicnlp-sharedtasks.64): adapted **Whisper-large** for IqraEval phoneme assessment — a Whisper-based alternative to the wav2vec2 line.
- Confirms the IqraEval phoneme set + `quranic-phonemizer` + EveryAyah are the de-facto shared substrate across all top 2025 systems — Sakina aligning to the same substrate keeps it benchmark-comparable.

### Unchanged from Round 31 (still current)
`quranic-phonemizer` 2.7, `camel-tools` 1.6.0, `panphon` 0.22.2, `rapidfuzz` 3.14.5, AraS2P (#1 prior IqraEval), El-Kheir-2025 confusion matrices, IQRA 2026 challenge (arXiv 2603.29087). No Arabic Soundex/Metaphone package (negative finding holds).

---

## Net effect on the plan

No change to the core pipeline. **One refinement to P1 step 5 (mispronunciation scorer):** instead of designing the scorer from scratch, **reimplement Hafs2Vec's recipe** (wav2vec2 + EveryAyah/QUL 94h + IqraEval 79h, `quranic-phonemizer` labels) and **add synthetic mispronunciation augmentation** (the BAIC #1 lever). Optionally fuse the ASR transcript as a text modality (2511.17477) since Sakina already produces it. This converts step 5 from "research task" to "reproduce two ranked systems."

No breakthrough overturning the action plan; no failures or blockers encountered. Stack remains stable and buildable.

---

## Sources (2025–2026)
- Flutter record/stream cookbook: https://docs.flutter.dev/cookbook/audio/record · `record`: https://pub.dev/packages/record
- sherpa_onnx changelog (1.13.3 latest): https://pub.dev/packages/sherpa_onnx/changelog · streaming_asr example: https://github.com/k2-fsa/sherpa-onnx/blob/master/flutter-examples/streaming_asr/lib/streaming_asr.dart
- Flutter local-ASR write-ups: https://dasroot.net/posts/2025/12/building-flutter-voice-assistants-local-speech-recognition/
- yayaiu6 tracker: https://github.com/yayaiu6/Real-Time-Quran-recitation-tracker-System
- Tarteel "no API" article: https://support.tarteel.ai/en/articles/12414464-do-you-have-an-api-i-can-use · Developer Resources collection (gated): https://support.tarteel.ai/en/collections/15105284-developer-resources
- Hafs2Vec (IqraEval 2025): https://aclanthology.org/2025.arabicnlp-sharedtasks.62.pdf · IqraEval task overview: https://aclanthology.org/2025.arabicnlp-sharedtasks.61.pdf · ANPLers Whisper-large: https://aclanthology.org/2025.arabicnlp-sharedtasks.64.pdf
- Multimodal MDD (Nov 2025): https://arxiv.org/abs/2511.17477
- AraS2P: https://arxiv.org/abs/2509.23504 · IQRA 2026: https://arxiv.org/abs/2603.29087
