# Research Report — 2026-06-22 Daytime (16:06 UTC) — Round 16
## Focus: Harf-Speech Full Benchmark + IqraEval Ecosystem GitHub + New 2026 Quran ASR Models

---

## MAJOR FINDING 1 — Harf-Speech (arXiv:2604.06191, Mar 11, 2026): Complete Arabic Phoneme Benchmark Leaderboard — Gemini 3 RTF=10.75 ELIMINATES Generative Models for Real-Time Tajweed

**Paper:** "Harf-Speech: A Clinically Aligned Framework for Arabic Phoneme-Level Speech Assessment"  
**arXiv:** https://arxiv.org/abs/2604.06191  
**Published:** March 11, 2026

Previous rounds (Round 12) referenced the OmniASR 8.92% PER number from this paper but not the **full leaderboard**. This round surfaces the complete benchmark table:

| Model | PER | RTF | Notes |
|---|---|---|---|
| **OmniASR-CTC-1B-v2** | **8.92%** | ~1x | Fine-tuned, best overall |
| Wav2Vec2 | 13.58% | Low | Zero-shot |
| **Gemini-3-pro-preview** | **15.07%** | **10.75** | Zero-shot — **UNUSABLE real-time** |

**RTF=10.75 for Gemini-3-pro means it takes 10.75 seconds to process 1 second of audio.** Gemini 3 is completely ruled out for any real-time tajweed feedback application. This confirms what Round 12 suspected but did not prove.

**Architecture of Harf-Speech:**
- MSA phonetizer → forced alignment → Levenshtein alignment → blended scorer (LCS + edit-distance)
- Clinical validation: 3 certified Arabic SLPs scored 40 utterances; Harf-Speech achieves **Pearson=0.791, ICC(2,1)=0.659** with mean expert scores
- This is the first Arabic phoneme scorer with clinical SLP validation — directly comparable to how human Quran teachers assess recitation

**Sakīna action:** Harf-Speech confirms the Sakīna tajweed scoring architecture: OmniASR-CTC-1B-v2 (already pre-built ONNX in sherpa-onnx at `csukuangfj/sherpa-onnx-omnilingual-asr-1600-languages-300M-ctc-2025-11-12`) → phoneme diff → tajweed rule classification. The 1B model variant has better PER but needs server-side deployment; the 300M CTC variant runs on-device. **Do NOT use Gemini 3 for any live feedback path.**

---

## MAJOR FINDING 2 — GitHub Iqra-Eval/interspeech_IqraEval: Official Pipeline Code for the 68-Phoneme Quranic Benchmark

**GitHub:** https://github.com/Iqra-Eval/interspeech_IqraEval  
**Paper:** arXiv:2506.07722 "Towards a Unified Benchmark for Arabic Pronunciation Assessment: Quranic Recitation as Case Study" (June 12, 2025, presented at Interspeech 2025 + ArabicNLP 2025)  
**HuggingFace org:** https://huggingface.co/IqraEval

This is the **official open-source implementation** of the Quranic phoneme evaluation pipeline. Previous rounds only cited the AraS2P and whu-iasp papers; this is the repository containing the actual code to run the benchmark. Contents include:

- **68-phoneme inventory** for MSA/Quranic Arabic (standardized; also published as HuggingFace ArabicPhoneme Space)
- **QuranMB.v1** evaluation benchmark (IQRA 2026 upgraded this to QuranMB.v2)
- Data processing scripts: audio alignment, phoneme label generation, forced alignment tooling
- Training pipeline for phoneme-level mispronunciation detection models

**Hafs2Vec** (ACL Anthology: 2025.arabicnlp-sharedtasks.62, ResearchGate publication) is the most detailed system paper from the IqraEval 2025 shared task using this codebase:
- Training data: **EveryAyah/QUL (94h, 28 reciters, 54k clips)** + IqraEval train set **(79h, 74k clips)**
- Custom **Quranic phonemizer producing Tajweed-aware phoneme sequences** aligned to the 68-phoneme set
- Ranked 2nd in IqraEval 2025 shared task (Team BAIC was 1st; Hafs2Vec strong in 5/9 metrics)

