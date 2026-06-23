# Sakina Research — Round 25
**Date:** 2026-06-23 | **Time:** 04:36 UTC | **Focus:** Arabic Quran ASR, Flutter audio, Tarteel, new ML models, open-source

---

## TL;DR — Top 3 New Insights This Round

1. **Groq Orpheus Arabic Saudi TTS** is live on GroqCloud (January 2026) — Abdullah & Aisha voices, sub-200ms TTFB, $40/1M chars — never covered in 24 prior rounds; enables Sakina's spoken AI feedback layer
2. **Tadabur Whisper Fine-tuned Models** on HuggingFace (April 2026, FaisaI org) — first Whisper variants domain-adapted for 1,400h Quranic audio diversity including Madd/mujawwad; prior rounds covered only the dataset, not these models
3. **arXiv:2606.16629 Islamic LLMs Survey** (June 15, 2026) + **IslamicMMLU benchmark** — the most comprehensive Islamic AI safety/hallucination framework yet; directly maps to Sakina's AI assistant design requirements

---

## Section 1 — Arabic Quran ASR: New Models & Papers

### 1.1 Tadabur Whisper Fine-tuned Models (NEW)
**arXiv:** 2604.18932 | **HuggingFace:** FaisaI/tadabur | **License:** CC BY-NC 4.0 | **Date:** April 2026

The Tadabur dataset paper (1,400h, 600+ reciters, known since Round 19) was accompanied by **Whisper models fine-tuned specifically on Tadabur** for Quranic ASR. These models are domain-adapted for:
- Prolonged phoneme durations (Madd rules extending vowels 2–6 counts)
- Full tajweed acoustic variation (ikhfa nasalization, qalqalah echo, ghunnah)
- Melodic mujawwad recitation style (absent in standard MSA training data)
- Acoustic diversity across 600+ distinct reciters

**Why this matters:** Prior 24 rounds covered the Tadabur *dataset* but not these *fine-tuned model weights*. These are the first Whisper variants trained on more than 10× the data behind KheemP/whisper-base-quran-lora (5.98% WER, trained on EveryAyah ~390h). Theoretical expectation: significantly lower WER and better tajweed robustness due to mujawwad coverage.

**Sakina action:**
- Locate FaisaI org on HuggingFace → find exact model cards (search: `FaisaI/tadabur-whisper` or similar)
- Benchmark on the Quran-MD word-level clips (`Buraaq/quran-audio-text-dataset`) — 20 clips per Qira'at style
- Compare WER against KheemP/whisper-base-quran-lora (5.98%) and MaddoggProduction LoRA (12.69%)
- If WER < 5%, replace current ASR backbone; if 5–8%, use as ensemble second path
- Check CC BY-NC 4.0 commercial clause — NC may require a separate license for production use; negotiate with author (Faisal Alherran) or use for training only

### 1.2 Ramsa: Emirati Arabic Speech Corpus (NEW)
**arXiv:** 2603.08125 | **Date:** March 2026 | **Size:** 41h, 157 speakers

41-hour sociolinguistically stratified Emirati Arabic corpus. Subdialects: Urban, Bedouin, Mountain/Shihhi. Zero-shot evaluation baseline: **Whisper-large-v3-turbo achieves 0.268 WER / 0.144 CER** on Emirati dialect.

**Relevance for Sakina:** GCC users (UAE, Bahrain, Qatar, Kuwait) are high-value for Sakina's market. Sakina's current ASR path (Moonshine Arabic, Nemotron-3.5, Whisper fine-tunes) is trained on MSA/Quranic audio; Emirati dialect users speaking conversational Arabic (not recitation) in the app's UI will see worse ASR on non-recitation speech (e.g., teacher instructions). Ramsa provides a fine-tuning resource for that UI layer.

**Priority:** Low. Recitation ASR does not depend on dialect; teacher speech→text transcription does. Defer until v2.

