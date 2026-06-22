# Sakina Research — Rolling Summary

Cumulative top insights across all research runs, newest first.

---

## 2026-06-22 Daytime (12:15 UTC) — IQRA 2026 Champion Architecture + Qwen3-ASR Quran Verdict + Meta OmniASR sherpa-onnx (Round 12)
*Full report: [research/2026-06-22/daytime-1215.md](2026-06-22/daytime-1215.md)*

### Insight 1 — IQRA 2026 Champion (whu-iasp, F1=0.7201): wav2vec2-XLS-R + TCN + CTC Is the Sakina Tajweed Assessment Template
19 teams at IQRA 2026 (Interspeech, Sydney). Winner **whu-iasp**: frozen `wav2vec2-xls-r-300m` encoder + **Temporal Convolutional Network (TCN)** for phoneme-level context + CTC head, with two-stage curriculum training on the new `Iqra_Extra_IS26` corpus (first dataset of real human mispronounced MSA speech). F1 jumped from 0.30 (ArabicNLP 2025 best) to **0.7201** — a >2.4× improvement. The top 3 systems (whu-iasp, UTokyo, RAM) differ by < 0.005 F1, all using CTC-based approaches. **6th place: Kalimat (F1=0.6702)** used Qwen3-ASR 1.7B as a generative LALM — proves Qwen3-ASR works for phoneme assessment but is outclassed by lighter CTC systems. Sakina should use wav2vec2-xls-r-300m + TCN + CTC as the pronunciation assessment backbone, not Qwen3-ASR.

### Insight 2 — Meta OmniASR 300M sherpa-onnx Pre-Converted (Nov 12, 2025): New Flutter Arabic ASR Path, Unbenched on Quran
Meta released **Omnilingual ASR** (arXiv:2511.09690, Apache-2.0, Nov 10, 2025) — 1,600+ languages, CTC models at 300M/1B/7B params. Critical for Flutter: `csukuangfj/sherpa-onnx-omnilingual-asr-1600-languages-300M-ctc-2025-11-12` is a pre-built ONNX export already in the sherpa-onnx ecosystem — no conversion work needed, just set `language=arb_Arab`. The 300M CTC model achieves **8.92% phoneme error rate** on MSA Arabic (via Harf-Speech, arXiv:2604.06191). **Critical gap:** WER on specifically Quranic Classical Arabic is unpublished. Test this in the sherpa-onnx Flutter streaming example vs. `KheemP/whisper-base-quran-lora` (5.98% WER) immediately. If OmniASR hits < 8% on Quran it becomes the preferred on-device path: pre-converted, Apache-2.0, no Whisper ONNX export step.

---

## 2026-06-22 Daytime (08:10 UTC) — arXiv:2507.13977 Deep Dive + TajweedAI + NeurIPS 2025 Full Sweep (Round 11)
*Full report: [research/2026-06-22/daytime-0810.md](2026-06-22/daytime-0810.md)*

### Insight 1 — nvidia/stt_ar_fastconformer_hybrid_large_pcd_v1.0: SOTA Classical Arabic + Diacritics, Tarteel's Actual 4% WER Backbone
arXiv:2507.13977 (Jul 2025, IEEE) trained two FastConformer models on ~1,150h including **TarteelAI EveryAyah [390h]**. The `_pcd` variant (MSA + Classical Arabic + **diacritics**) is the model Tarteel runs via NVIDIA Riva to achieve their confirmed 4% WER on Quran — confirmed by NVIDIA case study. ~115M params. **Critical blocker for Flutter**: sherpa-onnx issue #790 (open since April 2024, still unresolved) confirms FastConformer → ONNX export fails at the decoder step (tokens.txt empty). FastConformer via sherpa-onnx is NOT a viable Flutter path. Keep on-device path = `KheemP/whisper-base-quran-lora` ONNX; use FastConformer server-side via NeMo for maximum accuracy.

### Insight 2 — TajweedAI (NeurIPS 2025 Muslims in ML, GitHub: nmazid121/tajweedAI): CTC Forced Alignment Is the Tajweed Detection Architecture Template
Hybrid system: `nvidia/stt-ar-fastconformer.hybrid-large.pcd-v1.0` → `ctc-forced-aligner` → binary Qalqalah classifier. Achieves 100% internal accuracy / 57.14% external accuracy on Qalqalah detection (overfitting on small dataset). This is the **first published system using CTC forced alignment for tajweed rule detection** — gives phoneme-level timestamps so you know exactly which millisecond a Qalqalah letter was pronounced and whether the echo was present. Architecture template for all Sakīna tajweed classifiers. Fix: retrain with 5× more data using `Buraaq/quran-md-ayahs` 77,429 word-level clips.

---

## 2026-06-21 Weekend Day (18:03 UTC) — Implementation Focus: Working Code TODAY (Round 10)
*Full report: [research/2026-06-21/weekend-day-1803.md](2026-06-21/weekend-day-1803.md)*

### Insight 1 — offline-tarteel v0.1.0 (Mar 1, 2026, 220 ⭐): Three-Phase Architecture Is the Blueprint for Sub-500ms Verse ID
`yazinsai/offline-tarteel` published its first release March 1, 2026 (248 total commits). The project's `RESEARCH-audio-to-verse.md` (dated Feb 24, 2026) specifies a production-grade three-phase hybrid pipeline: (1) **WavLink/HuBERT embeddings + FAISS** for <500ms identification — pre-compute embeddings for all 6,236 verses × multiple reciters = **6–128 MB on-device** with quantization; (2) **Moonshine v2 Arabic streaming ASR** with word-level timestamps for real-time position tracking; (3) **Full Whisper fallback** for low-confidence cases. Live browser demo at offline-tarteel.whhite.com achieves 89.3% recall with 300ms audio chunks. React Native uses ONNX Runtime Mobile — same ONNX weights work in Flutter via sherpa-onnx. This is the most complete public architecture spec for a Quran verse ID system found across 10 rounds of research.

### Insight 2 — CrisperWeaver 10s/3s Sliding Window: Production Mic Streaming Pattern for Continuous Arabic ASR
`CrispStrobe/CrisperWeaver` (21 ⭐, v0.7.9 June 12, 2026, Flutter 3.44, AGPL-3.0) uses a **10-second sliding window with 3-second step** for continuous mic transcription. This is the specific windowing pattern that solves continuous Arabic recitation without VAD silence detection requirements — each window overlaps the previous by 7 seconds, ensuring no phoneme is cut at a window boundary. The MIT-licensed `CrispStrobe/CrispASR` backend (327 ⭐, ggml, 26 backends, June 14, 2026) can be wrapped in Flutter via FFI without AGPL implications. Study, do not copy, the CrisperWeaver window/step parameters for Sakīna's streaming pipeline.

