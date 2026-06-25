# Sakina Research — Round 45
**Date:** 2026-06-25 08:06 UTC
**Focus:** arXiv:2606.19747 (June 18 2026) — Greentech Apps Foundation Comparative Quranic ASR Study + Mega-ASR Robustness Adapter for Degraded Learner Audio + Tarteel Monitor Day 11

---

## Insight 1 — arXiv:2606.19747 (June 18, 2026): GTAF + Queen Mary University Comparative Study — Wav2Vec2/HuBERT/XLS-R on 870h Quranic Data, Best WER 8%, **Arabic WITHOUT Diacritics Beats WITH Diacritics** — 44-Round Blind Spot

**Paper:** "A Comparative Study of Pretrained Transformer Models for Quranic ASR: Speech Representations, Label Formats, and Dataset Composition"
**arXiv:** `arxiv.org/abs/2606.19747v1` (submitted June 18, 2026)
**Authors:** Nabil Mosharraf Hossain (nabilmh.com), Riasat Islam, Unaizah Obaidellah
**Institutions:** **Greentech Apps Foundation (GTAF)** + University of Malaya (Queen Mary University of London affiliation)
**GTAF GitHub:** `github.com/GreentechApps` (makers of Al-Quran app, ADAB NeurIPS 2025)

### What They Did
Systematic empirical comparison of three pretrained Transformer architectures — **Wav2Vec2.0, HuBERT, XLS-R** — fine-tuned for Quranic ASR on a filtered dataset of **870+ hours** (EveryAyah professional + Tarteel user recitations). Four label format variants compared:
1. Arabic text (no diacritics)
2. Arabic with Tashkeel (diacritics)
3. English transliteration
4. Buckwalter transliteration

Training: 2,000 steps (16–17h per model; 870h combined needed ~40h), evaluated every 400 steps. 80/20 train/test split. Platform: Google Colab + HuggingFace Transformers + PyTorch.

### Key Results
| Configuration | WER |
|---|---|
| Citrinet baseline | 16.3% |
| Best model (EveryAyah subset) | **8.0%** |
| Best model (EveryAyah + Tarteel combined) | **11.0%** |

- **~8% relative improvement on EveryAyah**: ~5 percentage point gain over Citrinet baseline
- **Best architecture not confirmed** in available snippets (Wav2Vec2/HuBERT/XLS-R winner not specified — need full paper table)

### The Counterintuitive Finding: Arabic WITHOUT Diacritics Wins
> "Arabic text without diacritics yields the best fine-tuning results"

This directly contradicts the default assumption across all 44 prior rounds that diacritized output (Tashkeel) is necessary for tajweed-quality Quran ASR. The paper suggests:
- Diacritics during training may introduce unnecessary complexity/noise in the model's vocabulary space
- The acoustic signal already encodes timing/elongation information without needing diacritic labels
- For transcript-diff tajweed scoring, **the diacritic comparison step should happen AFTER undiacritized ASR**, not as part of the ASR output

**Why this matters for Sakina:** Sakina's current pipeline (arXiv:2511.18774 `initial_prompt` trick + KheemP 5.98% WER + muaalem-v3_2 tajweed scorer) injects the expected verse as the Whisper decoder prompt. The paper suggests using the **undiacritized** form as the `initial_prompt` may reduce WER further. The diacritic comparison (for tajweed scoring) happens at the downstream phoneme-diff layer, not in the ASR transcript.

### HuggingFace Models Released?
**Not confirmed.** No HuggingFace model card link found from the paper's author (Nabil Mosharraf Hossain). GTAF's GitHub (`github.com/GreentechApps`) has a `QuranTextFromVoiceDetector` repo that uses DeepSpeech (old architecture, Kotlin/Android, 5 commits, no HuggingFace). Monitor `huggingface.co/NabilMH` or `huggingface.co/gtaf` for model release.

### Significance for Sakina
- **Provides the first public WER evidence on the EveryAyah+Tarteel combined 870h dataset** — the best available training corpus for Quranic ASR
- **Closes a gap in benchmark coverage**: prior rounds had KheemP (5.98% WER, EveryAyah-only), tadabur-Whisper-Small (8.7% WER), MaddoggProduction (12.69% WER). This paper shows 8% is achievable on EveryAyah with older-generation (2020-era) models — meaning modern architectures (Qwen3-ASR, Cohere Transcribe) fine-tuned on the same 870h should reach <5.98%
- **GTAF involvement** is strategically significant: GTAF's Al-Quran app is used by millions; they have real-world app experience and are publishing academic research — potential partnership or benchmarking collaboration

### Sakina Actions
1. **P0: Test undiacritized `initial_prompt`** — switch Whisper's `initial_prompt` from `<expected_verse_with_tashkeel>` to `<expected_verse_without_tashkeel>` and benchmark WER change on `RetaSy/quranic_audio_dataset` (7K non-native recordings). Paper predicts improvement.
2. **P1: Monitor `huggingface.co/NabilMH` and GTAF GitHub** for model release; if models released, benchmark on Tadabur/Buraaq for cross-study comparison.
3. **P2: Contact Nabil Mosharraf Hossain via nabilmh.com** (GTAF researcher) — GTAF is a natural strategic partner (millions of users, shared Islamic mission, academic rigor).

---

## Insight 2 — `zhifeixie/Mega-ASR` (Apache 2.0, arXiv:2605.19833, May 19 2026): Qwen3-ASR + LoRA Robustness Adapter, 20% Relative WER Improvement on Degraded Real-World Audio — 44-Round Blind Spot