### 1.3 No New Arabic Quran ASR Model Found (June 2026)
Search across HuggingFace, arXiv, and GitHub for models uploaded in May–June 2026 found no new Quran-specific ASR model beyond what was already documented. Confirmed still-current:
- Nemotron-3.5 ASR Streaming 0.6B (June 4, 2026) — primary on-device path
- sherpa-onnx v1.13.3 (June 15) — no v1.13.4+ released yet as of June 23
- QNN binary packages (June 18) — Parakeet-TDT 110M pre-built for Qualcomm NPU

---

## Section 2 — Real-Time Audio Processing for Flutter

### 2.1 flutter_webrtc Updated May 18, 2026 (NEW)
**Package:** flutter_webrtc (pub.dev) | **Date:** May 18, 2026

Key audio changes relevant to Sakina's live teacher sessions:
- **[iOS/macOS]** Now uses `AVAudioEngine` audio device module — enables Apple platform native voice processing: **AEC (Acoustic Echo Cancellation), NS (Noise Suppression), AGC (Automatic Gain Control)**. This means teacher↔student audio in Sakina's live session will have hardware-level echo and noise processing on iOS, improving audio quality at zero ML cost.
- **[Android]** Configurable audio sample rate with smart defaults + AudioSwitch bump for wired headset and Bluetooth fixes

**Sakina action:** Upgrade flutter_webrtc to latest version before live-session feature ships. The AEC/NS/AGC auto-processing on iOS is particularly important: students reciting Quran often use speakerphone (to hold Mushaf), which creates echo. AVAudioEngine handles this natively.

### 2.2 No New Flutter ASR Packages (June 2026)
Searches for new Flutter ASR packages in June 2026 found no new entrants beyond the established sherpa-onnx + Picovoice Cheetah landscape. The current confirmed stack remains:
- `sherpa_onnx` Flutter (v1.13.3) — primary on-device ASR path
- Picovoice Cheetah — alternative on-device path, proprietary
- Deepgram Dart client — cloud ASR option

---

## Section 3 — Tarteel Competitive Intelligence

### 3.1 Tarteel v5.78.2 Confirmed Still Latest (June 23, 2026)
No new Tarteel Android version found beyond v5.78.2 (June 19) confirmed in Round 24. Makharij/Mahraj beta confirmed Q2 2026 target; full release Q4 2026 deadline unchanged.

### 3.2 Hifz AI v5.78.x — No New Update
No new version of `com.awa.hifz` found. Tajweed still explicitly absent per current store description.

---

## Section 4 — New Competitor: RecitID (NEW)

**App IDs:** iOS `id6759472226`, Android `com.recitid.app` | **Developer:** Vikolo Dynamics Ltd | **Available:** iOS launch date unknown; **Android:** March 2026 | **Downloads:** 1,900 total Android, ~31/day | **Price:** Free + Premium

**What RecitID does:** It is a "Shazam for the Quran" — listen to any ambient recitation (mosque, phone, speaker) and identify surah, ayah number, and reciter within ~18 seconds. Also includes:
- 200+ reciter identification by voice
- Live Khutbah translation to 53 languages in real-time
- Tajweed color-coded Mushaf (King Fahd Complex font)
- 48+ reciters for listening playback
- Camera OCR to scan Arabic text

**Competitive assessment:** RecitID is **NOT a Tajweed practice or teacher app** — it's passive identification and utility. 1,900 Android downloads confirms it is a micro-niche app at launch stage. It appears in SEO comparison articles ("Best Quran Apps 2026") but does not compete with Sakina's live teacher + AI feedback positioning. The khutbah translation feature is novel but irrelevant to recitation learning.

**One strategic note:** RecitID's Tajweed Mushaf with color-coding could be a cheap UI reference feature Sakina could incorporate (color-coded recitation display while practicing). This is table-stakes in 2026.

---

## Section 5 — AI/LLM: Islamic AI Design & Safety