### Insight 3 — Moonshine Arabic Base (8,500 ⭐, Apache-2.0): 5.63% WER + Real-Time Streaming, Best On-Device Option
`moonshine-ai/moonshine` (8,500 ⭐) provides an Arabic Base model (58M params, 5.63% WER) with bounded-latency streaming architecture that emits partial tokens **while the user is still speaking** — architecturally superior to batch Whisper for live verse tracking. Pre-quantized ONNX variant `sherpa-onnx-moonshine-base-ar-quantized-2026-02-27` is already in the sherpa-onnx model zoo — drop into `flutter-examples/streaming_asr`, zero conversion work. Flutter not listed as a first-class target but Android/iOS native routes exist; sherpa-onnx ONNX path is the clean Flutter integration.

### Insight 4 — Groq Exact Pricing Locked: $0.000667/min → 10,000 Verse Checks for $0.55 (Re-Confirmed Round 10)
Groq Whisper Large v3 Turbo: **$0.04/hr = $0.000667/min**. At 5s per verse check: 10,000 checks = 13.9 audio-hours = **$0.55 total**. Free tier: 2,000 req/day + 7,200 audio-sec/hr, no credit card. Endpoint: `POST https://api.groq.com/openai/v1/audio/transcriptions`, `language=ar`, OpenAI-compatible. Running at 228× real-time — an hour of recitation audio processes in ~15 seconds. The cost floor for Sakīna's entire early beta is under $5. Switch `baseUrl` in the `openai` Dart package to point at Groq; zero other code changes needed.

---

## 2026-06-21 Weekend Day (15:03 UTC) — Implementation Focus: Working Code TODAY (Round 9)
*Full report: [research/2026-06-21/weekend-day-1503.md](2026-06-21/weekend-day-1503.md)*

### Insight 1 — CrisperWeaver (Jun 12, 2026, 21 ⭐): First Flutter Offline ASR App With Confirmed Arabic + Active 2026 Dev
`CrispStrobe/CrisperWeaver` (AGPL-3.0, Flutter 3.44) is a fully-offline Flutter ASR app supporting 40+ ASR families including FunASR (31 languages, Arabic included) and Arabic TTS ("Lahgtna"). Last commit June 12, 2026 v0.7.9 — actively maintained and the only Flutter ASR reference app found with confirmed Arabic support updated in 2026. **AGPL-3.0 blocks code reuse in a proprietary product** — study the architecture (`lib/` structure, CrispASR engine wiring) without copying code. The CrispASR C++ backend supports on-device Arabic inference via ggml, which is the Flutter offline Arabic path to prototype next.

### Insight 2 — Tadabur Dataset (fherran/tadabur, 162 ⭐, Apr 2026): 1,400h + 600 Reciters — Biggest Public Quran ASR Corpus
`fherran/tadabur` provides 1,400+ hours of Quranic audio from 600+ distinct reciters with word-level timestamp alignment (JSON metadata). CC BY-NC 4.0 for research. Dataset on HuggingFace at `FaisaI/tadabur`. Includes `Tadabur-Whisper-Small` — Whisper-Small fine-tuned specifically on Tadabur data, domain-adapted for tajwīd, prolonged phonemes, and recitation style diversity. **No WER benchmark published yet but training data is 3–4× larger than any prior Quran ASR corpus.** Evaluate `Tadabur-Whisper-Small` vs. `tarteel-ai/whisper-base-ar-quran` (5.75% WER) on a held-out recitation set before choosing the Sakīna on-device model.

### Insight 3 — MaddoggProduction Whisper-Turbo-Quran-LoRA: Diacritized Output = Tajweed Diff at Character Level
`MaddoggProduction/whisper-l-v3-turbo-quran-lora-dataset-mix` fine-tunes Whisper Large v3 Turbo (4-layer decoder) on 3 Quran datasets with curriculum learning + augmentation. WER 12.69% — higher than KheemP (5.98%) but it outputs **fully diacritized Arabic (tashkeel)**. This makes it uniquely useful for Tajweed feedback: diff expected diacritized text vs. recognized diacritized text at the character level → pinpoint exactly which vowel/sukun/shadda was mispronounced. No other public Quran ASR model outputs tashkeel by default. Use alongside KheemP (better WER) for verse identification + MaddoggProduction for diacritized character-level feedback.

### Insight 4 — `record` v7.1.0 + Groq Free Tier = Still the Fastest Path to Working Demo (Re-Confirmed Round 9)
Confirmed again from new sources: `record` v7.1.0 (pub.dev, June 10, 2026, 717k downloads, BSD-3-Clause) is the sole viable Flutter mic streaming package in 2026. Groq free tier (2,000 req/day, 7,200 audio-sec/hr, `language=ar`, OpenAI-compatible) remains the zero-cost cloud ASR backbone. These two components alone are sufficient for a working Sakīna prototype today. New addition this round: `Voicegain` offers **15,000 free minutes** on signup — more credits than Groq for a single burst testing session.

---

## 2026-06-21 Weekend Day (12:05 UTC) — Implementation Focus: Working Code TODAY (Round 8)
*Full report: [research/2026-06-21/weekend-day-1205.md](2026-06-21/weekend-day-1205.md)*

### Insight 1 — flutter_sound 9.30.0 Has `record_to_stream` for PCM16 → ASR Pipeline (75k Downloads/wk)
`flutter_sound` v9.30.0 (6 months ago, 1,640 likes, 75k weekly downloads, MPL-2.0) is the highest-usage Flutter audio package with `record_to_stream` that pipes PCM Float32 or PCM Int16 directly to a Dart `StreamController<Food>`. Call `startRecorder(toStream: sink, codec: Codec.pcm16, numChannels: 1, sampleRate: 16000)` and every mic buffer arrives as bytes — pipe to Groq API or sherpa-onnx. However, **`record` v7.1.0 (from Round 7) is still the better choice** for simple `startStream()` API. Use flutter_sound only if you need simultaneous record+playback or Web support.

### Insight 2 — KheemP/whisper-base-quran-lora Achieves 5.98% WER — Best Public Quran ASR Model
LoRA fine-tune of Whisper-base specifically on Quranic recitation with diacritic sensitivity. Test WER ≈ 5.98%. Available on HuggingFace Hub with Inference API access. This is the smallest (fastest/cheapest) model achieving sub-6% WER on Quran text — ideal for the Groq free tier pipeline or self-hosted inference. Pair with `Buraaq/quran-audio-text-dataset` (264,509 MP3s, 30 reciters, NeurIPS 2025) for further fine-tuning with diverse tajweed styles.

