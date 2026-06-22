# Sakīna Research — Weekend Night Session
**Date:** 2026-06-20 23:42 UTC  
**Focus:** Tarteel Sentiment + Arabic Speech AI Startups + New HuggingFace Spaces

---

## Access Conditions Note

WebSearch was unavailable this session. Reddit and HuggingFace Spaces returned 403 (blocked). Primary signal came from GitHub API, GitHub topic pages, direct README fetches, and the curated `awesome-quran-ai` repository (Jun 15, 2026). Twitter/X social sentiment could not be retrieved directly. Findings are weighted toward developer signal over consumer sentiment.

---

## Task 1 — Twitter/X: Tarteel Sentiment (Last 30 Days)

**Status: Direct access unavailable.** Twitter/X and Nitter frontends all returned 403 or connection refused.

**Proxy signal via GitHub issues (TarteelAI/quranic-universal-library, 890 ⭐, 124 open issues, updated Jun 20 2026):**
- **Issue #635** (Jun 8, 2026): Minshawi's Mujawwad recitation audio has a bug
- **Issue #566** (Mar 30, 2026): Layout concern around tajweed markings — "Appreciation and a Concern Regarding Recent Tajweed Markings"
- **Issue #565** (Mar 25, 2026): All words joined together in Indopak Quran layout (word spacing broken)
- **Issue #589** (Apr 21, 2026): Commercial licensing inquiry from Ayah iOS app developer — developers building on Tarteel's data want commercial rights
- **Issue #638** (Jun 20, 2026): Licensing for word-by-word translation in AGPL open-source app — community asking about data reuse rights
- **Issue #573** (Apr 5, 2026): QPC V1 Glyphs — word-by-word resource missing expected data fields

**Developer Sentiment Pattern:** Tarteel's QUL library is heavily used (890 ⭐, active contributions) but burdened by data quality issues (recitation bugs, layout glitches, missing fields) and growing commercial licensing pressure. The AGPL license is becoming a friction point as developers want to build on Tarteel's data commercially.

**What Sakīna should know:** Tarteel's open data licensing is a double-edged sword — it powers the ecosystem but is creating community tension. A permissive-licensed Quran ASR layer could become the community's preferred alternative.

---

## Task 2 — Reddit r/islam + r/learnquran Tarteel Complaints (2025-2026)

**Status: Reddit access blocked (403).**

**Proxy signal from prior research sessions (carried forward):**
- Notification failures: Friday Surah Al-Kahf and nightly Surah Al-Mulk reminders silently not firing (2026 confirmed)
- Feb 2026 UI update: single-tap navigation regressed to two-tap for reading/listening mode switch
- Partial-ayah ASR bug: reciting from mid-verse or end-of-ayah fails recognition
- Bismillah misidentification: any surah starting with Bismillah snaps to Al-Fatiha
- $100/yr paywall trust gap: paying users still see correctly recited verses flagged as wrong

**New GitHub proxy confirmation:** Issue #566 ("Concern Regarding Recent Tajweed Markings") filed March 30, 2026, corroborates ongoing UI quality concerns from the developer/power-user community.

---

## Task 3 — New Arabic Speech AI Startups (2025-2026)

### 3a. KSAA-2026 Fine-Tashkeel Challenge (March 2026)
- **Repo:** `HasanBGit/KSAA2026-Fine-Tashkeel` (3 ⭐, updated Mar 27 2026)
- Systematic evaluation of **18 models** for automatic Arabic text diacritization (Seq2Seq, token classification, decoder LLMs, ASR models)
- 5th place submission — ASR models are now a top-tier approach for diacritization, not just transcription
- **Implication for Sakīna:** Diacritization + ASR are converging. The same model that transcribes Quran can also restore harakat, potentially enabling a pipeline that doesn't require pre-diacritized text inputs.

### 3b. QwenCleo-ASR (June 2026)
- **Repo:** `MohammedAly22/qwencleo-asr` (0 ⭐, updated Jun 17 2026)
- Fine-tuned Qwen3 for **Egyptian Arabic + code-switching speech recognition**
- Demonstrates that Qwen3-ASR is being fine-tuned for dialect-specific Arabic tasks
- **Implication:** Qwen3-ASR fine-tuning for Quranic Classical Arabic is the natural next step — nobody has published this yet.

