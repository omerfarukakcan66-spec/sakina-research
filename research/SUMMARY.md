# Sakina Research — Rolling Summary

Cumulative top insights across all research runs, newest first.

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