### Insight 3 — sherpa-onnx v1.13.3 (June 2026, 13.1k ⭐) Still Has No Arabic Streaming Model — Use Offline Whisper Path
sherpa-onnx `streaming_asr` Flutter example is rock-solid and updated (latest release June 18, 2026), but Arabic streaming models do NOT exist in its model zoo. Whisper in sherpa-onnx is **offline/batch only** (non-streaming). The practical workaround for Sakīna: buffer ~5–10 seconds of mic audio via `record` → pass to `SherpaOnnxOfflineRecognizer` with `whisper-large-v3` ONNX weights → display result. Fully on-device, no network. Use `export-onnx.py` from previous Round 6 to convert `tarteel-ai/whisper-base-ar-quran` to ONNX for the best Quran accuracy.

### Insight 4 — Groq Free Tier Confirmed: 2,000 req/day + 7,200 audio-sec/hr for Arabic Whisper
Re-confirmed from multiple 2026 sources: Groq free tier gives 2,000 audio transcription requests/day and 7,200 audio-seconds/hour — no credit card. Whisper Large v3 Turbo: $0.04/hr (paid). Whisper Large v3: $0.111/hr (paid). Both support `language=ar`. OpenAI-compatible endpoint: `POST https://api.groq.com/openai/v1/audio/transcriptions`. For Sakīna development and early beta, this is the zero-cost cloud ASR backbone. Groq runs Whisper at 164–228× real-time speed.

---

## 2026-06-21 Weekend Day (09:04 UTC) — Implementation Focus: Working Code TODAY (Round 7)
*Full report: [research/2026-06-21/weekend-day-0904.md](2026-06-21/weekend-day-0904.md)*

### Insight 1 — Minimum Viable Pipeline Is ONE Day Away: `record` + `quran-ai-transcriping` + Groq
The fastest working Sakina prototype requires only 3 components, all confirmed active in 2025-2026: (1) `record` v7.1.0 (pub.dev, 10 days ago) for Flutter mic streaming → PCM16 bytes, (2) `sayedmahmoud266/quran-ai-transcriping` (FastAPI, Oct 2025, 25 ⭐) which wraps `tarteel-ai/whisper-base-ar-quran` + RapidFuzz verse matching → returns exact Surah + Ayah + timestamp for any recitation, (3) Groq API free tier (2,000 req/day, no card) as cloud ASR. Clone the FastAPI repo, run locally, point Flutter HTTP client at `localhost:8000`. No GPU, no model conversion, no ONNX. **Working end-to-end verse detection in hours.**

### Insight 2 — `record` v7.1.0 Is the Only Flutter Mic Package Worth Using in 2026
Published ~June 11, 2026 (10 days ago). 884 likes, 160 pub points, 717k downloads. BSD-3-Clause license. `startStream()` returns `Stream<Uint8List>` in PCM16 — pipes directly into sherpa-onnx and into HTTP chunked uploads to Groq/local API. Cross-platform: Android, iOS, Windows, macOS, Linux, Web. `mic_stream` is 15 months stale AND GPL-3.0 (license risk for proprietary app). `yasinarik/mic_stream_recorder` (created June 10, 2025, 3 ⭐) is too new/untested. `record` wins on every axis.

### Insight 3 — obadx/recitation-segmenter-v2 + obadx/muaalem-model-v3_2 = Segmentation + Pronunciation Error Stack
`recitation-segmenter-v2` (HuggingFace) is Wav2Vec2Bert fine-tuned at 20ms frame resolution — splits continuous recitation at waqf pause points without needing Ayah boundaries. Trained on 850+ hours / 300K annotated utterances. `muaalem-model-v3_2` (also obadx) goes further: detects actual pronunciation errors in learners. Together these two models handle (a) where to split the audio stream and (b) whether the segment was recited correctly. The segmenter is the missing preprocessing layer before verse matching that quran-ai-transcriping does not yet include.

### Insight 4 — Groq Free Tier Is the Production-Viable API Backbone: 2,000 req/day + 7,200 sec/hr Arabic Whisper
Groq's Whisper Large v3 Turbo at `https://api.groq.com/openai/v1/audio/transcriptions` runs at 228× real-time speed, supports Arabic (`language=ar`), and has a free tier of 2,000 requests/day + 7,200 audio seconds/hour — no credit card. API is OpenAI-compatible: same `openai` Dart package, just change `baseUrl`. At 2,000 req/day free, that's enough for ~2,000 verse checks/day for development and early users. Paid tier is $0.04/hr — dramatically cheaper than OpenAI ($0.36/hr) and Speechmatics for the same Whisper model.

---

## 2026-06-21 Weekend Day (06:03 UTC) — Implementation Focus: Working Code TODAY (Round 6)
*Full report: [research/2026-06-21/weekend-day-0603.md](2026-06-21/weekend-day-0603.md)*

### Insight 1 — FunASR v1.3.11 (June 20, 2026) Provides a Self-Hosted OpenAI-Compatible Endpoint
`modelscope/FunASR` (18,400 ⭐, MIT, v1.3.11 released literally yesterday) now ships an OpenAI-compatible REST endpoint via `funasr-server --device cuda → localhost:8000`. `docker run modelscope/funasr:latest` spins it up in minutes. Flutter calls `http://your-vps:8000/v1/audio/transcriptions` — same code as Groq prototype, just a different base URL. Self-hosted on a $5/mo VPS for a low-volume prototype. **Arabic WER on Quranic text is unverified** — benchmark before trusting for verse matching. Also has WebSocket streaming at `runtime/python/websocket/` for real-time token flow.

### Insight 2 — sherpa-onnx's export-onnx.py Script Converts Tarteel Fine-Tune to ONNX
`sherpa-onnx/scripts/whisper/export-onnx.py` takes any HuggingFace Whisper variant and exports it to ONNX. Running `python export-onnx.py --model tarteel-ai/whisper-base-ar-quran` converts Tarteel's 5.75% WER Quran model into a file that drops directly into the `flutter-examples/streaming_asr` assets folder. **This closes the last gap in the offline pipeline**: best Quran accuracy + fully on-device + no cloud cost. The Moonshine Arabic model (5.63% WER) remains the faster real-time option; the Tarteel ONNX export is the accuracy-focused batch/VAD-chunked option.

