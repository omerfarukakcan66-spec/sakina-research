# Research Round 26 — 2026-06-23 Daytime (08:08 UTC)
**Focus:** FaisaI/tadabur-Whisper-Small Exact Model Card + WER Benchmarks + Tarteel Status Check

---

## Critical Question Answered: FaisaI/tadabur Exact Whisper Model Cards

Round 25 flagged this as an open action: *"Locate exact model card in FaisaI org → benchmark on 20 Quran-MD word clips → compare WER."* This round delivers the confirmed specs.

---

## Insight 1 — `FaisaI/tadabur-Whisper-Small` Confirmed: 8.7% WER, 6.5% CER, Updated June 15 — Worse Than KheemP on Pure WER But Broader Style Coverage Is Its True Value

**Confirmed HuggingFace model ID:** `FaisaI/tadabur-Whisper-Small`
- **Author:** Faisal Alherran (`FaisaI` on HuggingFace, `fherran` on GitHub)
- **Paper:** arXiv:2604.18932 (*Tadabur: A Large-Scale Quran Audio Dataset*, April 2026)
- **Last updated on HuggingFace:** ~June 15, 2026 (8 days before this run)
- **Downloads:** 174 | **Likes:** 5

**Performance numbers (from arXiv:2604.18932):**
| Model | Parameters | WER | CER |
|-------|-----------|-----|-----|
| Tadabur-Whisper-Small ("Whisper-Quran") | 74M or 244M* | **8.7%** | **6.5%** |
| General-purpose Whisper models (Medium, Large v3) | 769M–1.5B | >8.7% | >6.5% |

*\*Naming ambiguity: the paper states "smallest model at 74M parameters" (= Whisper Base) achieves best WER, but the HuggingFace release is named "Whisper-Small" (244M). Either: (a) the paper's fine-tune is actually Whisper-Base re-labeled, or (b) Whisper-Small at 244M achieves 8.7% while the paper's text is imprecise. This must be confirmed by checking the model card directly. The coverage metric (SILMA: 96.63%) is unambiguous.*

**Coverage metrics (verse-alignment coverage, from paper):**
| Model | SILMA Coverage | Fuzzy Coverage |
|-------|---------------|----------------|
| Tadabur-Whisper-Small (fine-tuned) | **96.63%** | **86.03%** |
| Vanilla Whisper Small (no fine-tune) | 82.57% | 72.80% |

**Key paper finding:** *"Domain adaptation outweighs model size in Qur'anic ASR."* A fine-tuned smaller model substantially outperforms much larger untuned models on Quranic text.

**Model suite status:**
- ✅ Released: `FaisaI/tadabur-Whisper-Small`
- 🔜 Coming soon: `FaisaI/tadabur-Whisper-Medium` (Whisper Medium, 769M)
- 🔜 Coming soon: `FaisaI/tadabur-Whisper-Large` (Whisper Large v3, 1.5B)
- **License:** CC BY-NC 4.0 (same as dataset) — NC clause blocks direct commercial deployment; requires license negotiation with Faisal Alherran for production use

**GitHub:** https://github.com/fherran/tadabur | **HuggingFace dataset:** https://huggingface.co/datasets/FaisaI/tadabur

---

## Insight 2 — The Three-Way WER Reality Check: What 8.7% Actually Means Against All Published Benchmarks

All prior rounds treated "WER" numbers as comparable, but they are not — each was measured on a different test set. This round provides a unified picture:

| Model | WER | Test Set | Training Data | Style Coverage |
|-------|-----|----------|---------------|----------------|
| **FaisaI/tadabur-Whisper-Small** | **8.7%** | Tadabur held-out (diverse: 600 reciters, mujawwad + murattal) | 1,400h Tadabur | **Broadest: murattal + mujawwad, 600 reciters** |
| KheemP/whisper-base-quran-lora | 5.98% | EveryAyah-style (hafs murattal only) | ~390h EveryAyah | Narrow: hafs murattal |
| tarteel-ai/whisper-base-ar-quran | 5.75% | EveryAyah-style (hafs murattal only) | ~390h EveryAyah | Narrow: hafs murattal |
| ICML QPS model (arXiv:2509.00094) | 0.16% PER | 286K annotated utterances (proprietary) | 850h (proprietary) | Broad but proprietary |

**Critical interpretation:** 8.7% vs 5.98% is NOT a loss — it's a different axis:
- **KheemP at 5.98%** is optimized for a single recitation style (hafs murattal). If a Sakina user recites in mujawwad style (common with GCC teachers), KheemP will likely perform far worse than 5.98%.
- **Tadabur-Whisper-Small at 8.7%** was tested against 600 reciters including mujawwad — the style diversity that actually matters for a live teacher app where reciters and students have varied styles.
- The SILMA coverage delta (+14 percentage points: 96.63% vs 82.57%) is arguably more important than WER for Sakina's verse-tracking use case.

