# Sakina — Latest Action Plan
**Last updated:** 2026-06-24 06:48 UTC (Round 32, Opus deep-analysis)
**Status:** 🟢 BREAKTHROUGH — every layer of the core pipeline now has a confirmed 2025–2026 open/MIT/Apache reference implementation. The two biggest unknowns (live follow-along + Arabic phonetic matching) are now buildable from MIT-licensed code.

---

## The Buildable Pipeline (all components verified 2025–2026)

| Layer | Choice | Version / Source | License |
|---|---|---|---|
| Mic capture (capture-once-fork) | `record` | 7.1.0 (Jun 11 2026) | BSD-3 |
| Audio session coordination | `audio_session` | current | MIT |
| Live teacher session | `flutter_webrtc` | 1.5.2 (Jun 20 2026) | MIT |
| On-device ASR (streaming) | `sherpa_onnx` (Nemotron-3.5) | 1.13.3 (Jun 16 2026) | Apache-2.0 |
| Fine-tuned Whisper on-device | `whisper_ggml_plus` | 1.5.2 (Apr 1 2026) | MIT |
| Cloud ASR fallback | Groq Whisper Large v3 Turbo | $0.04/hr | — |
| **Live position tracking** | port `yayaiu6/Real-Time-Quran-recitation-tracker-System` | MIT, Nov2025–Apr2026 | MIT |
| **Tajweed-aware G2P + fuzzy ayah-lock** | `quranic-phonemizer` | 2.7 (Jun 18 2026) | MIT |
| Arabic text normalization | `camel-tools` | 1.6.0 (Jun 8 2026) | MIT |
| Phoneme edit distance | `panphon` | 0.22.2 (Jun 12 2025) | MIT |
| Spoken feedback TTS | Groq Orpheus Arabic (Aisha) | $40/1M chars | Apache-2.0 |
| Quran data | `TarteelAI/quranic-universal-library` (QUL) | MIT | MIT |

---

## P0 — This Week
1. **Clone & study `yayaiu6/Real-Time-Quran-recitation-tracker-System`** — read README + `backend/config.py`. It documents the exact live algorithm: normalized-Levenshtein segment ranking → Needleman-Wunsch word alignment → 60-ahead/15-behind sliding window with tracking/search modes. Port the matching loop to Dart.
2. **`pip install quranic-phonemizer` (2.7)** — test `ref_text` fuzzy mode on partial/noisy recitation; confirm it locks to correct ayah + returns position. This is the phonetic-matching + ayah-lock core.
3. **Prototype capture-once-fork in Flutter** — `record` 7.1.0 broadcast stream + `audio_session`, fork to `sherpa_onnx` 1.13.3 (Nemotron-3.5, prompt_index=101 for Arabic) and a stub WebRTC consumer. Verify NO AVAudioSession conflict on a real iOS device (the documented failure mode).

## P1 — This Sprint
4. Benchmark `uzair0/quran-asr` (Apache-2.0, ~6.3GB) and `FaisaI/tadabur` Whisper fine-tunes once training stabilizes; convert the winner via `whisper_ggml_plus` HF→ggml path for on-device.
5. Build mispronunciation scorer — **reproduce a ranked recipe, don't design from scratch.** Reimplement **Hafs2Vec** (IqraEval-2025 #2): wav2vec2/AraS2P fine-tuned on **EveryAyah/QUL (94h, 54k clips) + IqraEval train (79h, 74k clips)** with **`quranic-phonemizer` Tajweed-aware labels** (IqraEval phoneme set). Then add the **#1 lever — synthetic mispronunciation augmentation** (BAIC/Mattar 2025: perturb phonemes per El-Kheir-2025 confusion pairs). Optionally fuse the ASR transcript as a 2nd modality (arXiv 2511.17477, UniSpeech+BERT → PER 3.83%/F1 70.53%) since Sakina already produces it. Score via weighted Needleman-Wunsch with `panphon` + El-Kheir confusion-matrix substitution costs; normalize ASR text with `camel-tools`.
6. Add Groq Orpheus Arabic TTS (Aisha) for spoken error feedback.

## P2 — Pre-Launch
7. RAG-ground all AI tajweed explanations over QUL (MIT) per arXiv:2606.16629 Islamic-LLM safety requirements; require >90% on IslamicMMLU Quran track for any assistant LLM.
8. Live teacher session via LiveKit free tier (5,000 WebRTC min/mo) + flutter_webrtc AEC/NS/AGC.

---

## Competitive Posture
- **Tarteel** = proprietary NVIDIA NeMo+Riva+Triton (~4% WER), React Native, **no public ASR API** → Sakina cannot piggyback; self-host. Their tracking algorithm has no secret sauce (same as yayaiu6's open approach); moat is data + latency. Mahraj/Makharij still roadmap-only (no firm date).
- **Sakina's moat** = synchronous LIVE teacher sessions (Flutter), validated by 3 peer-reviewed 2026 papers that AI cannot replace Quran teachers. No competitor (Tarteel, QariAI, Tilawa.ai, AL Siraat, Qara'a async) ships synchronous live human sessions.
