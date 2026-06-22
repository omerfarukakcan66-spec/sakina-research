# Sakina Research — 2026-06-22 Daytime (12:15 UTC)

**Topic:** Qwen3-ASR on Quranic Arabic + IQRA 2026 Winning Architecture Deep Dive + Meta OmniASR sherpa-onnx Path
**Round:** 12
**Critical Questions:** (1) Has Qwen3-ASR 1.7B been benchmarked on Quranic Arabic? (2) What exactly did the IQRA 2026 winner do? (3) Any new HuggingFace Arabic ASR models missed in prior rounds?

---

## Finding 1 — IQRA 2026 Champion Architecture: wav2vec2-XLS-R + TCN + CTC Achieves F1=0.7201 (>2× Jump From 2025)

**Paper:** *"IQRA 2026: Interspeech Challenge on Automatic Pronunciation Assessment for MSA"*
**arXiv:** 2603.29087 (versions v1 and v2)
**Challenge venue:** Interspeech 2026, Sydney (Sep 27–Oct 1, 2026)
**19 teams participated** (nearly double the 2025 edition)

### Winning system breakdown — whu-iasp (1st place):
| Component | Detail |
|---|---|
| **Encoder** | `wav2vec2-xls-r-300m` (frozen, cross-lingual SSL) |
| **Alignment head** | Temporal Convolutional Network (TCN) for phoneme-level contextual modeling |
| **Training objective** | CTC (Connectionist Temporal Classification) |
| **F1 score** | **0.7201** |
| **Key innovation** | Two-stage curriculum training + targeted fine-tuning on real mispronounced speech |

### Top 3 systems (all < 0.005 apart in F1):
| Rank | Team | Approach | F1 |
|---|---|---|---|
| 1 | **whu-iasp** | wav2vec2-xls-r-300m + TCN + CTC | **0.7201** |
| 2 | UTokyo | Enhanced CTC + temporal alignment | ~0.718 |
| 3 | RAM | Rigorous label curation + real mispronounced data | ~0.716 |
| 6 | **Kalimat** | **Qwen3-ASR 1.7B generative LALM** | **0.6702** |

### Year-over-year improvement:
- ArabicNLP 2025 best system: F1 ≈ 0.30
- IQRA 2026 winner: F1 = 0.7201
- **>2.4× improvement in one year** — driven by the new `Iqra_Extra_IS26` dataset (first authentic human mispronounced MSA speech)

### New dataset released with IQRA 2026:
- **`Iqra_Extra_IS26`**: First corpus of real human mispronounced MSA speech (not synthetic)
- **`QuranMB.v2`**: Expanded Quranic mispronunciation benchmark (upgraded from QuranMB.v1)
- **Evaluation space**: `IqraEval/IqraEval_Interspeech_26` on HuggingFace

### Sakina implication:
The whu-iasp architecture (wav2vec2-xls-r-300m + TCN + CTC) is the **confirmed state-of-the-art** template for Sakina's pronunciation assessment engine. It outperforms generative approaches (Kalimat/Qwen3-ASR) by 0.05 F1 while being:
- Lighter (300M params vs. 1.7B for Qwen3-ASR)
- Faster (CTC vs. autoregressive generation)
- More interpretable (frame-level phoneme probabilities)

The CTC head directly outputs phoneme probability sequences, which map to tajweed rule violations via the same Quranic Phonemizer pipeline (Hetchy, NeurIPS 2025) already identified in prior rounds.

---

## Finding 2 — Qwen3-ASR 1.7B on Quranic Arabic: No Published WER, But Phoneme-Level Performance Confirmed at F1=0.6702

**Model:** `Qwen/Qwen3-ASR-1.7B` (HuggingFace)
**Released:** January 29, 2026
**GitHub:** `QwenLM/Qwen3-ASR`
**arXiv:** 2601.21337 (Technical Report)

### What we now know (confirmed via IQRA 2026 paper):
- Kalimat team used Qwen3-ASR 1.7B to reframe MSA pronunciation assessment as **direct speech-to-phoneme generation**
- Treats 68 MSA phonemes as discrete atomic special tokens in the model's vocabulary
- Uses parameter-efficient fine-tuning (PEFT, likely LoRA)
- Achieved F1=0.6702 at IQRA 2026 → **6th out of 19 teams**
- First proof that a generative LALM can compete on Arabic phoneme assessment

### What's still missing:
- **No published WER on specifically Quranic Arabic** (Classical Arabic with tashkeel and tajweed phonetics)
- Benchmark tables in the technical report cover 52 languages but Arabic is listed as "-5.0pp improvement" vs. a prior baseline, without absolute WER numbers
- No Quran-specific fine-tune of Qwen3-ASR has been published

