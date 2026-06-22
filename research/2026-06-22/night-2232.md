# Night Research — 2026-06-22 22:32 UTC
*Round 20 — Arabic Quran ASR · Flutter Audio · Tarteel Watch · New ML Models*

---

## 1. ICML 2026 PAPER — QPS + Multi-Level CTC Achieves 0.16% PER on Quran Recitation

**Source:** arXiv 2509.00094 — *"Automatic Pronunciation Error Detection and Correction of the Holy Quran's Learners Using Deep Learning"*
**Venue:** ICML 2026 (confirmed in conference proceedings)
**HuggingFace page:** https://huggingface.co/papers/2509.00094

This is the most significant Quran ASR result found to date. Key contributions:

### Dataset Pipeline (98% automated)
- **848 hours** of Quranic audio from expert reciters, yielding **286,000 annotated utterances**
- Waqf (pause-point) segmentation via fine-tuned **wav2vec2-BERT** model
- Transcript verification via novel **Tasmeea algorithm** (not manual)
- Result: first large-scale, diacritically-complete Quranic ASR dataset with tajweed annotation

### Quran Phonetic Script (QPS)
- 11-level encoding system for Arabic Quran phonemes
- **Phoneme level:** Arabic letters with short + long vowels
- **Sifat level:** articulation characteristics (Ghunnah, Qalqalah, Madd, Ikhfa, etc.)
- QPS bridges the gap between raw audio and tajweed rule enforcement — something no prior open model does

### Multi-Level CTC Model
- Achieves **0.16% average Phoneme Error Rate (PER)** on test set — extraordinary
- Multi-level CTC simultaneously trains on phoneme + sifat targets
- Includes `qdat_bench`: benchmark covering phonemes, diacritization, Ghunnah, Qalqalah, Madd — real mispronounced recitation samples (not TTS)

### Sakina Implications
- **The QPS framework is exactly what Sakina needs for tajweed feedback** — it maps audio → phoneme + rule violation simultaneously, without requiring a separate tajweed rule engine post-ASR
- If open-sourced (check ICML 2026 proceedings), this is an immediate drop-in for pronunciation error feedback
- 0.16% PER means the model correctly identifies ~99.84% of phoneme-level mistakes — far beyond what Tarteel currently offers at the word level
- **Action:** Email authors (check paper) for code/weights access; check ICML 2026 supplementary for GitHub link

---

## 2. Quran-MD Dataset — Word-Level Aligned Audio, 32 Reciters, Jan 2026

**Source:** arXiv 2601.17880 — *"Quran-MD: A Fine-Grained Multilingual Multimodal Dataset of the Quran"*
**HuggingFace:** https://huggingface.co/datasets/Buraaq/quran-md-ayahs
**Venue:** Muslims in ML Workshop @ NeurIPS 2025

Key specs:
- **77,429 word-level samples** — each word paired with Arabic script, English translation, transliteration, aligned audio
- **187,080 verse-level samples** across 32 reciters
- Diverse recitation styles (murattal and mujawwad)
- Fully public on HuggingFace

### How it differs from Tadabur
| Dataset | Hours | Reciters | Word-Level? | Alignment |
|---------|-------|----------|-------------|-----------|
| **Tadabur** | 1,400+ | 600+ | No | Verse-level |
| **Quran-MD** | Not stated | 32 | **Yes** | Word-level temporal |

Quran-MD wins on granularity (word-level timestamps for training a live verse tracker). Tadabur wins on diversity. **Sakina should use Quran-MD for word-boundary detection training and Tadabur for general ASR robustness.**

### Sakina Implications
- Word-level temporal alignment is the missing piece for real-time "which word is the user on" during recitation
- Can directly train `obadx/recitation-segmenter-v2`-style models on Quran-MD word timestamps
- **Action:** Download from HuggingFace now; 77K samples is a practical fine-tuning size

---

## 3. Tarteel Version Watch — v5.77.3 (Jun 7) and v5.85.1 Spotted

### v5.77.3 (June 7, 2026) — Confirmed Features
- **Home screen widgets** added: streak tracker, daily ayah reminder, instant ayah search
- Multiple behind-the-scenes crash fixes and improvements
- TarteelAI/quranic-universal-library also updated June 7, 2026

