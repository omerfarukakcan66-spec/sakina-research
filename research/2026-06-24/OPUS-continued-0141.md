# Sakina Research — OPUS Deep Analysis (Wednesday Continuation) — Round 31
**Date:** 2026-06-24 | **Time:** 01:41 UTC | **Agent:** Opus deep-analysis
**Constraint:** Only 2025–2026 sources. Focus: the 5 open implementation questions.

---

## TL;DR — Two Breakthroughs This Round

1. **An open-source, MIT-licensed, genuinely LIVE Quran follow-along engine exists** and documents the exact algorithm Sakina needs: `yayaiu6/Real-Time-Quran-recitation-tracker-System` (created Nov 2025, last push Apr 26 2026, 121★). Whisper/Conformer-CTC ASR → Levenshtein segment ranking → **Needleman-Wunsch word alignment** → 60-ahead/15-behind sliding window with tracking/search modes, streamed over Socket.IO. **This is a build guide, not just inspiration.**

2. **`quranic-phonemizer` v2.7 (PyPI, June 18 2026, MIT)** ships a Tajweed-aware Quran G2P with **built-in fuzzy `ref_text` matching** that maps noisy/partial recited text onto the correct ayah AND returns position — i.e. it solves a chunk of both the follow-along position problem and the phonetic-matching problem in one MIT package. `pip install quranic-phonemizer`.

3. **Tarteel's production stack is now fully mapped** (NVIDIA NeMo + Riva + Triton, ~4% WER, <200ms, React Native app, NO public ASR API, QUL is data-only). Their public HuggingFace Whisper models are research artifacts, not the production model.

---

## Q1 — Flutter Packages for Real-Time Audio Streaming (Active 2026)