### Sakina verdict on Qwen3-ASR:
**Do NOT use as primary on-device ASR for verse identification.** The Kalimat IQRA 2026 result shows it's competitive for phoneme generation, but at 1.7B parameters it's too heavy for Flutter on-device inference, and it underperforms the CTC architecture by 0.05 F1. Use Qwen3-ASR if and only if you want to test a generative speech-to-phoneme approach for tajweed feedback — but prototype first with the lighter wav2vec2 + TCN + CTC stack.

### Qwen3-TTS side note (new):
**`QwenLM/Qwen3-TTS`** (GitHub, Apache-2.0, released Jan 2026) supports 10 languages — **Arabic is NOT currently included**. Community fine-tune exists: `vadimbelsky/qwen3-TTS-KSA` (KSA/Khaleeji Arabic dialect, ~13k utterances). GitHub Discussion #45 is an open request for Arabic support. Do not use Qwen3-TTS for Sakina's Quran recitation playback — it wasn't trained on Quranic text or Classical Arabic.

---

## Finding 3 — Meta OmniASR 300M sherpa-onnx Conversion (Nov 12, 2025): New On-Device Flutter Arabic ASR Path

**Paper:** *"Omnilingual ASR: Open-Source Multilingual Speech Recognition for 1600+ Languages"*
**arXiv:** 2511.09690 (November 2025)
**Released:** November 10, 2025 (MarkTechPost confirmed)
**HuggingFace org:** `facebook/`
**License:** Apache 2.0 (models) + CC-BY-4.0 (corpus)

### Model family:
| Model | Params | Type | HuggingFace |
|---|---|---|---|
| omniASR-CTC-1B | 1B | CTC | `facebook/omniASR-CTC-1B` |
| omniASR-CTC-7B | 7B | CTC | `facebook/omniASR-CTC-7B` |
| omniASR-LLM-1B | 1B | LLM-decoder | `facebook/omniASR-LLM-1B` |
| omniASR-LLM-7B | 7B | LLM-decoder | `facebook/omniASR-LLM-7B` |
| omniASR-W2V-1B | 1B | wav2vec2 SSL | `facebook/omniASR-W2V-1B` |
| omniASR-W2V-300M | 300M | wav2vec2 SSL | `facebook/omniASR-W2V-300M` |

### Critical for Flutter — sherpa-onnx already converted it:
**`csukuangfj/sherpa-onnx-omnilingual-asr-1600-languages-300M-ctc-2025-11-12`** (HuggingFace)

- The sherpa-onnx team exported the 300M CTC model to ONNX on November 12, 2025
- **C++ and Python API for OmniASR added to sherpa-onnx**
- Flutter support via existing `sherpa_onnx` pub.dev package
- 1600+ languages → Arabic (`arb_Arab` or `ara_Arab` language code) is included

### Critical gap:
OmniASR is trained on general spoken Arabic, **not** Quranic Classical Arabic. WER on Quranic recitation is unknown. The model follows `{language_code}_{script}` format — Quranic Arabic would be `arb_Arab` (Classical Arabic, Arabic script).

### How OmniASR compares in practice:
- `Harf-Speech` framework (arXiv:2604.06191, Mar 2026) used `omniASR-CTC-1B-v2` for Arabic phoneme assessment, achieving **8.92% phoneme error rate** (PER) on MSA pronunciation data
- This is PER not WER — but suggests the model achieves competitive phoneme-level accuracy on Arabic
- Outperforms other end-to-end assessment frameworks on Pearson correlation (0.791) and ICC(2,1) (0.659) with expert human raters

### Sakina action:
Test `csukuangfj/sherpa-onnx-omnilingual-asr-1600-languages-300M-ctc-2025-11-12` in the sherpa-onnx Flutter example by specifying `language=arb_Arab`. Benchmark WER on 50 Quranic ayahs vs. `KheemP/whisper-base-quran-lora` (5.98% WER). If WER < 10% on Quran, OmniASR 300M becomes a strong alternative to Whisper on-device path because:
- No ONNX export step needed (pre-exported)
- 300M is lighter than whisper-base (74M but decoder) on the CTC inference side
- 1600+ language support means it covers code-switching recitation styles

---

## Finding 4 — Harf-Speech (arXiv:2604.06191, Mar 2026): Complete Phoneme-Level Arabic Assessment Pipeline