### v5.85.1 — New (unconfirmed date, mod APK site)
- **8 minor version bumps** from 5.77.3 to 5.85.1 in approximately 2 weeks — aggressive iteration pace
- Changelog not yet available on official channels; mod APK site listed it without changelog
- **Pattern:** Tarteel ships 1–2 minor versions per week when in active feature development
- **Hypothesis:** The Mahraj/Makharij detection beta (previously identified as Q2 2026 target) may be landing in these rapid-fire releases

### Key Weakness Still Confirmed
- Paying users ($100/yr) still receiving incorrect recitation flags
- Partial-ayah ASR mid-verse/end-of-verse recognition remains unreliable per App Store reviews
- Paywall trust gap unchanged — complaints visible across App Store and aggregator review sites

### Sakina Implications
- Tarteel's development velocity is high (8 releases in ~2 weeks) — they're in a crunch sprint
- **The window before Mahraj beta ships publicly is narrowing** — previously estimated Q4 2026 full release
- Sakina must ship a live teacher differentiation point before Tarteel's AI-only tajweed detection matures

---

## 4. Moonshine v2 Arabic Model — Confirmed Production-Ready for Streaming

**Source:** arXiv 2602.12241 — *"Moonshine v2: Ergodic Streaming Encoder ASR for Latency-Critical Speech"*

### Confirmed Specs
- Arabic language-specific model available (Base size) alongside Japanese, Korean, Mandarin, Spanish, Ukrainian, Vietnamese
- Ergodic streaming encoder: sliding-window self-attention with bounded latency regardless of utterance length
- **Latency vs Whisper (Apple M3):**
  - Moonshine v2 Tiny: **50ms** (5.8x faster than Whisper Tiny)
  - Moonshine v2 Small: **148ms** (13.1x faster than Whisper Small)
  - Moonshine v2 Medium: **258ms** (43.7x faster than Whisper Large v3)
- No zero-padding: computes only on actual audio, not silence padding
- Caches encoder + partial decoder state between streaming windows

### Gap vs Whisper
- Whisper Large V3 equivalent latency: **11,286ms** (11+ seconds)
- Moonshine processes 3-second phrase in time proportional to 3 seconds, not padded to 30 seconds
- This is architecturally superior for live recitation tracking where the user stops mid-verse

### Sakina Implications
- **Moonshine v2 Arabic is the streaming backbone to build on** — not Whisper
- 50–148ms TTFT means on-device recitation tracking that feels instant
- Combine with QPS phoneme model (ICML 2026 paper) for: Moonshine → phoneme stream → QPS error detection
- **Action:** Pull Arabic weights, benchmark on 30-second Quran clip to confirm WER vs whisper-base-quran-lora (5.98% WER)

---

## 5. Flutter Audio Stack — Sherpa-ONNX Confirmed Viable, SenseVoice GGUF Not Yet Arabic

### Sherpa-ONNX + Flutter
- `sherpa_onnx` pub.dev package confirmed live: https://pub.dev/packages/sherpa_onnx
- Official Flutter streaming ASR example: `flutter-examples/streaming_asr/lib/streaming_asr.dart`
- **Fully on-device, no internet required** — ONNX runtime replaces PyTorch
- No Arabic Quran ONNX model bundled, but any model can be exported to ONNX + loaded
- **Path:** Export whisper-base-ar-quran or Moonshine Arabic to ONNX → load in sherpa_onnx Flutter package

### SenseVoice.cpp / GGUF
- C++ port of SenseVoice available (lovemefan/SenseVoice.cpp)
- q8 quantized model: **~254MB**, same accuracy, no Python runtime
- Built-in FSMN-VAD (voice activity detection) included
- **Critical gap:** SenseVoice currently supports: Mandarin, Cantonese, English, Japanese, Korean — **Arabic NOT confirmed**
- 15x faster than Whisper, non-autoregressive — extremely attractive for edge
- **Watch:** SenseVoice Arabic fine-tune would be a major unlock; check FunAudioLLM repo for Arabic roadmap

