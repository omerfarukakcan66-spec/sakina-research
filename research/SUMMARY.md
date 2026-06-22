# Sakina Research — Rolling Summary

Cumulative top insights across all research runs, newest first.

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