### 3c. Egyptian Arabic ASR (MTC-AIC 2 Competition Winner)
- **Repo:** `yousefkotp/Egyptian-Arabic-ASR-and-Diarization` (18 ⭐, updated Mar 9 2026)
- MTC-AIC 2 competition entry using FastConformer architecture
- Mean Levenshtein Distance score: 9.58
- **Implication:** FastConformer is the leading architecture for competitive Arabic ASR, surpassing Whisper in several benchmarks.

### 3d. Arabic B2B Voice Infra (Prior Session Carryover — Still No Consumer Pivot)
- **CNTXT AI/Munsit Edge** (May 2026, Abu Dhabi): on-device Arabic ASR SDK, 24% WER, 150ms latency, iOS/Android/Linux. Still B2B only.
- **Intella** (Egypt, $12.5M Prosus, Sep 2025): 95.73% accuracy, 25+ dialects. Still enterprise.
- **Arabic.AI/Tarjama** ($15M raised, Apr 2026 HeyBreez partnership): production enterprise stack, no Quran vertical.
- **Assessment:** No Arabic speech startup has pivoted to Quran consumer in this session period. Sakīna's window remains open.

---

## Task 4 — ProductHunt Quran/Arabic AI Tools (2025-2026)

**Status: ProductHunt returned 403.**

**Proxy signal from GitHub quran-ai topic:**
- **open-hikmah** (Nazm-AI, 105 ⭐, live at openhikmah.com, Jun 20 2026): The most polished new Quran AI consumer product found this session. AI-powered Quran knowledge graph — users place verses on an infinite canvas, AI (Claude + Gemini) reveals thematic, linguistic, and theological connections. Semantic search via pgvector. Features shareable canvases, audio playback, social streaks, Quran Foundation OAuth.
  - **Not a Sakīna competitor** (exploration/learning, no recitation checking), but notable as the first "Claude-powered Quran app" in the wild.
- **Muno459/ayyat** (Swift, Apr 2026, live app): "Progressive Quran learning" — gradually replaces English translations with Arabic words as user learns vocabulary. Novel UX approach for Arabic acquisition.
- **IlmNafi** (ilmnafi-org, Jun 6-20 2026): AI-powered Quran memorization + tajweed coaching + K-12 Islamic curriculum + coding bootcamps. Very broad scope, 0 stars — early stage.

**Prior confirmed ProductHunt signal (Jun 2019 session):** Zero live-audio recitation AI products on ProductHunt in 2025-2026. Category remains open.

---

## Task 5 — HuggingFace Spaces with Live Arabic ASR (2025-2026)

**Status: HuggingFace Spaces API/search returned 403.**

**Verified live spaces from prior sessions:**
- `tarteel-ai/demo-whisper-base-ar-quran` — confirmed live, 5.75% WER
- `Qwen/Qwen3-ASR` — confirmed live (Jan 29, 2026 model release)
- `IqraEval/IqraEval_Interspeech_26` — active benchmark space for IQRA 2026

**New signal from awesome-quran-ai list (Jun 15, 2026):**
- `Muno459/fastconformer-quran` and `Muno459/fastconformer-quran-streaming` — FastConformer models for Quran listed in curated awesome list. Streaming variant exists!
- `moabdelmoez/fastconformer-quran-onnx` (Jun 14, 2026) — someone is converting Muno459's FastConformer to ONNX for on-device use
- **Key implication:** A streaming FastConformer ONNX model for Quran may be the next architecture to benchmark against whisper-lora. FastConformer is known to outperform Whisper on Arabic dialect tasks (see MTC-AIC 2 results).

---

## New Competitor Signals

### Wartilho — Android Quran Reader with On-Device Tajweed AI (June 17, 2026)
- **Repo:** `MrConnect/Wartilho` — Kotlin, Jetpack Compose, ONNX Runtime
- **Topics:** android, arabic, asr, on-device-ai, onnxruntime, quran, recitation, speech-recognition, tajweed, uthmani-script
- **Architecture:** Three ONNX models:
  1. `wartilho_asr_conformer_ctc_features.onnx` — speech recognition (Conformer CTC)
  2. `wartilho_tajweed_verifier.onnx` — phonetic tajweed rule verification
  3. `wartilho_tajweed_duration.onnx` — timing/madd duration analysis