### Picovoice Cheetah
- Also confirmed for Flutter real-time streaming STT
- On-device, higher accuracy than Google Streaming ASR per Picovoice claims
- **Not open source** — commercial license, unclear Arabic Quran support

### Recommended Flutter Stack for Sakina
1. **ASR:** sherpa_onnx + Moonshine v2 Arabic ONNX export (or whisper-base-ar-quran ONNX)
2. **VAD:** sherpa_onnx built-in VAD or SileroVAD via ONNX
3. **Phoneme scoring:** QPS CTC model (ICML 2026) once weights are open
4. **Word tracking:** Quran-MD temporal alignments as lookup table

---

## 6. New Open-Source Developments (Last 3 Months)

### FunASR (ModelScope)
- **170x real-time**, 50+ languages, OpenAI-compatible API
- Speaker diarization, emotion detection, streaming built-in
- Arabic in the 50+ languages — exact Quran performance unknown
- Updated actively on GitHub: https://github.com/modelscope/FunASR

### Tadabur-Whisper-Small
- Whisper Small fine-tuned specifically on the Tadabur dataset
- Available on HuggingFace (check fherran/tadabur repo for model card)
- Designed for murattal + mujawwad style diversity — may be better than whisper-base-ar-quran for broad recitation styles

### OdyAsh/faster-whisper-base-ar-quran
- CTranslate2 (faster-whisper) version of tarteel-ai/whisper-base-ar-quran
- Inference speed significantly higher than standard whisper
- Drop-in for CPU-only serving

---

## Priority Action Items

| Priority | Action | Why Now |
|----------|--------|---------|
| **P0** | Pull Moonshine v2 Arabic weights; benchmark vs whisper-base-quran-lora on live verse clip | Architecture is superior for streaming; need WER number |
| **P0** | Download Quran-MD from HuggingFace (77K word-level aligned samples) | Word-boundary training data for real-time verse tracking |
| **P1** | Check ICML 2026 proceedings for QPS model weights (arXiv 2509.00094) | 0.16% PER model may be open — would replace entire tajweed rule engine |
| **P1** | Prototype sherpa_onnx + Moonshine Arabic in Flutter | Confirms end-to-end mobile stack before committing architecture |
| **P2** | Monitor Tarteel changelog for v5.78–v5.85 changes | 8 minor versions = something significant shipping; likely Mahraj detection |
| **P2** | Evaluate Tadabur-Whisper-Small on diverse recitation styles | More reciters = more robust to user accent variation |

---

## Sources
- [arXiv 2509.00094 — Quran Pronunciation Error Detection (ICML 2026)](https://arxiv.org/abs/2509.00094)
- [ICML 2026 Virtual Proceedings — Paper 80144](https://icml.cc/virtual/2026/80144)
- [arXiv 2601.17880 — Quran-MD Dataset](https://arxiv.org/abs/2601.17880)
- [HuggingFace — Buraaq/quran-md-ayahs](https://huggingface.co/datasets/Buraaq/quran-md-ayahs)
- [arXiv 2602.12241 — Moonshine v2](https://arxiv.org/abs/2602.12241)
- [Moonshine vs Whisper 2026 Benchmark](https://modelslab.com/blog/audio-generation/moonshine-vs-whisper-asr-real-time-speech-2026)
- [sherpa_onnx Flutter package](https://pub.dev/packages/sherpa_onnx)
- [sherpa-onnx Flutter streaming ASR example](https://github.com/k2-fsa/sherpa-onnx/tree/master/flutter-examples/streaming_asr)
- [SenseVoice.cpp GitHub](https://github.com/lovemefan/SenseVoice.cpp)
- [Tarteel v5.77.3 changelog — iqra.soft112.com](https://iqra.soft112.com/)
- [Tarteel v5.85.1 — getmodpc.net](https://getmodpc.net/tarteel/)
- [TarteelAI GitHub org](https://github.com/orgs/TarteelAI/repositories)
- [FunASR GitHub](https://github.com/modelscope/FunASR)
- [OdyAsh/faster-whisper-base-ar-quran](https://huggingface.co/OdyAsh/faster-whisper-base-ar-quran)
- [KheemP/whisper-base-quran-lora (5.98% WER)](https://huggingface.co/KheemP/whisper-base-quran-lora)
