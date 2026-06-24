# Research Report — 2026-06-24 daytime-1803 UTC (Round 39)

**Topic:** `MaddoggProduction/whisper-l-v3-turbo-quran-lora-dataset-mix` — First Whisper-Large-v3-Turbo Apache 2.0 Fine-Tune for Quran with Curriculum Learning + Monitor Confirmations (Tarteel v5.78.2, uzair0 still training)

---

## Primary Finding — `MaddoggProduction/whisper-l-v3-turbo-quran-lora-dataset-mix` (Apache 2.0): Whisper-Large-v3-Turbo LoRA for Quran, Curriculum 3-Dataset Training, 12.69% WER

**HuggingFace:** `huggingface.co/MaddoggProduction/whisper-l-v3-turbo-quran-lora-dataset-mix`
**Base model:** `openai/whisper-large-v3-turbo` — 4 decoder layers (vs. 32 in Large v3), ~3-4x faster inference
**License:** Apache 2.0 ✓ — commercially deployable without NC restrictions
**WER:** 12.69% on `ahishamm/QURANICWhisperDataset` test set (20% held out)
**Never covered in any prior round (1–38).**

### Architecture & Training

LoRA fine-tune of `openai/whisper-large-v3-turbo` using **3-stage curriculum learning**:

| Stage | Dataset | Role |
|---|---|---|
| 1 | `tarteel-ai/everyayah` | Professional expert reciters — clean, high-quality |
| 2 | `MohamedRashad/Quran-Recitations` | 20 reciters, full-Quran ayah-level diacritized audio |
| 3 | `ahishamm/QURANICWhisperDataset` | Unknown composition — used as final stage + test set |

Data augmentation applied: gain adjustments, spectral masking, white noise injection.

### WER Context

| Model | WER | Test Set | License | Architecture |
|---|---|---|---|---|
| `KheemP/whisper-base-quran-lora` | 5.98% | EveryAyah hafs-murattal | unconfirmed | whisper-base LoRA |
| `FaisaI/tadabur-Whisper-Small` | 8.7% | Tadabur held-out (600+ reciters) | CC BY-NC 4.0 | whisper-small |
| `MaddoggProduction/...` | **12.69%** | `ahishamm/QURANICWhisperDataset` test | **Apache 2.0** | **whisper-large-v3-turbo** LoRA |

**Key nuance:** The 12.69% WER is benchmarked on `ahishamm/QURANICWhisperDataset`, not on EveryAyah or Tadabur. Cross-benchmark WERs are not comparable directly — this model could perform significantly differently on Buraaq or tadabur's test sets. **Action: benchmark on `Buraaq/quran-audio-text-dataset` to get a fair comparison.**

### Strategic Value for Sakina

**1. Inference speed advantage (the core reason to care):**
whisper-large-v3-turbo has only 4 decoder layers vs. 32 in whisper-large-v3. On-device inference is ~3-4x faster — making it the most practical large-class Quran ASR model for streaming recitation on a phone. Groq's Whisper endpoint already supports `whisper-large-v3-turbo` at $0.04/hr — if this LoRA can be merged and hosted separately, it could replace the standard Groq endpoint call with a Quran-tuned version.