### Insight 3 — Word-by-Word Quran ASR Model Exists: 7.9% WER, Callable via HF Inference API
`HamzaSidhu786/wav2vec2-base-word-by-word-quran-asr` achieves 7.9% WER with word-level granularity — it outputs one word at a time, not full ayahs. Input: 16kHz WAV. This is the closest public implementation of Sakīna's word-level tajweed feedback differentiator ("you mispronounced كَلِمَة at position 3") vs. Tarteel's ayah-level feedback. No live HuggingFace Space, but callable via HF Inference API free tier. Combine with `Hetchy/quranic-phonemizer` (NeurIPS 2025, from Round 5) to map mispronounced word → expected phonemes → specific tajweed rule violated.

### Insight 4 — mic_stream Stable Release Is 15 Months Old — Use `record` v7.1.0 Only
`mic_stream` 0.7.2 was published March 2025 (15 months ago). A `0.7.3-dev` prerelease exists but no stable update. `sound_stream` last updated September 2025. **Only `record` v7.1.0 (June 2026, 717k downloads, 160 pub points) is actively maintained in 2026.** This confirms previous runs: `record` is the sole safe choice for production Flutter mic streaming. The sherpa-onnx `streaming_asr` example already uses it as the audio layer.

---

## 2026-06-20 Weekend Night (23:42 UTC) — Competitor Apps + Phoneme Layer + FastConformer Architecture
*Full report: [research/2026-06-20/weekend-night-2342.md](2026-06-20/weekend-night-2342.md)*

### Insight 1 — Wartilho (Jun 17, 2026): 3-Model On-Device Architecture Proves the Full Tajweed Pipeline
Android app by Mohamed Marzouk: three ONNX models on-device — Conformer CTC ASR + tajweed verifier + tajweed duration (madd timing). Metrics: **5.82% WER, 1.21% CER, 68% exact ayah match, 23ms inference**. This is the first public proof that a complete on-device ASR+tajweed pipeline (including madd duration analysis) is achievable in a mobile app without cloud. The tajweed_duration model for madd analysis is unique — no other open-source project has published this component. Sakīna should study `MrConnect/Wartilho` as the reference architecture for its Flutter on-device pipeline.

### Insight 2 — Quranic-Phonemizer (NeurIPS 2025) Closes the "Which Tajweed Rule?" Gap
`Hetchy/quranic-phonemizer` (23 ⭐, MIT, live demo at quranicphonemizer.com, NeurIPS 2025 Muslims in ML paper). Converts Quranic text to 69-71 IPA-based phonemes per word with tajweed rule annotations (Iqlab, Idgham, Ikhfaa, Qalqala, Tafkheem, Madd, Ghunnah). The missing piece in every Quran ASR system: ASR gives wrong output → Phonemizer gives expected phonemes → diff → map to specific tajweed rule violated → tell user "you missed Ikhfaa at word 3." No custom model needed — pure alignment + rule lookup. MIT licensed.

### Insight 3 — FastConformer ONNX Is Now Available for On-Device Quran ASR (Jun 14, 2026)
`moabdelmoez/fastconformer-quran-onnx` (Jun 14, 2026) converts a FastConformer Quran model to ONNX. Wartilho's 23ms on Conformer CTC beats whisper-base-ar-quran speed by ~10-20× at comparable accuracy. The streaming variant (`Muno459/fastconformer-quran-streaming`) from awesome-quran-ai is the architecture to benchmark against Moonshine Arabic and sherpa-onnx. If FastConformer streaming beats Moonshine on a Quranic test set, it becomes Sakīna's primary on-device inference path.