**Other IqraEval 2025 system papers** (ACL Anthology, ArabicNLP 2025):
- `2025.arabicnlp-sharedtasks.64.pdf` — "ANPLers: Adapting Whisper-large for Quranic phoneme assessment"
- `2025.arabicnlp-sharedtasks.65` — AraS2P (#1 winner, already documented in Round 13)
- `2025.arabicnlp-sharedtasks.61` — IqraEval organizer overview paper

**Sakīna action:** Clone `Iqra-Eval/interspeech_IqraEval`. The data processing scripts are the exact tools needed to:
1. Convert any Quran audio + text → 68-phoneme label sequences  
2. Align expected vs. produced phonemes  
3. Classify tajweed rule violations  
This eliminates 2–3 months of pipeline development. The 68-phoneme set (IqraEval) is now the industry standard; Sakīna should adopt it to be comparable to IQRA 2026 benchmark scores.

---

## SUPPORTING FINDING 3 — Tadabur Formal Paper (arXiv:2604.18932, April 2026): Harder Benchmark — Whisper-Quran 8.7% WER on Diverse Reciters

**Paper:** "Tadabur: A Large-Scale Quran Audio Dataset" (Faisal Alherran, arXiv:2604.18932)  
**Dataset HF:** https://huggingface.co/datasets/FaisaI/tadabur  
**Released model:** `FaisaI/tadabur-Whisper-Small`

This round confirms the formal paper. Key benchmark numbers from the Tadabur test set (600+ reciters, much harder than typical same-reciter evaluations):

| Model | WER | CER | Notes |
|---|---|---|---|
| Whisper-Quran (tarteel-ai, 74M) | **8.7%** | **6.5%** | Substantially best among domain-adapted models |
| Tadabur fine-tuned | — | — | 96.63% coverage (SILMA embedding) vs 95.50% for Whisper-Quran |
| General-purpose models | Higher | Higher | Significantly underperform |

**Critical reinterpretation:** The commonly cited 5.75% WER for `tarteel-ai/whisper-base-ar-quran` was measured on a held-out set from the training reciters. The Tadabur benchmark uses 600+ independent reciters — a much harder real-world scenario where the same model reaches 8.7% WER. This is the more honest number for production deployment.

**Implication for Sakīna:** When evaluating models for the final production pipeline, use a **held-out reciter benchmark** (reciters not seen during training) rather than same-reciter test sets. Tadabur provides exactly this. The gap between 5.75% and 8.7% WER on unseen reciters is the real measurement that matters for an app serving diverse users.

---

## SUPPORTING FINDING 4 — IbrahimSalah/Wav2vecLarge_quran_syllables_recognition: First Public Syllable-Level Quran Model, Azure AI Foundry Listed

**HuggingFace:** https://huggingface.co/IbrahimSalah/Wav2vecLarge_quran_syllables_recognition  
**Related:** `IbrahimSalah/Arabic_speech_Syllables_recognition_Using_Wav2vec2` (MSA syllables, on Azure AI Foundry: https://ai.azure.com/catalog/models/ibrahimsalah-arabic-speech-syllables-recognition-using-wav2vec2)  
**Architecture:** Wav2Vec2-large-XLSR fine-tuned + 5-gram language model

This model outputs **syllable-level Arabic transcription**, which is architecturally between word-level and phoneme-level:

**Example:** `مِنَ الْجِنَّةِ وَالنَّاسِ` → `مِ نَلْ جِنْ نَ تِ وَنْ نَاْسْ`

Training: Tarteel dataset + private data, converted to syllable labels. Presence on Azure AI Foundry (Microsoft's enterprise catalog) suggests production-grade quality.

**Why syllables matter for Tajweed:**
- **Madd detection**: Elongated syllables appear as explicit syllable tokens (vowel-heavy) — detect madd duration errors by counting syllable length
- **Shadda**: Gemination shows as repeated consonant pattern in syllable sequence
- **Qalqalah**: Stop consonants at syllable-final position are identifiable
- **Syllable-level is faster than phoneme-level** — no forced alignment step needed; output is direct

**Sakīna action:** Test `IbrahimSalah/Wav2vecLarge_quran_syllables_recognition` via HuggingFace Inference API. Input 3–5 clips from `Buraaq/quran-audio-text-dataset` and compare syllable output against expected syllables from `Hetchy/quranic-phonemizer`. If syllable error rate is < 15%, this model becomes a **faster alternative to the full phoneme pipeline** for madd and shadda detection specifically.

---

## SUPPORTING FINDING 5 — Enhanced Quran ASR via Whisper Large (MDPI Applied Sciences, Aug 29, 2025)

**Paper:** "Enhanced Neural Speech Recognition of Quranic Recitations via a Large Audio Model"  
**Journal:** Applied Sciences, MDPI 2076-3417/15/17/9521  
**Dataset:** QRFAM (Quran Recitations by Females and Males — diverse age groups, competency levels)

Published August 29, 2025 (accepted August 5, 2025). Key contribution: systematic comparison of **DeepSpeech** (previous SOTA for QRFAM) vs **Whisper Large** on the same Quranic test set, showing significant WER reduction. Specific WER numbers not extracted (WebFetch blocked) but paper states Whisper improves over DeepSpeech across all demographic groups.

**Sakīna relevance:** The QRFAM dataset covers reciters from multiple ages and genders — unlike Tadabur's expert reciters, QRFAM includes learners and non-experts. This is the demographic Sakīna targets (people learning to recite better). If the QRFAM test set or a similar learner-audio set exists publicly, it should supplement the Tadabur benchmark for evaluating Sakīna's ASR under real user conditions.

---

## Summary of Open Questions to Close Before Development

1. **OmniASR-CTC-1B-v2 on Quranic text** — Harf-Speech benchmarked on MSA clinical samples only; Quran WER is unknown. Test via HF Inference API on 50 `Buraaq/quran-audio-text-dataset` clips.

2. **Iqra-Eval/interspeech_IqraEval code quality** — Clone and verify the phoneme pipeline scripts run without modification on a fresh environment. The data processing tools are the critical path for Sakīna's tajweed layer.

3. **`FaisaI/tadabur-Whisper-Small` exact WER** — Paper released model but no published WER number found yet. Benchmark it against `KheemP/whisper-base-quran-lora` (5.98% same-reciter WER, ~8.7% diverse reciters) to see if Tadabur training diversity improves on unseen reciters.

4. **IbrahimSalah syllable model upload date** — Unconfirmed whether 2025 or earlier. Check HuggingFace model page creation date before relying on it.

---

## Sources

- arXiv:2604.06191 Harf-Speech: https://arxiv.org/abs/2604.06191
- arXiv:2506.07722 IqraEval Benchmark: https://arxiv.org/abs/2506.07722
- GitHub IqraEval: https://github.com/Iqra-Eval/interspeech_IqraEval
- Hafs2Vec (ACL): https://aclanthology.org/2025.arabicnlp-sharedtasks.62.pdf
- arXiv:2604.18932 Tadabur Paper: https://arxiv.org/abs/2604.18932
- FaisaI/tadabur HF: https://huggingface.co/datasets/FaisaI/tadabur
- IbrahimSalah syllables HF: https://huggingface.co/IbrahimSalah/Wav2vecLarge_quran_syllables_recognition
- IbrahimSalah MSA syllables Azure: https://ai.azure.com/catalog/models/ibrahimsalah-arabic-speech-syllables-recognition-using-wav2vec2
- MDPI Enhanced Quran ASR: https://www.mdpi.com/2076-3417/15/17/9521
- IQRA 2026 arXiv: https://arxiv.org/abs/2603.29087
- HuggingFace IqraEval org: https://huggingface.co/IqraEval