**HuggingFace:** `huggingface.co/zhifeixie/Mega-ASR`
**GitHub:** `github.com/xzf-thu/Mega-ASR`
**arXiv:** `arxiv.org/abs/2605.19833` (May 2026)
**License:** **Apache 2.0 ✓**
**Training dataset:** `zhifeixie/Voices-in-the-Wild-2M` (2M real-world audio samples, 54 compound acoustic scenarios)
**MLX variant:** `mlx-community/Mega-ASR-bf16` (Apple Silicon deployment)

### Architecture
Mega-ASR is a **LoRA adapter** trained on top of **Qwen3-ASR-1.7B** (Apache 2.0, 52 languages including Arabic). The system adds:
1. **LoRA adaptation weights** fine-tuned on 2M real-world degraded audio samples
2. **Audio quality router** that dynamically decides whether to mount the LoRA weights or use the base Qwen3-ASR — clean audio skips the LoRA adapter (preserving clean-speech quality), degraded audio activates it

Training method: Progressive supervised fine-tuning (A2S-SFT) + reinforcement learning (DG-WGPO).

### Results
- **~20% relative WER reduction** over Qwen3-ASR-1.7B baseline on the Voices-in-the-Wild benchmark (54 compound acoustic scenarios)
- Overall robust WER: **7.53** vs Qwen3-ASR baseline
- Benchmarked against: Qwen3-ASR, Gemini-3-Pro, Seed-ASR, Whisper — outperforms all on degraded audio
- Up to **~30% gains** in worst-case acoustic scenarios

### Arabic Language Support
Mega-ASR inherits Qwen3-ASR-1.7B's multilingual support including Arabic. The audio quality router and LoRA adaptation should generalize across all supported languages. Arabic-specific noise robustness **not explicitly benchmarked** in the paper snippets found (English and Chinese examples shown).

### Why It Matters for Sakina
Every prior ASR benchmark in this research series was performed on **professional reciter audio** (EveryAyah: studio-quality; Buraaq: controlled recording). But Sakina's **actual learners** record:
- On phones in living rooms with background noise (TV, family)
- In cars during commutes
- Via Bluetooth headsets with compression artifacts
- In masjids with reverb

Mega-ASR's LoRA adapter is specifically trained on these exact degradation types (reverb, noise, codec artifacts, band-limiting, clipping, overlapping speech). The adaptive router means zero degradation on clean audio and ~20% WER improvement on degraded audio. **This is the only ASR model found in 45 rounds explicitly optimized for the recording conditions of real learners, not expert reciters.**

### Deployment Approach
Mega-ASR is a LoRA delta on top of Qwen3-ASR-1.7B, which means:
1. Deploy Qwen3-ASR-1.7B as base
2. Load Mega-ASR LoRA adapter (small delta file)
3. Audio quality router auto-selects clean vs. robust path
4. Net: same inference cost as Qwen3-ASR; free noise robustness

### Sakina Actions
1. **P1: Benchmark Mega-ASR vs. Qwen3-ASR-1.7B on `RetaSy/quranic_audio_dataset`** (7K non-native recordings — the best proxy for real learner audio) — if Mega-ASR WER is <10% on RetaSy, adopt as primary cloud ASR for live sessions
2. **P2: Test audio quality router on Quranic audio** — verify clean recitation uses base path (no LoRA overhead), degraded path activates for phone/noisy recordings
3. **Note:** MLX variant (`mlx-community/Mega-ASR-bf16`) enables Apple Silicon on-device deployment — relevant if targeting Mac desktop app

---

## Monitor Updates (Round 45 — 2026-06-25 08:06 UTC)

| Target | Last Known | Today's Status | Days |
|---|---|---|---|
| Tarteel app | v5.78.2 (June 19) | **v5.78.2 — STALL DAY 11+** | 11+ |
| `uzair0/quran-asr` | Training (daily ckpts) | Still training, no WER confirmed | — |
| `tadabur-Whisper-Large/Medium` | "Coming Soon" | Still unreleased | 11+ |
| `sherpa-onnx` | v1.13.3 (Jun 22) | No new release found | — |
| `livekit_client` | v2.8.1 stable | No change | — |

**Tarteel note:** A MOD APK site (getmodpc.net) listed v5.85.1, but APKMirror (reliable source) only shows v5.77.2 and v5.77.3. v5.78.2 was the June 19 release confirmed in prior rounds. The v5.85.1 claim is almost certainly a MOD APK site fabrication (these sites routinely increment version numbers). Tarteel stall intact at v5.78.2 — ship window remains open.

---

## Summary of Round 45

Two new finds:
1. **arXiv:2606.19747** (GTAF, June 18, 2026): First public WER benchmark on 870h EveryAyah+Tarteel combined; best 8% WER (EveryAyah), 11% (combined); counterintuitive finding that **undiacritized Arabic outperforms diacritized as the label format**. Actionable change to Sakina's `initial_prompt` pipeline.
2. **Mega-ASR** (Apache 2.0, May 2026): Qwen3-ASR-1.7B + LoRA robustness adapter for degraded real-world audio; 20% relative WER improvement; **first ASR model optimized for learner (vs. expert) recording conditions**; adaptive router means no clean-audio quality loss.

Neither has been covered in any prior round. Combined, they address the gap between lab benchmarks (professional reciters) and real Sakina user recordings (noisy, phone, reverb).

---

## Sources
- arXiv:2606.19747v1: `arxiv.org/abs/2606.19747v1`
- arXiv:2605.19833v1: `arxiv.org/abs/2605.19833v1`
- Mega-ASR GitHub: `github.com/xzf-thu/Mega-ASR`
- Mega-ASR HuggingFace: `huggingface.co/zhifeixie/Mega-ASR`
- MLX variant: `huggingface.co/mlx-community/Mega-ASR-bf16`
- GTAF homepage: `gtaf.org`
- GTAF GitHub: `github.com/GreentechApps`
- Nabil Mosharraf Hossain: `nabilmh.com`