### Insight 4 — Tarteel's AGPL Licensing Is Creating Community Friction (Active Jun 2026)
TarteelAI/quranic-universal-library (AGPL-3.0, 890 ⭐, 124 open issues) has 3 active commercial licensing inquiries (issues #588, #589, #638 — Apr–Jun 2026) from iOS and open-source app developers who need to build on Tarteel's translation and glyph data commercially. Tarteel's restrictive AGPL is slowing the ecosystem. Sakīna releasing its fine-tuned model under MIT/Apache and using Tanzil + Tadabur (CC-BY-NC) data could pull community developers away from Tarteel's data dependency — a strategic positioning move that costs nothing.

---

## 2026-06-20 Weekend Night (22:34 UTC) — Tarteel Sentiment + New Arabic AI Startups
*Full report: [research/2026-06-20/weekend-night-2234.md](2026-06-20/weekend-night-2234.md)*

### Insight 1 — CNTXT AI Acquires Actualize (June 4, 2026): Arabic Voice Agent Stack Is Consolidating
CNTXT AI (UAE) acquired Actualize, adding dialect-aware Arabic **voice agents** (bookings, transactions, tasks via Arabic voice) to its Munsit ASR platform. Arabic voice is evolving from transcription → full AI agents. GCC conversational AI market projected $400M (2025) → $2.5B by 2034. Still B2B/government only, no Quran vertical, no consumer UX — Sakīna's window intact but the infrastructure is rapidly maturing around it. Watch 12-18 month horizon.

### Insight 2 — Tarteel's Notification System Is Broken — Friday/Nightly Reminders Silently Fail
Users confirm Friday Surah Al-Kahf reminders and nightly Surah Al-Mulk notifications are not firing in 2026. For a memorization app, a broken habit loop = churn. The Feb 2026 UI update also regressed single-tap navigation to two-tap (reading/listening mode switch, bookmarks). These are active user pain points Sakīna must treat as Day 1 quality bars: notifications that actually fire + no UI regression post-update.

### Insight 3 — Qwen3-ASR 1.7B (Jan 29, 2026): SOTA Open-Source ASR, Arabic WER on Quran Unpublished
Alibaba released Qwen3-ASR 1.7B on January 29, 2026 — outperforms Whisper-large-v3, GPT-4o, Gemini-2.5 on standard ASR benchmarks. Supports 52 languages including Arabic. Live HuggingFace Space: `Qwen/Qwen3-ASR`. Now on Azure AI Foundry. **Critical gap:** no published WER on Quranic Arabic. Sakīna should benchmark Qwen3-ASR against `KheemP/whisper-base-quran-lora` (5.98% WER) on a Quranic test set — this is a publishable result that establishes technical credibility while finding a potentially superior model.

### Insight 4 — Verse-to-Verse Audio Gap Is a Tarteel Architecture Bug, Not UX
Tarteel serves each ayah as a separate audio clip, causing noticeable pauses between verses even when a sheikh recites them in a single breath. This is an audio architecture failure (clip-per-verse vs. streaming audio). Sakīna using HLS or WebRTC streaming reciter audio avoids this entirely — advantage comes for free from choosing the right architecture, not from extra engineering.

---

## 2026-06-20 Weekend Day (18:06 UTC) — Implementation Focus: Working Code TODAY (Round 5)
*Full report: [research/2026-06-20/weekend-day-1806.md](2026-06-20/weekend-day-1806.md)*

### Insight 1 — sherpa-onnx `streaming_asr` Flutter Example + Arabic Models = Copy-Paste Pipeline
`k2-fsa/sherpa-onnx` (13,100 ⭐, last commit June 18, 2026) ships `flutter-examples/streaming_asr/` — a complete Flutter app: `record` mic → sherpa-onnx online recognizer → real-time tokens. Swap `online_model.dart` to `sherpa-onnx-moonshine-base-ar-quantized-2026-02-27` (5.63% WER) or `sherpa-onnx-cohere-transcribe-14-lang-int8-2026-04-01` for Arabic. **One file change from a working Arabic ASR Flutter app.** Add `sherpa_onnx` (pub.dev v1.13.3, June 2026) + `record` v7.1.0 (160 pub points, 717k downloads) — done.

### Insight 2 — Tarteel HuggingFace Space Is a Live Quranic ASR Demo (No Code Needed)
https://huggingface.co/spaces/tarteel-ai/demo-whisper-base-ar-quran is a live inference UI for `tarteel-ai/whisper-base-ar-quran` (5.75% WER, diacritics preserved). Test any Quran recitation audio right now — paste URL or upload file. This is the quality benchmark Sakīna must match or beat. Model is also callable via HF Inference API with a free token for prototype validation.

### Insight 3 — Diacritics (Tashkeel) Preservation Is the Critical API Selection Filter
Every major commercial API (Deepgram Nova-3, AssemblyAI, OpenAI Whisper, ElevenLabs Scribe v2) strips tashkeel from Arabic output. For Quranic verse matching, diacritics are required. **Only Tarteel's self-hosted models preserve them.** Deepgram Nova-3 ($200 free credits, Jan 2026 Arabic launch, 40% lower WER than competitors) is excellent for general Arabic but needs a diacritization post-processing layer for verse matching. AssemblyAI ($0.0025/min, $50 free) is cheapest for budget experiments. Groq (2,000 req/day free, no card) remains best zero-cost prototype.

### Insight 4 — CrisperWeaver (June 12, 2026, Flutter) + CrispASR (327 ⭐) Show Runtime Model-Swap Architecture
`CrispStrobe/CrisperWeaver` (AGPL-3.0, last commit June 12, 2026) is a working Flutter app bundling Qwen3-ASR and Gemma4-E2B for Arabic alongside 40+ other ASR families. Its model browser + download queue + offline inference architecture is the reference implementation for Sakīna's offline/cloud toggle and model selection UX. Backend is `CrispStrobe/CrispASR` (MIT, 327 ⭐, June 14, 2026 — ggml C++ runtime with 26 backends).

---

## 2026-06-20 Weekend Day (15:04 UTC) — Implementation Focus: Working Code TODAY (Round 4)
*Full report: [research/2026-06-20/weekend-day-1504.md](2026-06-20/weekend-day-1504.md)*

### Insight 1 — sherpa-onnx + `record` Package = Complete Offline Flutter ASR Stack, Both Confirmed June 2026
`k2-fsa/sherpa-onnx` (13,100 ⭐, latest release June 18 2026) provides on-device streaming ASR for Android/iOS/desktop via ONNX Runtime. `record` v7.1.0 (717k downloads, published ~June 10 2026) is the Flutter microphone layer — it outputs `Stream<Uint8List>` in PCM16 at 16kHz, which pipes directly into sherpa-onnx. **This is the complete offline stack**: `flutter pub add record sherpa_onnx`, copy `flutter-examples/streaming_asr` as starting point, drop in any Arabic ONNX model. Both packages actively maintained in 2026.

### Insight 2 — Groq API Offers 2,000 Free Req/Day + 7,200 Audio Sec/Hour — Best Cloud Prototype Path
Groq's Whisper Large v3 Turbo endpoint (`https://api.groq.com/openai/v1/audio/transcriptions`) supports Arabic (`language=ar`), is OpenAI-compatible, runs at 164× real-time on Groq's LPU hardware, and has a free tier of 2,000 requests/day and 7,200 audio-seconds/hour (no credit card). Use this to validate the Sakīna pipeline in hours — no model download, no ONNX setup. Groq's free tier = ~2,000 Quran verse checks/day at zero cost.

### Insight 3 — Three Quran-Specific Whisper Fine-Tunes Are Ready on Hugging Face
(a) `MaddoggProduction/whisper-l-v3-turbo-quran-lora-dataset-mix`: 12.69% WER, LoRA-fine-tuned on Whisper-v3-Turbo — best for low-latency live use. (b) `tarteel-ai/whisper-base-ar-quran`: WER ~5.75%, lightweight (74M params), live demo at HuggingFace Spaces `tarteel-ai/whisper-tiny-demo-quran`. (c) `Habib-HF/tarbiyah-ai-whisper-medium-merged`: merged two fine-tunes for broader coverage. All three export to ONNX for sherpa-onnx integration. `tarteel-ai/whisper-base-ar-quran` available via free HuggingFace Inference API for immediate cloud testing.

### Insight 4 — `vosk-flutter` (75 ⭐) Skipped: No iOS Support, Stale Since 2024
VOSK provides offline Arabic ASR (50MB model) with streaming API, but `alphacep/vosk-flutter` explicitly does NOT support iOS and shows no 2025-2026 commit activity. Eliminated from Sakīna consideration. The sherpa-onnx path covers the same offline streaming use case with full iOS support and active 2026 maintenance.

---

## 2026-06-20 Weekend Day (12:08 UTC) — Implementation Focus: Working Code TODAY (Round 3)
*Full report: [research/2026-06-20/weekend-day-1208.md](2026-06-20/weekend-day-1208.md)*

### Insight 1 — sherpa-onnx Arabic Model + Flutter streaming_asr = Offline Pipeline Ready NOW
`k2-fsa/sherpa-onnx` v1.13.3 (13,100+ ⭐, released ~June 15 2026) ships `flutter-examples/streaming_asr/lib/streaming_asr.dart` — a complete Flutter mic-to-ASR pipeline. The model `sherpa-onnx-moonshine-base-ar-quantized-2026-02-27` (released Feb 27, 2026) is a quantized Arabic Moonshine Base (58M params, 5.63% WER) that drops directly into this example. **This is the offline Sakīna pipeline: `record` v7.1.0 → sherpa_onnx → Arabic Moonshine model.** No API, no cloud, fully on-device. Copy `streaming_asr.dart`, swap the model, done.

### Insight 2 — Deepgram Nova-3 Gives $200 Free Credits (~750 hrs) — Best Cloud Starter
Deepgram Nova-3 Arabic (`language=ar`) offers $200 in free credits (no credit card) — enough for ~750 hours of streaming Arabic transcription. Supports 17 dialect variants including `ar-SA` and `ar-EG`. 40% lower WER than competitors on conversational Arabic. Use as the cloud fallback during development before investing in on-device fine-tuning. Groq free tier (2,000 req/day) is simpler but Deepgram's credit depth is more useful for sustained testing.

### Insight 3 — IqraEval 2025 (ArabicNLP) Published Phoneme-Level Tajweed MDD Models
First open benchmark for Mispronunciation Detection & Diagnosis in Quranic recitation. Multiple teams published models including a Whisper-large-v3 adapted as a speech-to-phoneme model. `huggingface.co/spaces/IqraEval/SharedTask_ArabicNLP2025` — this is the phoneme-layer Sakīna needs on top of ASR to tell users which tajweed rule was violated, not just that something was wrong.

### Insight 4 — yayaiu6 Repo Has Copy-Pasteable Verse Alignment Logic
`yayaiu6/Real-Time-Quran-recitation-tracker-System` (121 ⭐) implements Levenshtein + Needleman-Wunsch alignment against `hafs_smart_v8` JSON for word-level Quran tracking. Python but directly portable to Dart. This is the verse-matching algorithm for Sakīna — pair it with the sherpa-onnx output stream and you have word-by-word live position tracking.

---

## 2026-06-20 Weekend Day (09:04 UTC) — Implementation Focus: Working Code TODAY (Round 2)
*Full report: [research/2026-06-20/weekend-day-0904.md](2026-06-20/weekend-day-0904.md)*

### Insight 1 — offline-tarteel Is the Best Ready-to-Use Quran ASR Demo Running Today
GitHub: `yazinsai/offline-tarteel` (221 ⭐). Identifies surah/ayah from recited audio fully offline. NVIDIA FastConformer quantized to ONNX (131 MB), sub-second latency, 95%+ recall. Live demo at https://offline-tarteel.whhite.com (FastAPI + React). Crucially: the ONNX model runs in React Native — **Flutter port via same ONNX model + sherpa-onnx is a direct path.** This is the verse-matching engine Sakīna needs.

### Insight 2 — yayaiu6/Real-Time-Quran-recitation-tracker Shows Production Word-Alignment Architecture
GitHub: `yayaiu6/Real-Time-Quran-recitation-tracker-System` (121 ⭐). Python/web, two ASR backends: (1) Groq Whisper API (cloud, 216× real-time), (2) NVIDIA NeMo Conformer-CTC local (60% faster than Whisper). Has word-level alignment + error detection + adaptive feedback. YouTube demo available. Mine its alignment logic for Sakīna's tajweed feedback layer.

### Insight 3 — Tadabur: Best Dataset + Fine-Tuned Model for Training Custom Quran ASR
GitHub: `fherran/tadabur` (161 ⭐). 1,400+ hours, 600+ reciters, word-level timestamps. `FaisaI/tadabur-Whisper-Small` on HuggingFace is ready for inference now. CC BY-NC 4.0. This is the training corpus if we need to fine-tune a model beyond Tarteel's whisper-base. Pair with `tarteel-ai/whisper-base-ar-quran` as baseline.

### Insight 4 — Groq Whisper is the Optimal Cloud API: Free Tier + 216× Real-Time Speed
2,000 req/day free, no credit card. Whisper Large v3 Turbo at $0.04/hr paid. Arabic is natively supported. Use as cloud fallback in Sakīna when on-device inference is too slow on low-end phones. Pair with on-device sherpa-onnx path for premium experience.

### Insight 5 — CrisperWeaver (June 12 2026, Flutter) Shows How to Bundle 40+ ASR Families
GitHub: `CrispStrobe/CrisperWeaver` (21 ⭐, last commit June 12, 2026). On-device Flutter app, Dart 88.9%, AGPL-3.0. Bundles Whisper, Qwen3-ASR, Gemma4-E2B. Architecture is a working reference for how to swap ASR models at runtime in Flutter — relevant for Sakīna's offline/cloud toggle.

---

## 2026-06-20 Weekend Day (07:20 UTC) — Implementation Focus: Working Code TODAY
*Full report: [research/2026-06-20/weekend-day-0720.md](2026-06-20/weekend-day-0720.md)*

### Insight 1 — sherpa-onnx Has a Working Flutter Streaming ASR Example + Arabic Model (2026-04-01)
`k2-fsa/sherpa-onnx` (13.1k ⭐, actively maintained) ships `flutter-examples/streaming_asr/` — a fully wired Flutter app that pipes mic → ONNX model → real-time tokens. The `sherpa-onnx-cohere-transcribe-14-lang-int8-2026-04-01` model released April 2026 explicitly includes **MENA: Arabic**. Drop this model into the existing example and you have on-device Arabic streaming ASR in Flutter today — zero API cost, works offline. Caveat: multilingual model, not Quran-fine-tuned.

### Insight 2 — tarteel-ai/whisper-base-ar-quran Is Free via HuggingFace API Right Now
POST audio bytes to `https://api-inference.huggingface.co/models/tarteel-ai/whisper-base-ar-quran` with a free HF token. Returns Arabic text with diacritics. WER 5.75% — best confirmed accuracy on Quranic Arabic. Free tier supports ~100–300 req/hr. Use this as the cloud fallback while sherpa-onnx runs on-device. For production scale: upgrade to HF Inference Endpoints (~$0.50/hr GPU, scale-to-zero).

### Insight 3 — `quran-ai-transcriping` (Oct 2025, Python) Has Production-Quality Ayah Matching Logic Ready to Steal
GitHub: `sayedmahmoud266/quran-ai-transcriping` (25 ⭐, last commit Oct 2025). FastAPI backend using tarteel-ai/whisper-base-ar-quran + constraint propagation algorithm that fuzzy-matches transcription against all 6,236 verses with tashkeel, fills backward gaps, continues forward consecutively. 100% verse detection accuracy claimed. Self-host on Fly.io free tier for dev. No streaming (roadmapped) — pair with sherpa-onnx for real-time, call this API for verse ID confirmation.

### Insight 4 — `record` Flutter Package (v7.1.0, June 2026) Is the Microphone Layer to Use
Published 9 days ago. 884 likes, 717k downloads, 160 pub points. Streams `Stream<Uint8List>` in PCM16 or AAC across all 6 platforms (Android, iOS, macOS, Linux, Windows, Web). This is the de facto standard for mic streaming in Flutter and is what sherpa-onnx streaming example already integrates with. Do not use `mic_stream` (15 months stale) or `mic_stream_recorder` (file output only, not byte stream).

---

## 2026-06-20 Weekend Night (02:34 UTC)
*Full report: [research/2026-06-20/weekend-night-0234.md](2026-06-20/weekend-night-0234.md)*

### Insight 1 — Tarteel's Partial-Ayah ASR Bug + Bismillah Misidentification Still Unresolved in 2026
Confirmed via 2026 aggregated reviews: reciting from the middle or end of an ayah fails (only full-ayah or start-of-ayah works). Separately, any surah starting with Bismillah incorrectly snaps to Al-Fatiha and flags it as a mistake. These are known bugs Tarteel has not fixed. Sakina must solve partial-verse tracking from v1 — this is a concrete, fixable user pain point that is directly addressable with `obadx/recitation-segmenter-v2` + streaming ASR.

### Insight 2 — Qwen3-ASR 1.7B Is Now Used for Quran Phoneme Tasks at IQRA 2026
The Kalimat team at IQRA 2026 (Interspeech, Sydney) used Qwen3-ASR 1.7B to reframe MSA diacritization detection as direct speech-to-phoneme generation, treating 68 MSA phonemes as discrete special tokens. Qwen3-ASR is SOTA open-source across 52 languages. Sakina should benchmark Qwen3-ASR 1.7B against the current best (`KheemP/whisper-base-quran-lora` at 5.98% WER) — Qwen3 may surpass it on Quranic Classical Arabic.

### Insight 3 — QuranMB.v2 (arXiv:2603.29087) Is the Active Tajweed Evaluation Benchmark
The IQRA 2026 Interspeech Challenge released QuranMB.v2 — the expanded Quranic mispronunciation benchmark. Best system achieved +0.2787 F1 over baseline using generative large audio-language models. Sakina's tajweed detection pipeline must be evaluated against QuranMB.v2 to be credible to the research community and investable as a technical differentiator.

### Insight 4 — Arabic.AI + HeyBreez Partnership (Apr 2026): Enterprise Arabic Voice Stack Now Production-Grade
Arabic.AI (Tarjama, $15M raised) partnered with HeyBreez in April 2026 to bring production-grade Arabic voice AI to enterprises/governments. Their stack: 22-dialect STT, Arabic TTS, Arabic OCR, and agentic workflows — all Arabic-native. This is B2B-only with no Quran fine-tuning, but represents the most complete Arabic voice infrastructure available as a potential Sakina backend (evaluate vs. Munsit Edge and whisper-lora for cost/accuracy tradeoff).

---

## 2026-06-20 Weekend Night (01:35 UTC)
*Full report: [research/2026-06-20/weekend-night-0135.md](2026-06-20/weekend-night-0135.md)*

### Insight 1 — CONFIRMED: Tarteel's Tajweed AI Is Roadmap, Not Shipped
Tarteel's own Google Play app description explicitly states: "Currently, this feature does not include Tajweed or pronunciation correction, but we know there's community demand and it's on our roadmap." Their brand dominance is built on AI recitation feedback, yet the core tajweed correction feature remains unshipped. Quranlingo also lists AI tajweed correction as "upcoming." Sakina can be first to ship real tajweed correction in the consumer market — the window is open but 3–6 months wide.

### Insight 2 — Time Magazine (May 26, 2026): "AI Tools Are Transforming Muslim Worship" — Mainstream Signal
Time ran a feature on AI and Muslim worship featuring Tarteel being used at the Maryam Islamic Center in Houston during Ramadan's last ten nights. Hundreds of Muslims used it live for verse identification. This is the clearest signal yet that AI Quran tools are crossing into mainstream media coverage — the vertical is now investable and writable. Sakina's timing is aligned with the cycle.

### Insight 3 — Saudi Govt Al-Maqraa (March 2025) Validates Hybrid Human+AI Model
Saudi Arabia's Ministry-backed Al-Maqraa platform at the Grand Mosque uses AI recitation + teacher supervision in 10 languages (Arabic, English, Urdu, Indonesian, Malay, Hausa + others). It provides accredited certificates. Government-level validation of Sakina's core thesis — human-escalation over pure AI — is now in production at the world's most prestigious Islamic institution. Use this in pitch materials.

### Insight 4 — 0.16% Phoneme Error Rate Achieved (Sep 2025 arXiv 2509.00094)
A Deep Learning paper (850+ hours Quran audio, 300K annotated utterances, multi-level CTC model) achieved 0.16% average Phoneme Error Rate on Quranic recitation. This is the technical benchmark Sakina's ASR/tajweed pipeline should aim to match. The `KheemP/whisper-base-quran-lora` model (5.98% WER) is the fastest path to production; this paper's approach is the long-term ceiling to hit.

---

## 2026-06-20 Weekend Night (00:36 UTC)
*Full report: [research/2026-06-20/weekend-night-0036.md](2026-06-20/weekend-night-0036.md)*

### Insight 1 — RecitID ("Shazam for Quran") Launched March 2026 — Live Khutbah Translation Is Unexpected Moat
RecitID (recitid.ai) launched Google Play March 2026, updated May 2026. It identifies any playing recitation in ~18 seconds across 200+ reciters AND translates Friday khutbahs live in 38+ languages. Different use case from Sakina (identification vs. teaching), but their live Arabic audio pipeline is sophisticated. The live khutbah translation feature is a differentiator worth watching — no Quran recitation app has this. Sakina should monitor for potential teacher-session/community feature overlap.

### Insight 2 — QariAI Now Live on Both App Stores (Jan–Feb 2026) — Phoneme-Level Tajweed Is Real
QariAI (qariai.app) deployed on Google Play Jan 2026 and App Store Feb 2026. It corrects Makharij, Madd, Ikhfa, Idgham, Qalqalah at the phoneme level with waveform visualization and pitch accuracy graphs vs. 6 renowned reciters. This is more advanced than Tarteel's word-level detection. QariAI is now the strongest direct competitor in the AI-only recitation correction space. It still lacks live teacher sessions — Sakina's positioning window is intact but narrowing.

### Insight 3 — Tarteel Is Pushing GCC App Store Rankings (~June 2026) — MENA Push Active
Tarteel announced "top 10 in GCC App Stores" and is actively campaigning users to push it to #1. Combined with a French language launch, this signals Tarteel is in aggressive geographic expansion. Key weakness confirmed: partial-ayah ASR still fails (mid-verse/end-of-verse recognition unreliable). The paywall trust gap ($100/yr, paying users still get wrong flags) remains the dominant complaint on both app stores and aggregator review sites.

### Insight 4 — Munsit Edge (May 2026) + QariAI phoneme model = Two Buildable Sakina Components
CNTXT AI's Munsit Edge (May 2026) provides an on-device Arabic ASR SDK (no cloud, iOS/Android/Linux). QariAI has demonstrated that phoneme-level Tajweed correction is now shippable. Sakina can combine: (1) Munsit Edge or whisper-base-quran-lora (5.98% WER) for live ASR, + (2) Tajweed rule engine similar to QariAI's approach, + (3) live teacher escalation for uncertain cases. All three components are now proven in production by other teams.

---

## 2026-06-19 Weekend Night (23:35 UTC)
*Full report: [research/2026-06-19/weekend-night-2335.md](2026-06-19/weekend-night-2335.md)*

### Insight 1 — ProductHunt Recitation AI Gap Confirmed: Zero Live-Audio Quran Apps Launched 2025-2026
All Quran AI launches on ProductHunt in 2025-2026 are text/tafsir/Q&A tools (Nura AI Jun 2025, Ask Quran, Salam.chat, etc.). Not a single live recitation audio product. Sakina has zero ProductHunt competition in its exact category — this is a launch opportunity, not a crowded space.

### Insight 2 — CNTXT Munsit Edge (May 2026) Is the On-Device Arabic ASR to Track
UAE-built, on-device (no cloud), ~150ms latency, 24% WER across 5 dialect groups, SDK for iOS/Android/Linux/IoT. This is general Arabic ASR — no Quran fine-tuning. If Sakina wants offline live recitation checking, Munsit Edge SDK is the most mature on-device Arabic option available today. License cost unknown; evaluate vs. running whisper-base-quran-lora locally.

### Insight 3 — `whisper-base-quran-lora` Achieves 5.98% WER — Lowest Confirmed Quranic ASR Number
`KheemP/whisper-base-quran-lora` (HuggingFace) — diacritic-sensitive LoRA fine-tune of Tarteel's own open model. 5.98% test WER. This is lower than the 12.69% WER from the Whisper-l-v3-turbo mix. Sakina should benchmark both for speed-vs-accuracy tradeoff on live streaming recitation.

### Insight 4 — New arXiv Paper (Jul 2026) Benchmarks Open Models on Classical Arabic — Required Reading
arXiv:2507.13977 — *"Open Automatic Speech Recognition Models for Classical and Modern Standard Arabic"* (July 2026). Directly benchmarks open ASR on Quranic/Classical Arabic. This paper will determine model selection for Sakina's ASR pipeline.

---

## 2026-06-19 Weekend Night (22:35 UTC)
*Full report: [research/2026-06-19/weekend-night-2235.md](2026-06-19/weekend-night-2235.md)*

### Insight 1 — Tarteel's #1 User Complaint Is AI Accuracy + $100 Paywall Trust Gap
Verified via App Store reviews and support docs: paying users ($100/yr) see correctly recited verses flagged as wrong, a Bismillah misidentification bug, and subscription status not loading post-purchase. This is Sakina's primary positioning wedge — hybrid human+AI validation (teacher confirms what AI is uncertain about) directly answers the trust failure. Neither Tarteel nor its closest rival Qari AI offers live teacher sessions.

### Insight 2 — Qari AI Is Tarteel's Sharpest Challenger (Phoneme-Level Tajweed)
Qari AI (qariai.app) corrects Makharij, Madd, Ikhfa, Idgham, and Qalqalah in real time at the phoneme level — vs. Tarteel's word-level detection. Qari's own comparison page positions this as the decisive gap. Neither app has a live session/teacher feature. Still a blue ocean for Sakina.

### Insight 3 — Two Funded Arabic Speech Infra Players Could Pivot into Quran Consumer (18-Month Risk)
**Intella** (Egypt, $12.5M Series A Prosus, Sep 2025): 95.73% accuracy, 25+ dialects, B2B now but Prosus-backed with runway. **CNTXT AI Munsit** (Abu Dhabi, April 2026): 95.7% accuracy, 18 dialects, on-device Edge model in May 2026. Both are B2B enterprise today. Neither has Quran-specific fine-tuning or consumer UX. Sakina's window is real but not indefinite.

### Insight 4 — Tadabur Dataset (HuggingFace) Is the Training Corpus Sakina Needs
1,400+ hours of Quranic recitation audio from 600+ reciters, NeurIPS 2025 Muslims in ML Workshop. Combined with `whisper-l-v3-turbo-quran-lora` (12.69% WER, fast inference), this is the ready-made fine-tuning stack. Also: `obadx/recitation-segmenter-v2` handles waqf segmentation — critical for live verse tracking. No need to collect training data from scratch.

---

## 2026-06-19 Night (08:57 UTC)
*Full report: [research/2026-06-19/night-0857.md](2026-06-19/night-0857.md)*

### Insight 1 — Moonshine v2 Arabic is the Streaming ASR to Beat Tarteel With
Released Feb 23, 2026 (open weights, Apache 2.0). Ergodic streaming encoder with a dedicated Arabic model — delivers bounded time-to-first-token regardless of utterance length. At 27M parameters it fits on edge devices and outperforms WhisperLargeV3 on real-time benchmarks. This is architecturally superior to Tarteel's Whisper-based approach for *live* verse tracking, because Moonshine emits tokens while the user is still speaking. Immediate action: pull the Arabic weights, run on a 30-second Quran clip, measure TTFT vs. WhisperKit.

### Insight 2 — WavLink Embeddings Enable Sub-500ms On-Device Verse Fingerprinting for 6 MB
WavLink (January 2026) adds a single learnable global token to Whisper's encoder, compressing audio to a Matryoshka-style embedding as small as sub-100 dimensions. Pre-compute embeddings for all 6,236 Quran verses × 10 reciters = 62,360 embeddings = ~6–12 MB on-device. FAISS nearest-neighbor lookup identifies the surah/ayah before any decoding completes — this is Phase 1 of a three-phase pipeline that bypasses Tarteel's slow segment-level ASR approach entirely. Tarteel does not appear to use this technique.

### Insight 3 — IQRA 2026 (Interspeech, Sydney Sep 27–Oct 1) is the Active Benchmark for Quran Pronunciation AI
The second IqraEval challenge attracted 19 teams and released `Iqra_Extra_IS26`, the first real human mispronounced MSA speech corpus (prior work used only TTS). Best system achieved +0.2787 F1 over baseline using generative LALMs for mispronunciation diagnosis. Sakina must use this dataset and benchmark to train and evaluate tajweed error feedback — it is the only standardized, community-validated dataset for this exact problem. Hugging Face Space: `IqraEval/IqraEval_Interspeech_26`.