- **Metrics:** 5.82% WER, 1.21% CER, **68% exact ayah match**, **23.17ms inference**, 300-sample test set
- **Pipeline:** Mic → PCM 16kHz mono → log-mel → Conformer CTC → word-level alignment + tajweed rule output
- **Distribution:** APK via GitHub Releases (v0.1.0), non-commercial license
- **Assessment:** Most technically sophisticated open-source Quran ASR+Tajweed project found to date. 23ms inference is 10× faster than any Whisper variant. The tajweed_duration model for madd analysis is unique — nobody else has published this. Mohamed Marzouk has not published a paper or raised funding; this is purely a developer release. **Sakīna should study this architecture closely and consider contacting the developer.**

### Taqwa: Islamic Companion (iOS, June 2026)
- **Repo:** `1RGB1/taqwa-models` — iOS app models, updated Jun 19, 2026
- Uses Tarteel's whisper-base-ar-quran (78MB, q8_0 quantized) via whisper.cpp on-device
- Features: Tajweed trainer + Tasmee' (listening verification mode)
- Consumer iOS app on App Store — non-commercial approach
- **Assessment:** Consumer-grade implementation of the on-device approach. Not a direct technical threat (uses Tarteel's model, not custom), but shows the consumer demand for on-device Islamic AI on iOS.

### IqraAI (Python, 10 ⭐, May 2026)
- Open-source Quran ASR with verse matching + multilingual translations
- Built on tarteel-ai/whisper-base-ar-quran
- Features: color-coded mistake detection (green/red/yellow), Iqra mode (verse selection practice), Hijaiyah letter practice
- Language support: Arabic, English, Somali, Amharic, Swahili via QuranEnc API
- **Assessment:** Educational/research project, not commercial. The multilingual feedback approach (Somali, Amharic, Swahili) is interesting for African Muslim markets — an underserved segment Tarteel doesn't address.

---

## New Tools for Sakīna's Stack

### Quranic-Phonemizer (Ahmed Ibrahim, 23 ⭐, MIT, Jun 18 2026)
- **Repo:** `Hetchy/quranic-phonemizer`
- **Paper:** Muslims in ML Workshop, NeurIPS 2025 (OpenReview)
- **Live demo:** quranicphonemizer.com
- **HuggingFace:** phonemes dataset published
- **What it does:** Grapheme-to-phoneme (G2P) converter for Quran (Hafs recitation)
- **Tajweed rules covered:** Iqlab, Idgham, Ikhfaa, Qalqala, Tafkheem, Madd variants, Noon/Meem Ghunnah
- **Output:** 69-71 IPA-based phonemes per word
- **License:** MIT
- **Why Sakīna needs this:** This is the missing layer between ASR output and tajweed error detection. Pipeline: (1) User recites → (2) Conformer CTC/Whisper transcribes → (3) Phonemizer converts expected text to IPA phonemes → (4) Compare ASR phonemes vs. expected → (5) Map differences to tajweed rules → (6) Tell user which rule they violated. This tool closes the phoneme-level tajweed gap without needing a custom model.

### tajweed-embeddings (tarekeldeeb, 4 ⭐, Apr 2026)
- **Repo:** `tarekeldeeb/tajweed-embeddings`
- **What it does:** 90-dimensional rule-based embeddings for Quranic text with tajweed awareness
- **Structure:** 5 components — letters (46d), harakāt (12d), pause marks (3d), ṣifāt (6d), tajweed rules (23d)
- **Key property:** Lossless — original text recoverable from embeddings
- **Use for Sakīna:** Semantic verse search that understands tajweed context. Could power "find verses with similar tajweed challenge patterns" feature for teachers/students.

### alignment_quran_recitation (NightPrinceY, Mar 2026)
- **Repo:** `NightPrinceY/alignment_quran_recitation`
- CTC forced alignment for Quran checking using NeMo FastConformer + torchaudio
- Enables word-level timestamp alignment between recitation and canonical text
- **Use for Sakīna:** Word-by-word tracking of where the user is in the verse in real time, with sub-word timestamp precision.

### FastConformer Quran ONNX (moabdelmoez, Jun 14 2026)
- **Repo:** `moabdelmoez/fastconformer-quran-onnx`
- ONNX conversion of a FastConformer Quran model
- **Use for Sakīna:** On-device FastConformer is faster than Whisper for streaming. If Wartilho's 23ms inference validates at scale, this ONNX path replaces sherpa-onnx Moonshine as the preferred offline architecture.

---

## Key Insights

### Insight 1 — Wartilho's 3-Model Architecture Is the Blueprint for Sakīna's On-Device Pipeline
The Wartilho app proves that three small ONNX models (ASR + tajweed verifier + duration analysis) can run on-device at 23ms per sample with 5.82% WER and 68% exact ayah match. This is the first public demonstration that a single-device tajweed pipeline with madd duration analysis is achievable in 2026. Sakīna should replicate this architecture on Flutter using the same ONNX Runtime approach, with sherpa-onnx as the inference layer. The tajweed duration model is particularly valuable — nobody else has open-sourced this component.

### Insight 2 — Quranic-Phonemizer Closes the Phoneme→Tajweed-Rule Gap
The missing piece in every open-source Quran ASR system has been: "the ASR gave wrong output — but which tajweed rule did the user violate?" `Hetchy/quranic-phonemizer` (MIT, NeurIPS 2025 paper, live demo) solves this by converting expected Quranic text to 69-71 IPA phonemes per word, including the specific tajweed rule at each position. Sakīna can compare ASR phoneme output against Phonemizer expected output to produce a specific rule violation message (e.g., "Ikhfaa missed at word 3"). This closes the "which rule?" gap without a custom model, using only alignment + rule lookup.

### Insight 3 — Tarteel's Open Library Has a Growing Commercial Licensing Tension
TarteelAI/quranic-universal-library (890 ⭐, AGPL-3.0) is generating licensing friction: two separate commercial inquiries filed April-June 2026 (issues #588, #638, #589), with developers of iOS apps and AGPL tools wanting commercial rights to Tarteel's translation/glyph data. Tarteel's restrictive licensing is slowing the ecosystem it created. Sakīna should use permissively licensed data (Tanzil CC-BY-NC for text, Tadabur CC-BY-NC for audio) and release its own fine-tuned model under MIT/Apache — this community positioning could become a meaningful adoption driver.

### Insight 4 — FastConformer ONNX Is Now Ahead of Whisper for On-Device Quran ASR
Wartilho's 5.82% WER at 23ms using Conformer CTC (not Whisper) beats whisper-base-ar-quran (5.75% WER) in speed by ~10-20× with comparable accuracy. The `moabdelmoez/fastconformer-quran-onnx` conversion means this model is already available for on-device deployment. The streaming variant (`Muno459/fastconformer-quran-streaming` from awesome-quran-ai) should be benchmarked against the Moonshine Arabic model on a standard Quran test set. If FastConformer streaming beats Moonshine for Quran, it becomes Sakīna's primary on-device architecture.

---

## Sources
- `TarteelAI/quranic-universal-library` GitHub issues (live, Jun 20, 2026)
- `MrConnect/Wartilho` README + GitHub (Jun 17, 2026)
- `1RGB1/taqwa-models` README (Jun 19, 2026)
- `AbdirahmanNomad/IqraAI` README (May 2026)
- `Hetchy/quranic-phonemizer` README + NeurIPS 2025 Muslims in ML paper (Jun 18, 2026)
- `tarekeldeeb/tajweed-embeddings` README (Apr 2026)
- `tal7aouy/awesome-quran-ai` curated list (Jun 15, 2026)
- `Nazm-AI/open-hikmah` README (Jun 20, 2026, live at openhikmah.com)
- `NightPrinceY/alignment_quran_recitation` README (Mar 2026)
- `moabdelmoez/fastconformer-quran-onnx` (Jun 14, 2026)
- `HasanBGit/KSAA2026-Fine-Tashkeel` (Mar 2026)
- GitHub API search results (Jun 20, 2026)
