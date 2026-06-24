# Sakina Research — Round 33
**Date:** 2026-06-24 07:16 UTC  
**Focus:** Newest Arabic ASR model on HuggingFace (2025–2026) — with monitor checks on `uzair0/quran-asr` training status and Tarteel v5.79+

---

## FINDING 1 — `nvidia/stt_ar_fastconformer_hybrid_large_pcd_v1.0` (July 2025, CC-BY-4.0): FIRST Open Classical Arabic ASR With Diacritized Output — Trained on EveryAyah, SOTA — Absent From All 32 Prior Rounds

**Source:** arXiv:2507.13977 "Open Automatic Speech Recognition Models for Classical and Modern Standard Arabic" (July 18, 2025, accepted IEEE ICASSP-region). HuggingFace: `nvidia/stt_ar_fastconformer_hybrid_large_pcd_v1.0`.

**Architecture:**
- FastConformer Hybrid Large, ~115M parameters
- Dual-loss: Transducer (primary) + CTC (secondary)
- "pcd" suffix = **Punctuation + Character + Diacritics** → outputs fully diacritized Arabic text (full tashkeel, including sukun, shadda, tanwin)
- Companion without diacritics: `nvidia/stt_ar_fastconformer_hybrid_large_pc_v1.0`

**Training data:**
- FLEURS Arabic
- **Tarteel EveryAyah** (Quran-specific, ~94h professional reciters)
- Arabic Common Voice

**What the paper claims:**
- First unified public model for both **MSA and Classical Arabic**
- **SOTA accuracy with diacritics for Classical Arabic** (the `pcd` variant)
- MSA-only model (`pc`) sets new SOTA on Arabic MSA benchmarks
- Introduced a universal methodology for Arabic speech+text processing
- Both models open-sourced with training recipes via NVIDIA NeMo framework

**License:** **CC-BY-4.0** — commercial use permitted with attribution. More permissive than `FaisaI/tadabur-Whisper-Small` (CC BY-NC 4.0) which blocks commercial use.

**WER numbers:** Exact numbers not retrievable from behind paywalls this run. The IQRA 2026 challenge paper (arXiv:2603.29087) cites this model as a baseline — benchmark numbers appear there. The discussion thread `nvidia/stt_ar_fastconformer_hybrid_large_pcd_v1.0/discussions/2` ("Arabic ASR comparison with SOTA") is the primary source for WER values.

**Why this is critical for Sakina:**
1. The model was trained on EveryAyah (Quran recitation) — high prior probability it already handles Quranic pronunciation patterns
2. Outputs **full tashkeel** → enables the tajweed diff pipeline immediately: `pcd_output → char-diff vs. expected Quran text → flag wrong harakah → tajweed rule`
3. CC-BY-4.0 = commercial deployable without contacting NVIDIA; just attribute
4. FastConformer architecture is the same family as Tarteel's production stack (NeMo/Riva) — Tarteel themselves rely on this framework's quality foundation
5. 115M params — server-side deployable; smaller than Whisper-large but purpose-built for Arabic
6. Referenced in IQRA 2026 as a known baseline system for Arabic pronunciation assessment

**Sakina action:** Test `nvidia/stt_ar_fastconformer_hybrid_large_pcd_v1.0` immediately on `Buraaq/quran-audio-text-dataset` (77,429 word-level clips). Compare:
- vs. `FaisaI/tadabur-Whisper-Small` (8.7% WER, CC BY-NC = NC-blocked)
- vs. `KheemP/whisper-base-quran-lora` (5.98% WER, Hafs-only)
- vs. `obadx/muaalem-model-v3_2` (0.16% PER, Apache-2.0 — but phoneme-level, not word-level WER)

If `pcd` WER on Quran < 8%, this becomes Sakina's primary **diacritized cloud-ASR** option — it is the only CC-BY-4.0 commercial model that natively outputs tashkeel from speech.

---

## FINDING 2 — `yazinsai/offline-tarteel` (2025–2026, Open): Best Verse-Identification From Audio at 87% Recall via FastConformer — QuranCLAP v2 FAISS Targets 95%

**Source:** `github.com/yazinsai/offline-tarteel` + `RESEARCH-audio-to-verse.md`

**What it is:** Offline Quran verse identification — given 3–5s audio of someone reciting, identify the surah and ayah number. No ASR transcript required.

**Current state:**
- Best model: **NVIDIA FastConformer**, **87% recall**, 115 MB model size, **0.33s latency**
- Gap: still misses 95% recall target — all code in the repo exists to close this gap

**Experimental approaches in the repo:**
1. **QuranCLAP v2** (CLIP-style contrastive model): maps audio → 256-dim speaker-invariant embedding → FAISS nearest-neighbor search against pre-computed index of all 6,236 Quran verses → one forward pass + NN lookup. No ASR intermediate step.
2. **HuBERT/WavLM embedding + FAISS NN**: similar audio-embedding approach with different encoder
3. **Moonshine v2 Arabic + WhisperKit streaming ASR** for word-position highlighting (real-time word tracking layer on top of verse identification)

**Distinction from yayaiu6 tracker:** `yayaiu6/Real-Time-Quran-recitation-tracker-System` (Round 31, MIT) does text-based position tracking via Levenshtein+Needleman-Wunsch. `offline-tarteel` does audio-embedding-based verse identification — complementary, not competing. `offline-tarteel` solves "what verse is this?" without needing ASR output.