**Paper:** *"Harf-Speech: A Clinically Aligned Framework for Arabic Phoneme-Level Speech Assessment"*
**arXiv:** 2604.06191 (submitted March 11, 2026)
**Best model:** `omniASR-CTC-1B-v2`

### Architecture (4 stages):
1. **MSA phonetizer** — converts reference text to expected phoneme sequence
2. **Speech-to-phoneme model** (`omniASR-CTC-1B-v2`) — outputs recognized phoneme sequence
3. **Levenshtein alignment** — aligns recognized vs. expected phonemes at character level
4. **Blended scorer** — combines Longest Common Subsequence (LCS) + edit-distance → clinical pronunciation score

### Results:
- **8.92% phoneme error rate** on Arabic
- **Pearson correlation 0.791** with mean expert scores
- **ICC(2,1) = 0.659** — comparable to inter-rater expert agreement
- Outperforms existing end-to-end Arabic assessment frameworks

### Why this matters for Sakina:
Harf-Speech is essentially the **"phoneme diff engine"** that Sakina needs. Swap the MSA phonetizer for the Quranic Phonemizer (Hetchy, NeurIPS 2025, 71 phonemes vs. 68 MSA) and you have a Quran-specific pronunciation scorer. The Levenshtein alignment step tells you exactly which phoneme was substituted/deleted/inserted — map that to a tajweed rule and you have human-readable feedback.

**Pipeline adaptation for Sakina:**
1. Expected: `Hetchy/quranic-phonemizer` → tajweed-aware phoneme sequence
2. Recognized: `omniASR-CTC-1B-v2` → recognized phoneme sequence  
3. Levenshtein diff → phoneme-level errors
4. Rule lookup → "You missed Ikhfaa at word 3 (position N)" 

This is cleaner than TajweedAI's binary classifier approach because it doesn't require separate labeled training data per rule — just the alignment + rule lookup table.

---

## Finding 5 — Tadabur Paper (arXiv:2604.18932, Apr 2026): Now Formally Published + Whisper Fine-Tunes Released

**Paper:** *"Tadabur: A Large-Scale Quran Audio Dataset"*
**arXiv:** 2604.18932 (April 2026)
**HuggingFace dataset:** `FaisaI/tadabur`
**GitHub:** `fherran/tadabur` (162+ ⭐)
**License:** CC BY-NC 4.0

### New details confirmed from the paper:
- **1,400+ hours** from **600+ distinct reciters** (largest public Quran corpus)
- Covers all 113 surahs in murattal and mujawwad styles
- **Automated pipeline**: LLM-based metadata extraction + Whisper/WhisperX-based alignment + ASR-driven content filtering
- Each file: word-level temporal timestamps in structured JSON

### Whisper fine-tuned models (now confirmed released):
- **`FaisaI/tadabur-whisper-small`** — Whisper-Small fine-tuned on Tadabur (lightweight, embedded systems)
- **`FaisaI/tadabur-whisper-large-v3`** — listed as "Coming Soon" as of research date
- **No WER published yet** — the arXiv paper does not include a formal evaluation table; waiting for v2

### OdyAsh model (newly discovered):
- **`OdyAsh/faster-whisper-base-ar-quran`** — faster-whisper CTranslate2 conversion of Tarteel's model, optimized for faster CPU inference. Not previously noted in research. Check inference speed vs. ONNX path.

---

## Key Architecture Decision for Sakina (Updated This Round)

The two clearest Sakina pipelines now are:

### Pipeline A: On-device verse identification (Flutter)
```
record v7.1.0 → PCM16 stream
  → sherpa-onnx OmniASR 300M CTC (language=arb_Arab)  [NEW option to test]
  OR KheemP/whisper-base-quran-lora ONNX via sherpa-onnx  [prior best]
  → verse matching (RapidFuzz on 6,236 verses)
  → surah + ayah + position displayed
```

### Pipeline B: Phoneme-level tajweed feedback (server-side)
```
Audio segment (word-level, 0.5–3s)
  → omniASR-CTC-1B-v2 → recognized phoneme sequence
  → Levenshtein align vs. Hetchy/quranic-phonemizer output
  → tajweed rule lookup table
  → "Ikhfaa missed at word N" / "Madd duration short at word M"
```

Pipeline B is the **Harf-Speech architecture** adapted for Quranic phonemics. This replaces the TajweedAI binary classifier approach with a simpler, more generalizable alignment-based approach.

---

## New Models/Resources Discovered This Round (Not in Prior Summaries)