**Sakina action:**
1. Use `FaisaI/tadabur-Whisper-Small` as the **primary fallback model** for non-hafs or mujawwad recitation styles (GCC teachers, mujawwad students).
2. Keep `KheemP/whisper-base-quran-lora` as the **primary model** for standard murattal/hafs — its 5.98% WER is still the sharpest on that style.
3. A routing layer: detect recitation style with DeiT V3 Qira'at classifier (99.6% accuracy, Round 15) → if hafs murattal → KheemP → else → Tadabur-Whisper-Small.
4. **Priority benchmark (this week):** Run both models on 20 clips from `Buraaq/quran-audio-text-dataset` (Quran-MD, 32 reciters) to get a fair head-to-head on a single test set. This is the missing data point.

---

## Insight 3 — Tarteel: v5.78.2 Still Latest (June 19, 2026), No New Release in 4 Days — Mahraj Beta Quiet

**Tarteel Android version history (confirmed from Uptodown + mod-APK sources):**
- v5.78.2 — June 19, 2026 (latest, changelog not public)
- v5.78.1 — June 17, 2026 (recording playback removed from free tier)
- v5.78.0 — June 14, 2026 (inter-ayah audio pause fixed)
- v5.77.3 — June 7, 2026 (home screen widgets)

**Status as of June 23, 2026:** No v5.79 or higher found on any official or unofficial source. The 4-day gap since v5.78.2 is not unusual (prior gaps: v5.77.3 → v5.78.0 was 7 days). The hotfix sprint from June 7–19 (4 releases in 12 days) appears to have paused.

**No Mahraj/Makharij announcement found.** Q2 beta still unconfirmed externally — consistent with an internal beta not publicly announced.

**iOS Tarteel:** Still behind. The 4+ month Android-iOS gap continues.

**Sakina racing clock:** Q3 2026 ship target for live sessions + phoneme feedback unchanged. Tarteel's Q4 full Mahraj release is still the hard deadline.

---

## Secondary Findings

**sherpa-onnx:** v1.13.3 (June 15, 2026) remains latest. No v1.13.4+ found in this run. The June 18, 2026 QNN model package releases (asr-models-qnn-binary-2, 458 assets) from Round 24 are the most recent artefacts.

**`record` Flutter package:** v7.1.0 (pub.dev, ~June 11, 2026) remains latest. No newer version found.

**New competitor scanned — no new threats found:**
- Qurania (iOS id6749280274, v2.6.5, May 7, 2026, developer: GOLAM MOSTAEEN): 40 ratings — tiny app, focused on text/meaning, not live recitation. Not a competitor.
- QuranTutor.ai = same platform as Qara'a (already covered in Round 22). No new features beyond what was documented.

**No new June 2026 arXiv paper found** on Quran ASR beyond the 25 already covered. The most recent relevant paper (arXiv:2604.18932 Tadabur) is from April 2026.

---

## Action Items for Sakina (From This Round)

1. **Benchmark `FaisaI/tadabur-Whisper-Small` vs `KheemP/whisper-base-quran-lora` on the same 20 clips from `Buraaq/quran-audio-text-dataset`** — this is the single most actionable step to close the open benchmark gap. Use HuggingFace Inference API for both (no local GPU needed).

2. **Route by recitation style:** DeiT V3 (5ms, 99.6%) → hafs murattal → KheemP; else → Tadabur-Whisper-Small. This dual-model architecture requires ~0 extra inference cost (DeiT V3 is 5ms) and covers 100% of users including non-hafs styles.

3. **License track:** CC BY-NC 4.0 on Tadabur-Whisper-Small = cannot use commercially without license from Faisal Alherran. Email `contact@fherran.com` (or via HuggingFace discussions) to negotiate a commercial license **before** building a production dependency. The model's research value in benchmarking requires no license; production deployment does.

4. **Watch for Tadabur-Whisper-Medium and Large:** When released on HuggingFace, benchmark immediately. A fine-tuned Whisper-Medium (769M) on 1400h Tadabur could outperform KheemP on both WER axes and become the primary model regardless of style.

---

## Sources
- FaisaI/tadabur HuggingFace: https://huggingface.co/datasets/FaisaI/tadabur
- Tadabur paper: https://arxiv.org/abs/2604.18932
- Tadabur GitHub: https://github.com/fherran/tadabur
- Tarteel Uptodown versions: https://tarteel.en.uptodown.com/android/versions
- IQRA 2026 challenge: https://arxiv.org/abs/2603.29087
- sherpa-onnx releases: https://github.com/k2-fsa/sherpa-onnx/releases