| Package | Version | Released | License | Verdict |
|---|---|---|---|---|
| **`record`** | **7.1.0** | ~Jun 11 2026 | BSD-3 | **PRIMARY.** `startStream(RecordConfig(encoder: AudioEncoder.pcm16bits))` → `Stream<Uint8List>` chunked PCM16. Verified publisher, ~706k downloads, active. |
| **`sherpa_onnx`** | **1.13.3** | ~Jun 16 2026 | Apache-2.0 | On-device ASR. (June 18 GitHub `asr-models-qnn-2/-binary-2` are model assets, NOT a new package version.) |
| **`flutter_webrtc`** | **1.5.2** | ~Jun 20 2026 | MIT | Live teacher session. Does NOT expose raw PCM of local track to Dart out-of-the-box. |
| `flutter_sound` | 9.30.0 | ~late 2025 | MPL-2.0 | Viable PCM streaming but heavy; historic iOS audio-session conflicts (issue #855). Successor "Taudio" 10.0 is alpha. |
| `audio_streamer` | 4.3.0 | ~2025 | MIT | PCM stream, less recently maintained than `record`. |
| **Picovoice `flutter_voice_processor`** | active | — | Apache-2.0 | **Built for one-capture→many-subscribers** real-time 16-bit PCM frames. Strong fit for forking mic to WebRTC + ASR. |
| `mic_stream` | 0.7.2 | **early-2024, DORMANT** | **GPL-3.0** | **AVOID** — unmaintained in 2026, copyleft, iOS sample-rate bugs. (Prior "March 2026" date was a hallucination; debunked.) |

### Critical architecture finding — DO NOT open the mic twice
Running `flutter_webrtc` AND `record`/`flutter_sound` as two independent mic openers is **unreliable on iOS**: there is one app-wide `AVAudioSession`; two plugins setting different categories clobber each other. A documented Feb 2026 LiveKit case (client-sdk-flutter issue #996) shows STT + WebRTC breaking remote audio via `AVAudioSession` category conflict.

**Recommended pattern (capture once, fork):**
- Capture mic **once** via `record` 7.1.0 (broadcast stream) or Picovoice `flutter_voice_processor` (multi-listener by design).
- Coordinate the session with the **`audio_session`** package (shared AVAudioSession / Android audio-focus).
- Fork the PCM16 stream to (a) WebRTC custom audio source/track and (b) the ASR pipeline.
- Alternative: tap the WebRTC local `AudioTrack` for PCM rather than opening a 2nd mic (flutter-webrtc issues #632, #1609) — needs native glue, not turnkey yet.

---

## Q2 — Exact Whisper / sherpa-onnx Integration Path for Flutter

**Two viable on-device paths in 2026. For a FINE-TUNED Arabic/Quran Whisper, GGML/whisper.cpp is the smoother CONVERSION path; ONNX/sherpa is the smoother RUNTIME/cross-platform path.**

### Path A — `sherpa_onnx` (ONNX, Apache-2.0) — best runtime integration
1. Example: `flutter-examples/streaming_asr` in k2-fsa/sherpa-onnx.
2. `flutter pub add sherpa_onnx`.
3. Models = **assets by default**: download streaming model from releases `asr-models` tag → extract into `assets/<model-dir>/` → declare in `pubspec.yaml`.
4. Select model in `online_model.dart`; set `final type = 0;` in `streaming_asr.dart`; `flutter run`.
5. Federated sub-packages (`sherpa_onnx_ios`, `sherpa_onnx_android_arm64`, …) vendor prebuilt onnxruntime → standard `pod install` / default NDK work; no manual Podfile/NDK edits needed (but add `NSMicrophoneUsageDescription`). To shrink APK/IPA, copy the **Flutter-EasySpeechRecognition** runtime-download pattern instead of bundling assets.
- **Fine-tune conversion gotcha:** `scripts/whisper/export-onnx.py` `--model` only accepts a fixed choices list (tiny…large-v3, turbo, distil-*), NOT an arbitrary HF path. Workaround: pick the matching base size, rename your fine-tuned weights to the filename the chosen branch expects, or lightly edit `main()`. Output: `*-encoder.onnx`, `*-decoder.onnx`, `*-tokens.txt` (+int8). Templates under `csukuangfj/sherpa-onnx-whisper-*`.

### Path B — whisper.cpp via **`whisper_ggml_plus`** (GGML, MIT) — best for fine-tune conversion
- `whisper_ggml_plus` **v1.5.2 (Apr 1 2026, MIT)**, all platforms, syncs whisper.cpp v1.8.3, new `ggml-backend`, 128 mel bands (large-v3-turbo), **CoreML(ANE)+Metal** on Apple, 16KB-page alignment for newer Android. Models: bundle ggml `.bin` asset or `WhisperController.downloadModel()`.
- whisper.cpp itself is very active: **v1.9.1 (Jun 19 2026)**, v1.9.0 added Parakeet support. whisper_ggml_plus pins v1.8.3 (slightly behind head).
- **HF fine-tune → ggml:** `models/convert-h5-to-ggml.py` has a `conv_map` built to read HF naming → single `ggml-*.bin`; quantize q5_0/q8_0 for mobile; optional iOS CoreML encoder via `models/generate-coreml-model.sh`. This is **first-class for fine-tunes** (unlike sherpa's fixed-name limitation). Community example: `cxeep/whisper-cpp-finetune`.

### iOS-only option (not cross-platform)
- `flutter_whisper_kit` v0.3.0 (MIT) wraps Apple WhisperKit/Core ML — **iOS 16+/macOS 13+ only, Android "planned"**. Use only for an iOS-native ASR module. `whisper_dart` = no 2025-2026 activity, avoid.

**Recommendation:** Default to **whisper_ggml_plus** for a fine-tuned Arabic Whisper (`uzair0/quran-asr`, `FaisaI/tadabur`) because conversion is first-class + CoreML/Metal automatic; use **sherpa_onnx** when you want one clean cross-platform plugin with real streaming and accept the ONNX export workaround.

---

## Q3 — Who Built Quran Follow-Along in 2025–2026 (BREAKTHROUGH)

### ⭐ `yayaiu6/Real-Time-Quran-recitation-tracker-System` — the open-source build guide
`github.com/yayaiu6/Real-Time-Quran-recitation-tracker-System` · **MIT** · Python · 121★ · created **2025-11-21**, last push **2026-04-26**. The closest thing to "karaoke for Quran" with a fully documented live algorithm:
- **ASR backends (selectable):** Groq Whisper API (cloud) OR NVIDIA NeMo Conformer-CTC local (recommends `MostafaAhmed98/Conformer-CTC-Arabic-ASR`).
- **Alignment = text-based fuzzy matching (NOT DTW/acoustic):** (1) candidate Quranic segments ranked by **normalized Levenshtein + length-mismatch penalty**; (2) **Needleman-Wunsch global alignment** maps spoken words → mushaf words.
- **Dual-mode tracking:** "tracking" mode searches a sliding window (default **60 words ahead / 15 behind**) for continuous recitation; "search" mode triggers after consecutive low-confidence chunks and scans the whole page.
- **Cumulative 6–10s audio buffer** feeds ASR for context; Socket.IO streams word results + skip/mismatch alerts. Skipped-verse detection at ≥12-word gap.
- Tunable in `backend/config.py`: word-similarity 0.45, segment-score 0.5, window 60.
- **Sakina action:** Read this repo's README + `config.py` directly — it is effectively the spec for the live position-tracking engine. Port the matching loop to Dart/Flutter on top of `record` + sherpa_onnx.

### Other concrete builders
- `sayedmahmoud266/quran-ai-transcriping` (Sep–Oct 2025, custom license) — FastAPI, `tarteel-ai/whisper-base-ar-quran`, **RapidFuzz + PyQuran constraint propagation**. NOT real-time (batch file upload). Good matching-algorithm reference.
- **`lafzize`** (rehandaphedar, sourcehut, 2025) — word-level timestamps via **CTC forced alignment** (`ctc-forced-aligner`), consumes `qpc-hafs-word-by-word.json` from QUL. Modern open-source alternative to cpfair/quran-align for the *playback* path.
- **Qurani.ai QRC API** (buy-vs-build) — WebSocket `StartTilawaSession`/`CheckTilawa` returns `correct_words`/`skipped_words`/`tajweed_mistakes` with chapter/verse/word indices — exactly the word-index position data a follow-along UI needs.

### Two distinct architectures (important)
- **(a) Playback follow-along** (highlight word during *audio playback*): look up pre-computed word timestamps, advance pointer by audio clock. Source: **Quran Foundation Audio API** (segment start/end timestamps) or generate via `lafzize`/cpfair-quran-align. Simple, robust, no mic.
- **(b) Live-recitation follow-along** (highlight as *user recites*): stream mic → ASR → fuzzy-match expected next words in sliding window. What Tarteel + yayaiu6 do. **This is Sakina's path.**

### HF Spaces & datasets
- `hetchyy/quranic-universal-aligner` (Space) — upload recitation → verse split + precise timestamps (batch, not live).
- Tadabur: arXiv 2604.18932, repo `fherran/tadabur` (162★, created 2025-11-07, pushed 2026-04-22), 1400h, WhisperX forced alignment producing word-level JSON.
- QURAN-MD arXiv 2601.17880 (NeurIPS 2025 wksp); MDPI Appl. Sci. 15(17):9521 (2025) "Enhanced Neural Speech Recognition of Quranic Recitations."

---

## Q4 — Tarteel API / SDK / Backend (Competitive Intel)

**Production ASR stack CONFIRMED: NVIDIA NeMo (train) → Riva (serve) → Triton (multi-GPU infer).** NOT a Whisper/wav2vec2 production model.
- WER **~4%** on Quranic Arabic (NVIDIA's "world's first" claim); **<200ms** streaming latency.
- Architecture: **Conformer/FastConformer family** (strongly inferred from NeMo defaults + their forks; CTC/RNNT specifics unconfirmed).
- Trained on **>75,000 min (~1,250h)** of curated recitation (separate, larger internal corpus than the ~67h crowd-sourced "Tarteel Dataset" from the ~2023 paper).

**No public ASR/inference API** — officially confirmed (support article, Sept 2025): they redirect developers to **QUL**, which is static Quran *data* (JSON/SQLite), not an API. QUL backend = **Ruby on Rails + PostgreSQL + ActiveAdmin**, MIT, pushed 2026-06-23 (894★).

**GitHub fork pattern = the real stack signal:** `nemo2riva` (Feb 2026), `NeMo`, `celery-exporter` (Apr 2026, Prometheus+Celery), `fastlane` (Jan 2026, mobile CI/CD). `tarteel-ml` is **archived since Dec 2021** (DeepSpeech-era, irrelevant to current stack).

**Public HuggingFace models = research/legacy, NOT production:** `tarteel-ai/whisper-base-ar-quran` (WER 5.75%), `whisper-tiny-ar-quran`. Production is the NeMo/Riva Conformer.

**Dataset:** `tarteel-ai/everyayah` = **professional reciters** (~25,081 rows, 117GB, ~829h train), diacritized — likely **CC BY-NC-ND 4.0** (NC+ND = no commercial, no derivatives; verify before any use). This is what most Quran-ASR fine-tunes train on.

**Other stack facts:** Mobile = **React Native** (`com.mmmoussa.iqra`, v5.76.9 May 7 2026, ~14M downloads) — **NOT Flutter**. Web/backend = React/Next.js/Node.js + Python. Infra = multi-cloud K8s; **migrated AWS→CoreWeave** (Zeet-assisted, ~22% latency↓, ~56% cost↓). No public reverse-engineering teardown exists; closest is `yazinsai/offline-tarteel` `RESEARCH-audio-to-verse.md` (Feb 24 2026) — speculative offline rebuild, zero actual RE.

**Sakina takeaways:** (1) Tarteel's moat is a proprietary NeMo model + infra, not an API you can call — so Sakina cannot piggyback; must self-host ASR (sherpa/whisper.cpp) or use Groq/Qurani.ai. (2) Their position-tracking is the same streaming-ASR-vs-canonical-text alignment that yayaiu6 documents openly — **no secret sauce in the algorithm**, the moat is data + latency engineering. (3) They are React Native; Sakina's Flutter + live-teacher angle remains differentiated.

---

## Q5 — Arabic Phonetic Matching Libraries (Updated 2025–2026)

### ⭐ `quranic-phonemizer` v2.7 — PRIMARY (PyPI, **Jun 18 2026**, MIT, Py 3.8–3.13)
- `pip install quranic-phonemizer`. Tajweed-aware G2P for **Hafs ʿan ʿAsim**, **69–71 phoneme inventory** (IPA + custom Tajweed phonemes encoding Idgham, Iqlab, Ikhfaa, Qalqala, Tafkheem, Waqf…).
- API takes Quran refs (`"1:1"`) **or raw `ref_text` with built-in fuzzy matching** → directly maps recited Arabic to the expected ayah AND yields position. NeurIPS 2025 Muslims-in-ML paper. Companion dataset `hetchyy/everyayah-phonemes` (45 reciters). Demo: quranicphonemizer.com.
```python
from quranic_phonemizer import Phonemizer
pm = Phonemizer(); res = pm.phonemize("1:1")   # or ref_text="..." (fuzzy)
```

### Supporting stack (all MIT/open, 2025–2026)
- **Normalize ASR text:** `camel-tools` **1.6.0 (Jun 8 2026, MIT)** — best-maintained Arabic normalization + diacritization (Alef/Ya/Ta-Marbuta). `PyArabic` is stale (2022, GPL) — prefer camel-tools.
- **Surface fuzzy match:** `rapidfuzz` 3.14.5 (MIT) — Unicode-correct over Arabic+diacritics.
- **Reference MSA G2P:** `Iqra-Eval/MSA_phonetiser` (active 2025–2026) — canonical phoneme seq from diacritized text; de-facto IqraEval baseline.
- **Phoneme edit distance:** `panphon` **0.22.2 (Jun 12 2025, MIT)** — IPA→articulatory features → **weighted feature-edit distance** (graded, better than raw Levenshtein). Feed it Arabic IPA from the phonemizer.
- **espeak-ng + `phonemizer`** (GPLv3) — quick Arabic→IPA fallback, needs full diacritization, has gemination/sun-letter bugs (patched by SawtArabi, Interspeech 2025).

### Confusion matrices for substitution costs (papers, 2025–2026)
- **El Kheir, Interspeech 2025** — confusion matrices from phoneme similarity; confusable pairs /t/–/tˤ/, /s/–/sˤ/, /g/–/x/. Directly usable as substitution-cost matrix.
- **"Improving MDD for non-native Arabic"** (Jan 2025, Springer) — empirical confusion matrices (21,511 phonemes), long-vowel confusions dominate, PER 3.1%.
- **IqraEval Shared Task** (ArabicNLP 2025) + **IQRA 2026 Interspeech Challenge** (arXiv 2603.29087) — open MDD benchmark, phoneme inventory, leaderboard. **AraS2P** (arXiv 2509.23504, ranked #1) = Wav2Vec2-BERT speech→phoneme front-end candidate.
- **QPS** (ICML 2026, arXiv 2509.00094) — two-level script (phoneme + Sifa articulation level), ~0.16% PER; a representation to adopt, not a pip package.

### Negative finding
No actively maintained dedicated **Arabic Soundex/Metaphone/double-metaphone** package updated 2025–2026 (`arSoundex` stale, `jellyfish`/`abydos` English-only). This class is superseded by IPA-G2P + phoneme edit distance.

---

## Recommended Concrete Sakina Pipeline (synthesized this round)

**Live follow-along + tajweed feedback, on-device-first, Flutter:**
1. **Capture once:** `record` 7.1.0 → PCM16 broadcast stream; coordinate via `audio_session`; fork to WebRTC (`flutter_webrtc` 1.5.2) + ASR.
2. **ASR:** `sherpa_onnx` 1.13.3 (Nemotron-3.5 streaming) on-device, OR Groq Whisper cloud; for fine-tuned Arabic Whisper use `whisper_ggml_plus` 1.5.2.
3. **Position tracking:** port **yayaiu6** algorithm — normalized-Levenshtein segment ranking → Needleman-Wunsch word alignment → 60/15 sliding window, tracking/search modes.
4. **Reference phonemes + fuzzy ayah lock:** `quranic-phonemizer` 2.7 `ref_text` mode (also gives Tajweed-aware IPA).
5. **Mispronunciation scoring:** `camel-tools` normalize → recited phonemes (AraS2P-style) → weighted Needleman-Wunsch with `panphon`/El-Kheir-2025 confusion-matrix substitution costs.
6. **Spoken feedback:** Groq Orpheus Arabic TTS (Aisha voice).

Every layer now has a confirmed 2025–2026 open/MIT/Apache implementation. **The follow-along + phonetic-matching layers — previously the biggest unknowns — are now both buildable from MIT-licensed reference code.**

---

## Sources
- `record`: https://pub.dev/packages/record/changelog
- `sherpa_onnx`: https://pub.dev/packages/sherpa_onnx · https://github.com/k2-fsa/sherpa-onnx/tree/master/flutter-examples/streaming_asr
- `whisper_ggml_plus`: https://pub.dev/packages/whisper_ggml_plus · whisper.cpp https://github.com/ggml-org/whisper.cpp
- `flutter_webrtc`: https://pub.dev/packages/flutter_webrtc · `audio_session`: https://pub.dev/packages/audio_session · LiveKit conflict: https://github.com/livekit/client-sdk-flutter/issues/996
- ⭐ yayaiu6 follow-along: https://github.com/yayaiu6/Real-Time-Quran-recitation-tracker-System
- sayedmahmoud266: https://github.com/sayedmahmoud266/quran-ai-transcriping · lafzize: https://sr.ht/~rehandaphedar/lafzize/
- Tarteel NVIDIA case study: https://www.nvidia.com/en-us/case-studies/automating-real-time-arabic-speech-recognition/ · CoreWeave: https://www.coreweave.com/blog/tarteel-migrates-cloud-infrastructure-to-coreweave-with-help-from-zeet · API article: https://support.tarteel.ai/en/articles/12414464-do-you-have-an-api-i-can-use · QUL: https://github.com/TarteelAI/quranic-universal-library
- ⭐ quranic-phonemizer: https://github.com/Hetchy/Quranic-Phonemizer · https://quranicphonemizer.com
- camel-tools 1.6.0: https://github.com/CAMeL-Lab/camel_tools · panphon: https://github.com/dmort27/panphon · rapidfuzz: https://github.com/rapidfuzz/RapidFuzz
- IqraEval 2026: https://arxiv.org/abs/2603.29087 · AraS2P: https://arxiv.org/abs/2509.23504 · El Kheir 2025: https://www.isca-archive.org/interspeech_2025/elkheir25b_interspeech.pdf