**2. Curriculum learning recipe is reproducible:**
The 3-stage curriculum (clean → diverse → challenge) is a training recipe Sakina can apply to the superior `obadx/muaalem-annotated-compressed-v3` dataset (848h, MIT). Specifically:
- Stage 1: `tarteel-ai/everyayah` (clean)
- Stage 2: `obadx/muaalem-annotated-compressed-v3` (848h diverse, MIT — 3x more than EveryAyah alone)
- Stage 3: `RetaSy/quranic_audio_dataset` (7,000 non-native learner samples — Sakina's exact target demographic)

This would produce a whisper-large-v3-turbo Quran model fine-tuned specifically on beginner learner audio — probably the first ever. License: Apache 2.0 (base) + MIT (data) = Apache 2.0 output.

**3. Apache 2.0 means immediate use:**
Unlike `tadabur-Whisper-Small` (CC BY-NC 4.0 — commercial requires Faisal's permission) and KheemP (unconfirmed license), MaddoggProduction's model can be deployed in production today.

### New Dataset Found: `MohamedRashad/Quran-Recitations`

Used as Stage 2 in curriculum. Details:
- 20 reciters, ayah-level diacritized Arabic + audio
- Sourced from AlQuran Cloud API
- Last updated ~April 2026 (2 months ago from search date June 24)
- Not covered in any prior round
- **Action:** Combine with `obadx/muaalem-annotated-compressed-v3` and `RetaSy` as a curriculum stage-2 dataset.

### New Dataset Found: `ahishamm/QURANICWhisperDataset`

Used as Stage 3 training data and held-out test set by MaddoggProduction. Details:
- License, size, number of reciters: **unknown** (not available from search)
- Appears designed for ASR model training/evaluation
- **Action:** Visit `huggingface.co/datasets/ahishamm/QURANICWhisperDataset` directly to check license, size, and composition.

---

## Monitor Confirmations (All Three Still Unresolved)

### Tarteel: v5.78.2 — Now 6-Day Stall Confirmed (No v5.79+)

- Latest confirmed: **v5.78.2 (June 19, 2026)**
- Multiple search results consistently confirm v5.78.2 as latest; no v5.79 or v5.80 found anywhere
- No Makharij/Mahraj announcement found
- **Now 6 days without a release** (previously 5 days in Rounds 33–38)
- Hotfix sprint (v5.77.3 → v5.78.2, Jun 7–19) has clearly ended; next release may be a larger feature update
- The 6-day gap is the longest since the June 7 sprint began — may signal pre-launch testing of a larger feature

### `uzair0/quran-asr`: Still Training (Model Saves Today)

- Confirmed still showing active model saves ("7 hours ago" from search)
- Still listed as "Training in progress" — no completion signal
- **Trigger NOT met.** Continue monitoring daily.
- When commit frequency drops to weekly → benchmark within 24h on `Buraaq/quran-audio-text-dataset`

### `FaisaI/tadabur-Whisper-Large` and `-Medium`: Still "Coming Soon"

- Only Small (8.7% WER, CC BY-NC 4.0) confirmed released
- Medium and Large: no HuggingFace model found, still listed as "coming soon" on tadabur website
- No sign of imminent release in search results

---

## Summary of Actionable Items

| Priority | Action |
|---|---|
| P0 | Benchmark `MaddoggProduction/whisper-l-v3-turbo-quran-lora-dataset-mix` on `Buraaq/quran-audio-text-dataset` (same test set as KheemP's 5.98% WER) — get fair apples-to-apples comparison |
| P0 | Check `huggingface.co/datasets/ahishamm/QURANICWhisperDataset` — license, size, composition |
| P1 | Apply MaddoggProduction's curriculum learning recipe with `obadx/muaalem-annotated-compressed-v3` (848h, MIT) + `RetaSy/quranic_audio_dataset` (non-native learners) as stages — creates the only Quran LoRA tuned on learner audio |
| P1 | Add `MohamedRashad/Quran-Recitations` to the training dataset inventory |
| Monitor | `uzair0/quran-asr` — check daily for training completion (Apache 2.0, 6.31GB) |
| Monitor | Tarteel release — v5.79+ could include Makharij beta given 6-day gap |
| Monitor | `FaisaI/tadabur-Whisper-Medium` and `-Large` — check FaisaI org daily |

---

## What Was Not Found This Round

- No new arXiv paper on Quran recitation from late June 2026 (IQRA 2026 / arXiv:2603.29087 remains the latest)
- No Tarteel Makharij announcement
- `uzair0/quran-asr` training still incomplete — the potential Apache 2.0 breakthrough remains pending
- `tadabur-Whisper-Large` still not released after 9+ days "coming soon"

---

*Round 39 of ongoing Sakina research. Prior rounds: 1–38 documented in SUMMARY.md.*
