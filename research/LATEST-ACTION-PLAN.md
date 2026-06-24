# Sakina — Latest Action Plan
**Last updated:** 2026-06-24 07:29 UTC (Round 34, Opus weekly deep-dive)
**Status:** 🟢 STABLE & SHARPENED — every layer of the core pipeline has a confirmed 2025–2026 open/MIT/Apache reference implementation. Round 34 adds the finalized IQRA 2026 results + a clinically-validated scorer blueprint (Harf-Speech), and **quantifies the moat**: mispronunciation *recognition* is solved (0.16% PER) but *judgment* peaks at ~72% MDD F1 with ~30% baseline precision — the exact gap a live teacher fills.

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
5. Build mispronunciation scorer — **clone a clinically-validated, modular blueprint: `Harf-Speech` (arXiv:2604.06191, Mar 2026).** Architecture = MSA G2P (`quranic-phonemizer`) → speech-to-phoneme → Levenshtein alignment → **blended LCS + edit-distance scorer** (Pearson 0.791 / ICC 0.659 vs. expert SLPs — beats end-to-end frameworks). **Base models, in priority order:** `obadx/muaalem-model-v3_2` (Apache, **0.16% PER**, Quran-native, primary) → `facebook/omniASR-CTC-1B` (Apache, 1,600-lang base; Harf-Speech got **8.92% PER** via Arabic-phoneme fine-tune — use for **non-native learner-accent robustness**) → Hafs2Vec recipe (wav2vec2 + EveryAyah/QUL 94h + IqraEval 79h). **IQRA 2026's #1 lesson (19 teams): real mispronunciation data + data-quality control beat architecture** → add synthetic augmentation (BAIC/Mattar, El-Kheir confusion pairs) AND, critically, **harvest teacher-labeled real learner errors from live sessions** (a data moat AI-only apps can't build). Optionally fuse the ASR transcript as a 2nd modality (arXiv 2511.17477, F1 70.53%). **Benchmark on `QuranMB.v2` + `Iqra_Extra_IS26`; eval via Pearson/ICC vs. a Sakina teacher panel, not just F1.** Score via weighted Needleman-Wunsch with `panphon` + El-Kheir substitution costs; normalize text with `camel-tools`.
6. Add Groq Orpheus Arabic TTS (Aisha) for spoken error feedback.

## P2 — Pre-Launch
7. RAG-ground all AI tajweed explanations over QUL (MIT) per arXiv:2606.16629 Islamic-LLM safety requirements; require >90% on IslamicMMLU Quran track for any assistant LLM.
8. Live teacher session via LiveKit free tier (5,000 WebRTC min/mo) + flutter_webrtc AEC/NS/AGC.

---

## Accuracy Snapshot (2026 SOTA) — recognition is solved, judgment is not
| Task | SOTA | Note |
|---|---|---|
| Quran ASR (WER) | ~4% (Tarteel); 5.98% open | near-solved |
| Phoneme recognition (PER) | **0.16%** (`muaalem-v3_2`) | solved |
| **Mispronunciation D&D (F1)** | **~70–72%** winner; **baselines 40–44%, precision 27–31%** | **unreliable — the frontier & the moat** |
| Score vs. expert (Pearson) | **0.791** (Harf-Speech) | best human-agreement |

## Competitive Posture
- **Tarteel** = proprietary NVIDIA NeMo+Riva+Triton (~4% WER), React Native, **no public ASR API** → Sakina cannot piggyback; self-host. Their tracking algorithm has no secret sauce (same as yayaiu6's open approach); moat is data + latency. Mahraj/Makharij still roadmap-only (no firm date). **Still v5.78.2 (June 19), no v5.79+ — ~5-day stall.** Fresh June-2026 user complaints: re-flags correct words as mistakes (= the low-precision MDD problem in production), fails on partial/mid-ayah recitation, iOS record-button crash, ~$100/yr value gripes, no structured Hifdh/SRS pathway.
- **Sakina's moat — now quantified.** Synchronous LIVE teacher sessions (Flutter), validated by 3 peer-reviewed 2026 papers that AI cannot replace Quran teachers, AND by hard accuracy numbers: best-in-class 2026 AI judges mispronunciations at only ~72% F1 / ~30% baseline precision (IQRA 2026, 19 teams). **Lead the pitch with precision:** "Even at $100/yr, the best AI cries wolf 2 of 3 times — a real teacher doesn't." No competitor (Tarteel, QariAI, Tilawa.ai, AL Siraat, Qara'a async) ships synchronous live human sessions. **Bonus moat: every teacher-corrected error in a live session is labeled real-mispronunciation data — the #1 accuracy lever per IQRA 2026 — that AI-only apps structurally cannot collect.**