**Sakina relevance:** The QuranCLAP v2 approach could enable verse identification even when ASR transcription quality is low (non-native learner accent, incomplete recitation). 87% → would correctly identify verse in ~7 out of 8 attempts. The FAISS index approach has sub-100ms lookup after the embedding pass.

**Sakina action:** Clone `yazinsai/offline-tarteel`; read `RESEARCH-audio-to-verse.md` for the architecture. Consider QuranCLAP v2 as a fallback verse-identification path parallel to the text-based yayaiu6 tracker — use whichever confirms first for the live recitation position lock.

---

## FINDING 3 — `Habib-HF/tarbiyah-ai-whisper-medium-merged` (MIT): Third "Tilawa"-Named Competitor — Whisper-Medium Merged Quran Fine-tune, Separate from Tilawa.ai

**Source:** `huggingface.co/Habib-HF/tarbiyah-ai-whisper-medium-merged` + `tilawa.hhammad.com`

**Model specs:**
- Base: `openai/whisper-medium` (769M params)
- Training approach: **merged two fine-tuned models**:
  - Model A: whisper-medium fine-tuned on broad MSA dataset (general Arabic comprehension)
  - Model B: whisper-medium fine-tuned exclusively on Quranic recitation styles (various Qira'at)
- License: **MIT** (fully open, commercial use unrestricted)
- No WER published on the model card

**Connected app:** "Tilawah AI" at `tilawa.hhammad.com` — built by Habib Hammad (GitHub: Habib-HF). This is **NOT** the same as:
- `tilawa.ai` (iOS `id6760923891`, Round 22: NeMo Conformer, ~20MB on-device)
- `tilawi.ai` (third Tilawa-branded app, Round 22)

This is a **fourth** Tilawa-named entry in the market. The naming collision confirms market crowding; "Tilawa" is a common branding choice for Quran recitation apps in 2025–2026.

**Sakina competitive note:** `tilawa.hhammad.com` appears to be a personal/research project level (no App Store listing found, no download count data). Not yet a tier-1 competitor. But the MIT model is extractable and immediately usable — Whisper-medium merged for Qira'at diversity could complement `FaisaI/tadabur-Whisper-Small` (which explicitly trains for multiple styles).

**Sakina action:** Monitor `Habib-HF/tarbiyah-ai-whisper-medium-merged` — if WER on Quran clips < 8%, consider adding to the model benchmark matrix alongside tadabur-Whisper-Small. MIT license makes it the most permissive Qira'at-aware model found to date.

---

## MONITOR ITEMS — No Change

### `uzair0/quran-asr` — Still Training
Last model save was ~7 hours before this round. Training log shows "step 2000 from 3 days ago" — model is still in active training. Commit frequency: still daily (not yet dropped to weekly). **Monitor trigger NOT met.** Do not benchmark yet. Next check: when commit frequency drops to 2–3 per week.

### Tarteel App — Still at v5.78.2 (June 19)
No new Android or iOS release found for June 24, 2026. The hotfix sprint that produced 4 releases in 12 days (June 7–19) has fully paused — now 5 days without update. This is consistent with a team that finished a sprint cycle and is staging the next batch. No Makharij/Tajweed announcement found. **Q3 Sakina ship window remains open.**

---

## Summary Table — Arabic/Quran ASR Models (Confirmed 2025–2026)

| Model | WER (Quran) | Diacritics | License | Size | Notes |
|---|---|---|---|---|---|
| `obadx/muaalem-model-v3_2` | 0.16% PER | Yes (QPS) | Apache-2.0 | 660M | ICML 2026; phoneme-level |
| `KheemP/whisper-base-quran-lora` | 5.98% | No | Unknown | ~150MB | Hafs murattal only |
| `FaisaI/tadabur-Whisper-Small` | 8.7% WER | No | CC BY-NC 4.0 | ~242MB | 600+ reciters; NC blocks commercial |
| `nvidia/stt_ar_fastconformer_hybrid_large_pcd_v1.0` | **TBD — benchmark now** | **Yes (tashkeel)** | **CC-BY-4.0** | 115M | EveryAyah trained; MSA+Classical Arabic; SOTA w/ diacritics |
| `Habib-HF/tarbiyah-ai-whisper-medium-merged` | TBD | No | **MIT** | ~769M | Multi-Qira'at fine-tune |
| `uzair0/quran-asr` | TBD (training) | Yes (tashkeel) | Apache-2.0 | 6.31GB | Still training |
| Nemotron-3.5 (INT4 ONNX) | ~8.2% WER (English) | No | OpenMDW | 0.67GB | Arabic Quran WER unknown |

---

## Key Reference Links
- arXiv:2507.13977: https://arxiv.org/abs/2507.13977
- HuggingFace model: https://huggingface.co/nvidia/stt_ar_fastconformer_hybrid_large_pcd_v1.0
- SOTA discussion: https://huggingface.co/nvidia/stt_ar_fastconformer_hybrid_large_pcd_v1.0/discussions/2
- Tarbiyah model: https://huggingface.co/Habib-HF/tarbiyah-ai-whisper-medium-merged
- offline-tarteel: https://github.com/yazinsai/offline-tarteel
- offline-tarteel research doc: https://github.com/yazinsai/offline-tarteel/blob/main/RESEARCH-audio-to-verse.md