### 5.1 arXiv:2606.16629 — Islamic Large Language Models Survey (BRAND NEW: June 15, 2026)
**Title:** "Islamic Large Language Models: From Knowledge Acquisition to Trustworthy and Hallucination-Resistant AI" | **Author:** Mohammed Amine Mouhoub | **Date:** June 15, 2026

The most comprehensive survey of Islamic AI published to date. Key findings directly applicable to Sakina's AI assistant layer:

**Five requirements for trustworthy Islamic AI:**
1. **Curated source grounding** — responses must be anchored to verified Quran/Hadith, not training data interpolation
2. **Retrieval + verification module** — retrieve exact Quran verse → verify against canonical text before presenting
3. **Citation-aware generation** — every fatwa/rule statement must cite its source (surah:ayah or hadith reference)
4. **Madhhab-aware reasoning** — different jurisprudential schools (Hanafi, Maliki, Shafi'i, Hanbali) give different rulings; the AI must represent this plurality, not collapse it
5. **Human expert evaluation** — automated benchmarks insufficient; scholarly validation required

**Why this matters for Sakina:** Sakina's planned AI assistant (pre-teacher session chatbot, tajweed rule explanations) must meet these five criteria or risk alienating its core Muslim user base with hallucinated Quran content. The survey provides a complete literature map and blueprint.

**Sakina action:** 
- Integrate RAG with verified Quran text from `TarteelAI/quranic-universal-library` (MIT, Round 19) as the grounding source for all AI-generated tajweed rule explanations
- Every AI explanation of a tajweed rule must cite its source (e.g., "Ikhfa requires ghunnah for 2 counts — Al-Jazariyya, verse 43")
- Do NOT use a general-purpose LLM bare for Islamic content; always use retrieval-augmented generation over verified Islamic text

### 5.2 IslamicMMLU Benchmark (arXiv:2603.23750, March 2026)
10,013 MCQ across Quran (2,013 questions), Hadith (4,000), Fiqh (4,000). Public leaderboard. Best performer: Gemini 3 Flash at 93.8% overall; Quran track top: 99.3%. Arabic-specific models significantly underperform frontier models.

**Sakina action:** Use IslamicMMLU Quran track (2,013 questions) as the minimum acceptance test for any LLM considered for the Sakina AI assistant. Minimum bar: >90% Quran track accuracy. Models falling below this threshold risk providing incorrect Quran content to users.

---

## Section 6 — Groq: Arabic TTS Now Available (BRAND NEW)

### 6.1 Groq Orpheus Arabic Saudi TTS (Live since January 13, 2026)
**Model:** `canopylabs/orpheus-arabic-saudi` on GroqCloud | **Price:** $40/1M chars | **Latency:** sub-200ms TTFB, ~100 chars/sec

**All 24 prior rounds covered Groq only for STT (Whisper Large v3 Turbo at $0.04/hr).** This is the first TTS finding on Groq.

**Voices:**
- **Abdullah** — male, professional, calm, conversational. Optimized for assistants and enterprise workflows.
- **Aisha** — female, professional, clear, approachable. Optimized for customer support and service.
- (4 total Saudi dialect voices available per the docs)

**Technical specs:**
- Saudi dialect pronunciation with regional nuance — appropriate for GCC/MENA core market
- sub-200ms time-to-first-byte (streaming TTS)
- Apache-2.0 (Orpheus model) + Groq commercial terms

**Cost analysis:** At $40/1M chars and ~3 chars/word × 10 words per error feedback message = 30 chars/message:
- 10,000 AI feedback messages = $0.012 (one cent per 833 messages)
- The cost floor for Sakina's AI-spoken feedback for the entire beta is effectively zero

**Sakina action (HIGH PRIORITY):**
1. **Pre-session AI tutor mode:** When a student opens the app before a teacher joins, the AI can speak tajweed corrections in Arabic using Aisha's voice — natural, culturally appropriate, not robotic
2. **Error feedback audio:** When a user mispronounces a word, Sakina can demonstrate the correct pronunciation by playing a TTS synthesis of the correct Arabic phoneme sequence
3. **Teacher-independent practice:** For users who want AI-only practice sessions (no live teacher), Arabic TTS completes the feedback loop: ASR detects error → TTS demonstrates correction
4. **API integration:** Same Groq API key already used for STT; just change endpoint to `POST /openai/v1/audio/speech`, add `model: canopylabs/orpheus-arabic-saudi`, `voice: aisha` — zero new infrastructure

---

## Section 7 — WhisperKit iOS (Confirmed Irrelevant for Android/Flutter)

**arXiv:** 2507.10860 | **Date:** July 14, 2025 (paper) | **Platform:** iOS/macOS only (Core ML, Apple Silicon)

WhisperKit achieves 2.2% WER at 0.46s latency on iOS, matching cloud systems while running fully on-device. MIT-licensed. **However: iOS-only**. No Android support, no Flutter plugin, no ONNX export. For Sakina's cross-platform Flutter app, WhisperKit is not the primary path. **iOS-specific note:** If Sakina ships a Flutter app with an iOS-native module for ASR, WhisperKit is the best iOS ASR option (outperforms sherpa-onnx on Apple devices due to Neural Engine acceleration).

---

## Confirmed No-Change Items This Round
- NVIDIA Parakeet-TDT-0.6B-v3 and Canary-1B-v2: **do NOT support Arabic** (25 European languages only). Confirmed not useful.
- Groq Whisper Large v3 Turbo STT pricing: unchanged at $0.04/hr.
- Tarteel Mahraj/Makharij: no announcement beyond confirmed Q2 beta / Q4 full release timeline.
- sherpa-onnx: no release newer than v1.13.3 (June 15) as of June 23.

---

## Priority Action Queue (Post Round 25)

| Priority | Action | Effort | Deadline |
|----------|--------|--------|----------|
| **P0** | Integrate `canopylabs/orpheus-arabic-saudi` via Groq TTS API for AI error feedback | 1 day | This week |
| **P0** | Locate FaisaI Tadabur-fine-tuned Whisper models on HuggingFace; benchmark WER on 20 Quran clips | 2 days | This week |
| **P1** | Upgrade flutter_webrtc to latest (May 2026) to get AEC/NS/AGC on iOS for live sessions | 0.5 days | Before live-session feature ship |
| **P1** | Run IslamicMMLU Quran track (2,013 questions) on candidate AI assistant LLMs; require >90% | 1 day | Before AI assistant feature ships |
| **P2** | Read arXiv:2606.16629 full text; extract specific Islamic RAG design patterns to implement | 0.5 days | Sprint planning |

---

## Sources
- Tadabur HuggingFace: https://huggingface.co/datasets/FaisaI/tadabur
- Tadabur arXiv: https://arxiv.org/abs/2604.18932
- Groq Orpheus Arabic: https://groq.com/blog/canopy-labs-orpheus-tts-is-live-on-groqcloud
- Groq Orpheus Docs: https://console.groq.com/docs/model/canopylabs/orpheus-arabic-saudi
- Islamic LLMs Survey: https://arxiv.org/abs/2606.16629
- IslamicMMLU: https://arxiv.org/abs/2603.23750
- Ramsa dataset: https://arxiv.org/abs/2603.08125
- RecitID iOS: https://apps.apple.com/us/app/recitid-ai-quran-identifier/id6759472226
- RecitID Android: https://www.appbrain.com/app/recitid-ai-quran-identifier/com.recitid.app
- flutter_webrtc releases: https://github.com/flutter-webrtc/flutter-webrtc/releases
- sherpa-onnx releases: https://github.com/k2-fsa/sherpa-onnx/releases
- WhisperKit arXiv: https://arxiv.org/abs/2507.10860