| Resource | Type | Date | Link |
|---|---|---|---|
| `csukuangfj/sherpa-onnx-omnilingual-asr-1600-languages-300M-ctc-2025-11-12` | ONNX model | Nov 12, 2025 | HF |
| `facebook/omniASR-CTC-1B` | Meta ASR model | Nov 2025 | HF |
| `facebook/omniASR-LLM-7B` | Meta ASR model | Nov 2025 | HF |
| `OdyAsh/faster-whisper-base-ar-quran` | faster-whisper | 2025/2026 | HF |
| `FaisaI/tadabur-whisper-small` | Quran Whisper | Apr 2026 | HF |
| `IqraEval/IqraEval_Interspeech_26` | Eval Space | 2026 | HF Space |
| Harf-Speech (arXiv:2604.06191) | Phoneme assessment | Mar 2026 | arXiv |
| Iqra_Extra_IS26 | First real mispronounced MSA corpus | 2026 | IQRA 2026 |

---

## Summary of Critical Answers

**Q: Has Qwen3-ASR been tested on Quranic Arabic?**
**A: No published WER on Quran.** Kalimat used it for phoneme generation at IQRA 2026 and got F1=0.6702 (6th out of 19) for MSA pronunciation assessment — competitive but not SOTA. Too large (1.7B) for on-device Flutter. Not recommended as the primary Sakina ASR path.

**Q: What did the IQRA 2026 winner do?**
**A:** wav2vec2-xls-r-300m + TCN + CTC, F1=0.7201. Two-stage curriculum training on real mispronounced speech. This is the confirmed template for Sakina's tajweed assessment module.

**Q: New HuggingFace Arabic ASR models?**
**A:** Meta's OmniASR 300M (Nov 2025, Apache-2.0) has a pre-built sherpa-onnx ONNX export — the only non-Whisper Arabic ASR model in the sherpa-onnx model zoo. Test immediately for Quranic WER.

---

## Sources
- [arXiv:2603.29087 IQRA 2026 Challenge Paper](https://arxiv.org/abs/2603.29087)
- [Interspeech 2026 Challenges Page](https://interspeech2026.org/en-AU/pages/programme/challenges)
- [Qwen/Qwen3-ASR-1.7B HuggingFace](https://huggingface.co/Qwen/Qwen3-ASR-1.7B)
- [QwenLM/Qwen3-ASR GitHub](https://github.com/QwenLM/Qwen3-ASR)
- [arXiv:2601.21337 Qwen3-ASR Technical Report](https://arxiv.org/abs/2601.21337)
- [arXiv:2511.09690 Omnilingual ASR Meta](https://arxiv.org/pdf/2511.09690)
- [Meta OmniASR Release MarkTechPost (Nov 11, 2025)](https://www.marktechpost.com/2025/11/11/meta-ai-releases-omnilingual-asr-a-suite-of-open-source-multilingual-speech-recognition-models-for-1600-languages/)
- [csukuangfj/sherpa-onnx-omnilingual-asr-1600-languages-300M-ctc-2025-11-12](https://huggingface.co/csukuangfj/sherpa-onnx-omnilingual-asr-1600-languages-300M-ctc-2025-11-12)
- [facebook/omniASR-CTC-1B](https://huggingface.co/facebook/omniASR-CTC-1B)
- [arXiv:2604.06191 Harf-Speech](https://arxiv.org/abs/2604.06191)
- [arXiv:2604.18932 Tadabur Paper](https://arxiv.org/abs/2604.18932)
- [FaisaI/tadabur HuggingFace](https://huggingface.co/datasets/FaisaI/tadabur)
- [OdyAsh/faster-whisper-base-ar-quran](https://huggingface.co/OdyAsh/faster-whisper-base-ar-quran)
- [IqraEval/IqraEval_Interspeech_26 Space](https://huggingface.co/spaces/IqraEval/IqraEval_Interspeech_26)
- [arXiv:2506.07722 Unified Benchmark Arabic Pronunciation](https://arxiv.org/abs/2506.07722)
- [Hafs2Vec ResearchGate](https://www.researchgate.net/publication/397426561_Hafs2Vec_A_System_for_the_IqraEval_Arabic_and_Qur'anic_Phoneme-level_Pronunciation_Assessment)
- [vadimbelsky/qwen3-TTS-KSA](https://huggingface.co/vadimbelsky/qwen3-TTS-KSA)
- [QwenLM/Qwen3-TTS GitHub](https://github.com/QwenLM/Qwen3-TTS)
- [Qwen3-TTS Arabic Discussion #45](https://github.com/QwenLM/Qwen3-TTS/discussions/45)
