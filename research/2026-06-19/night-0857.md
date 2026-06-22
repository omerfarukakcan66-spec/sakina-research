# Sakina Research — 2026-06-19 Night Run (08:57 UTC)

## 1. Arabic Quran ASR — New Models & Papers

### Moonshine v2 (Released Feb 23, 2026) ⭐ TOP PRIORITY
- **Paper**: [arxiv.org/abs/2602.12241](https://arxiv.org/abs/2602.12241)
- **Architecture**: Ergodic streaming encoder — reuses cached encoding as audio accumulates; bounded TTFT independent of utterance length
- **Arabic model**: Separate dedicated Arabic model (not multilingual compromise), explicitly designed for edge devices
- **Size**: As small as 27M parameters, beats WhisperLargeV3 on edge benchmarks
- **Key advantage over Tarteel's approach**: Streaming-first design means first token arrives mid-utterance, not after silence — critical for live Quran tracking
- **Open weights**: Yes, Apache 2.0

### MaddoggProduction/whisper-l-v3-turbo-quran-lora-dataset-mix (HuggingFace)
- Whisper Large v3 Turbo + LoRA, fine-tuned on Quran corpus with diacritics (tashkeel)
- Combines accuracy of Large v3 with Turbo's inference speed
- Targets diverse recitation styles and full tajweed compliance

### Habib-HF/tarbiyah-ai-whisper-medium-merged (HuggingFace)
- Strategic merge of two fine-tuned Whisper-medium models
- Specifically tuned for Quranic Arabic transcription

### KheemP/whisper-base-quran-lora (HuggingFace)
- LoRA fine-tune of tarteel-ai/whisper-base-ar-quran
- Published WER: 5.98% — strong baseline for a base-sized model

### WavLink (January 2026) ⭐ CRITICAL FOR VERSE ID
- Augments Whisper encoder with a single learnable "global token"
- Matryoshka-style training: embeddings as small as sub-100 dimensions retain competitive retrieval
- **Killer stat**: 62,360 verse embeddings (6,236 × 10 reciters) = only ~6–12 MB on-device at quantized sub-100-dim
- This means instant verse fingerprinting — no ASR decoding needed for location detection

### SADA Corpus + Transformer Models (arxiv 2508.12968, August 2025)
- Large-scale Arabic speech corpus benchmark
- Transformer-based models outperforming prior baselines on MSA

### Arab Voices Survey (arxiv 2601.13319, January 2026)
- Maps standard + dialectal Arabic speech technology landscape
- Useful for understanding coverage gaps in current ASR training data

---

## 2. IQRA 2026 — Interspeech Challenge (Sydney, Sep 27–Oct 1, 2026)

**Official**: [huggingface.co/spaces/IqraEval/IqraEval_Interspeech_26](https://huggingface.co/spaces/IqraEval/IqraEval_Interspeech_26)
**GitHub**: [github.com/Iqra-Eval/interspeech_IqraEval](https://github.com/Iqra-Eval/interspeech_IqraEval)

- Second edition of IqraEval — Automatic Pronunciation Assessment for MSA/Quran
- **New dataset**: `Iqra_Extra_IS26` — first corpus of *real human* mispronounced MSA speech (previous editions used TTS)
- Corpora: Iqra_train (79h) + Iqra_TTS (52h synthetic) + Iqra_Extra_IS26
- **19 teams** competed (nearly double previous edition)
- Best system: **F1 improvement of +0.2787** over organizer baseline
- Winning techniques: temporal alignment strategies + generative LALMs for Arabic mispronunciation detection diagnosis (MDD)
- **Actionable**: Sakina can use IqraEval benchmark + dataset to evaluate our tajweed error detection module

---

## 3. Tadabur Dataset (arxiv 2604.18932, April 2026)
- Large-scale Quran recitation dataset
- Whisper models fine-tuned on Tadabur, domain-adapted for:
  - Prolonged phoneme durations (madd)
  - Tajweed rules
  - Melodic articulation
  - Wide acoustic diversity across reciters
- Addresses core gap: standard Arabic ASR fails on tajweed-heavy recitation

---

## 4. Tarteel — Current State

### App Updates (2025–2026)
- **Adaptive Mode**: New layout that adapts to user recitation pace; switchable from traditional 15-line Mushaf
- **Home screen widgets**: Streak tracker, daily ayah, instant search
- **Audio playback**: Removed inter-ayah pauses; smoother continuous listening
- **Core claim**: Industry-leading recognition accuracy with on-device AI model
- App Store: [apps.apple.com/us/app/tarteel-ai-quran-memorization/id1391009396](https://apps.apple.com/us/app/tarteel-ai-quran-memorization/id1391009396)

### HuggingFace Models (Tarteel-AI org)
- `tarteel-ai/whisper-base-ar-quran`: WER 5.75% (publicly available)
- `tarteel-ai/whisper-tiny-ar-quran`: Tiny model for ultra-low-resource devices
- `tarteel-ai/whisper-large-v3-Tarteel`: Unpublished WER; this is likely their production model

### GitHub Activity
- `TarteelAI/quranic-universal-library`: Updated **June 7, 2026** — active maintenance
- React Native voice recognition library maintained for iOS/Android (online + offline)

### Competitive Gap Analysis
- Tarteel's on-device model is their moat, but their streaming approach shows latency (pauses between ayahs were a complaint fixed in recent update)
- They do NOT appear to use embedding-based verse fingerprinting (WavLink approach) — this is an opening
- Adaptive Mode is a UX feature, not a technical ASR advance

---

## 5. Flutter Real-Time Audio — Current Best Stack (2026)

### On-Device ASR
| Package | Approach | Notes |
|---------|----------|-------|
| `flutter_whisper.cpp` | Whisper via C++ FFI | On-device, offline capable |
| `speech_to_text` | Platform-native (Apple Speech / Google) | Simple, no Quran fine-tune possible |
| WhisperKit (iOS only) | Whisper Large v3 on Apple Neural Engine | 2.2% WER, 0.46s latency — best iOS option |

### Audio Capture & Streaming
| Package | Capability |
|---------|-----------|
| `flutter_sound` | PCM Float32/Int16 streams from mic — enables custom DSP in Dart |
| `flutter_soloud` | Low-latency C++ SoLoud engine via FFI; reverb, echo, effects |
| MediaRecorder API (web) | Used by yayaiu6's tracker for browser streaming |

### Recommended Sakina Flutter Stack
1. **Audio capture**: `flutter_sound` → raw PCM chunks
2. **Verse ID (Phase 1)**: WavLink embeddings + FAISS on pre-computed Quran verse DB (~6-12 MB)
3. **Live tracking (Phase 2)**: Moonshine v2 (Arabic) via `flutter_whisper.cpp` for streaming ASR
4. **Word alignment**: WhisperX or Whisper internal aligner (2025) for word-level timestamps
5. **Fallback (Phase 3)**: `tarteel-ai/whisper-large-v3-Tarteel` or `MaddoggProduction` LoRA model

---

## 6. Open Source Implementations

### yayaiu6/Real-Time-Quran-recitation-tracker-System (GitHub, 2025)
- Python/Flask/WebSocket backend; Groq Whisper API or NeMo CTC for ASR
- **Benchmarks**: 80–90% skip detection accuracy, <1s latency, ~50ms pipeline overhead, 5–10% false positive rate
- Algorithms: Levenshtein + Needleman-Wunsch alignment
- Hafs variant Quranic JSON with metadata

### yazinsai/offline-tarteel
- Research document mapping full solution space for offline Quran verse detection
- Covers embedding, fingerprinting, streaming ASR approaches with concrete metrics
- Recommended three-phase architecture (see §5 above) directly applicable to Sakina

### Abdelrahman47-code/Quranic-Verse-Recognition (GitHub, 2025)
- Verse recognition from audio using deep learning

---

## 7. Benchmark Summary

| Model | WER | Latency | Size | On-Device |
|-------|-----|---------|------|-----------|
| tarteel-ai/whisper-base-ar-quran | 5.75% | — | Small | Yes (via cpp) |
| KheemP/whisper-base-quran-lora | 5.98% | — | Small | Yes |
| WhisperKit Large v3 (ANE) | 2.2% | 0.46s | Large | Yes (iOS) |
| Moonshine v2 Arabic | TBD | < TTFT-bounded | 27M+ | Yes |
| Distil-Whisper Large v3 | ~3% est | 6× faster | 50% smaller | Yes |

---

## 8. Strategic Recommendations for Sakina

1. **Adopt Moonshine v2 Arabic** as the primary streaming ASR engine — its ergodic encoder is architecturally superior to Whisper for live recitation tracking. Test on Quran audio immediately.

2. **Implement WavLink embedding-based verse fingerprinting** as Phase 1 of the pipeline — instant sub-500ms surah/ayah identification before any decoding. 6-12 MB on-device DB is trivial.

3. **Fine-tune on Tadabur dataset** (arxiv 2604.18932) — this dataset explicitly handles madd, tajweed, and diverse reciters, which is the hardest part of Quran ASR that generic Whisper fails on.

4. **Enter or follow IQRA 2026** (Interspeech, Sydney Sep 27–Oct 1) — benchmark your models against the 19 competing teams, access the Iqra_Extra_IS26 real human mispronunciation dataset for tajweed feedback training.

5. **Tarteel's gap**: Their streaming shows latency artifacts (they patched inter-ayah pauses). Sakina should target sub-200ms word-level highlighting — this is achievable with Moonshine v2 + WhisperKit.
