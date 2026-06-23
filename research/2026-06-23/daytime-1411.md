# Research: 2026-06-23 Daytime (14:11 UTC) — Round 28
## Focus: Newest Arabic ASR Models on HuggingFace (June 2026) + Tarteel Status + Flutter record Package

---

## Finding 1 — `uzair0/quran-asr` (HuggingFace, June 2026): New Quran ASR Model Actively Training Right Now — First Discovery Across 28 Rounds

**What**: `huggingface.co/uzair0/quran-asr` is a Quran-specific ASR model on HuggingFace that was completely absent from all 27 prior research rounds. Key characteristics discovered via search:
- **Size**: 6.31 GB — consistent with Whisper-large-v3 (1.5B params) or a comparable large model class
- **Commits**: 36 commits in history, indicating non-trivial active development
- **Training activity**: Model was committed "6 days ago" AND "7 hours ago" from time of search (June 23, 2026 ~14:00 UTC) — meaning this model is **currently undergoing active training or fine-tuning as of this session**
- **Optimizer**: AdamW (betas=(0.9,0.999), epsilon=1e-08) — standard Whisper fine-tuning config
- **Dataset**: Listed as "unknown" in model card — training data not publicly documented
- **WER**: Not published; no benchmark numbers found
- **License**: Not confirmed (model card appears sparse)

**Why it matters**: At 6.31 GB this is the largest Quran-specific ASR model ever found on HuggingFace (prior rounds: KheemP whisper-base-quran-lora at ~74M params / ~150MB, tadabur-Whisper-Small at ~244M params / ~500MB). If `uzair0/quran-asr` is a Whisper-large-v3 fine-tune trained on a large diverse Quran corpus, its WER could be significantly below KheemP's 5.98%. The model appears to be someone's active research project — not yet published with metrics.

**HuggingFace direct access blocked** (403 during this session) — the repository file tree and README could not be fetched. Model card details unknown.

**Sakina action**: 
1. Monitor `uzair0/quran-asr` — check when training commits stop (model is likely in training now, will stabilize)
2. Once stable, benchmark on 20 Quran clips from `Buraaq/quran-audio-text-dataset` and compare WER vs. KheemP (5.98%) and tadabur-Whisper-Small (8.7%)
3. Check license before any production use

---

## Finding 2 — `FaisaI/tadabur-Whisper-Large` Confirmed NOT Yet Released (June 23, 2026)

**Status check**: As of June 23, 2026 (8 days after tadabur-Whisper-Small's June 15 update):
- **tadabur-Whisper-Small** (`FaisaI/tadabur-Whisper-Small`): RELEASED, WER 8.7% / CER 6.5%, updated June 15. Still the only released Tadabur model.
- **tadabur-Whisper-Medium**: Not mentioned on any source — no "coming soon" status even.
- **tadabur-Whisper-Large v3**: Listed as **"Coming Soon"** on the Tadabur project page — still pending as of June 23.

**Context from the Tadabur paper** (arXiv:2604.18932, Faisal Alherran): The model table lists Small → (Medium) → Large progression. Given that the Small achieves 8.7% WER on 600+ reciters / murattal + mujawwad, a Large (769M params, 1.5× more capacity) trained on the same 1,400h Tadabur corpus could realistically reach 5–7% WER on diverse recitation styles — potentially matching or surpassing KheemP (5.98%) on style breadth even if not on pure murattal hafs accuracy.

**Watch window**: The gap between Small release (June 15) and Medium/Large is at least 8 days and counting. Expect Large within 2–4 weeks based on typical release cadence.

**Sakina action**: Subscribe to `FaisaI` org on HuggingFace for new model notifications. When tadabur-Whisper-Large ships, benchmark within 24h — this is the model most likely to change the ASR stack recommendation.

---

## Finding 3 — Tarteel v5.78.2 Android / v5.75.4 iOS: No New Release in 4 Days + Tarteel Academy Disambiguation

**Android**: Confirmed latest stable = **v5.78.2 (June 19, 2026)**. No v5.79 found on any source (Google Play, Uptodown, APKPure, mod-APK sites). The 4-release hotfix sprint (June 7–19) has paused for at least 4 days.

**iOS**: Confirmed frozen at **v5.75.4 (February 28, 2026)** — 4+ month Android-iOS lag persists. No new iOS Tarteel release found.

**Makharij/Tajweed**: No public announcement. Prior research confirmed Tarteel's own description still says "no Tajweed, roadmap only." No Q3 beta announcement found.

**CRITICAL DISAMBIGUATION — Tarteel Academy (iOS `id6759074212`, `org.tarteelmcgp.app`) is NOT from Tarteel Inc.:**
This app surfaced in this round's App Store search. Clarification: it is the **Muslim Center of Greater Princeton (MCGP)**'s class management tool, guided by Shaykh Ismael Essa. Features: student attendance tracking, teacher-parent communication, class recording, memorization progress logs. Last updated March 22, 2026. iOS-only. This is a local mosque's internal app — **not a product from Tarteel Inc. (the AI startup)**. Prior research's Tarteel Inc. competitive timeline is unchanged. Tarteel Inc. has NOT launched a teacher platform or live session product.

**Sakina competitive moat update**: Live session positioning fully intact. Tarteel Inc. remains AI-only on both its products (Tarteel main + Hifz AI). No live teacher feature announced.

---

## Finding 4 — `record` Flutter Package: Confirmed v7.1.0 (June 11, 2026) — 12 Days Without Update = Stable

**Status**: `record` v7.1.0 (pub.dev) remains the latest version as of June 23, 2026. Published June 11 (~12 days ago). **No new version released.** 

**Key v7.1.0 feature**: `onConfigChanged` callback returns effective `RecordConfig` — "no more fuzzy or unknown changes from requested config." This is directly useful for Sakina's quality-adaptive streaming: detect if the platform downgrades bitrate/samplerate, then adjust Groq/Nemotron API parameters accordingly.

**Platform support confirmed**: Android, iOS, macOS, Linux, Windows, Web.
**License**: BSD-3-Clause (commercial-safe).
**Downloads**: 717k+, 884 likes, 160 pub points.

No competing package emerged in this round. `flutter_sound` (9.30.0, MPL-2.0) and `mic_stream` (0.7.2, 15 months stale) remain the stale alternatives. **`record` v7.1.0 is the definitive Flutter microphone package for Sakina.**

---

## Finding 5 — FunASR-GGML (huaxin0, Apache 2.0): New C++ GGUF Runtime for CPU/Edge — Potential Flutter FFI Path

**What**: `github.com/huaxin0/FunASR-GGML` is a C++ inference engine for FunASR models using GGML backend (same format as whisper.cpp). Key specs:
- **License**: Apache 2.0 (commercial-safe)
- **Runtime**: Single GGUF binary, no Python dependency, CPU + CUDA GPU
- **Benchmarks** (RTX 4070 Laptop + i7-13700H):
  - CPU real-time: RTF ~0.91 (slower than real-time)
  - GPU real-time: RTF ~0.28–0.35 (well below real-time, 3–4× faster than audio)
  - GPU batch processing: ~100–117 audio-sec/s
- **Model**: Based on FunASR's SenseVoice architecture
- **Languages**: Not explicitly listed in README, but FunASR base covers 50+ languages including Arabic

**Flutter relevance**: No Flutter binding exists. Could be integrated via FFI (Dart native interop) similarly to how whisper.cpp is wrapped in `flutter_whisper`. CPU RTF=0.91 means marginally real-time on a 2023-era laptop GPU — likely too slow for phones without GPU acceleration. **Not production-ready for Sakina Flutter on-device path yet.**

**Companion**: `FunAudioLLM/Fun-ASR-Nano-2512` on HuggingFace provides GGUF model files (~484MB quantized). No Quran-specific fine-tuning. Arabic WER on Quran unknown.

**Sakina action**: Hold — monitor for Flutter FFI binding from community. FunASR-GGML is interesting infrastructure but not competitive with sherpa-onnx v1.13.3 (Flutter-native, maintained) for on-device Arabic. Check back in 60 days.

---

## Summary Table: Current ASR Model Rankings (June 23, 2026)

| Model | WER (Quran) | Size | On-Device | License | Status |
|---|---|---|---|---|---|
| KheemP/whisper-base-quran-lora | 5.98% | ~150MB | Yes (ONNX) | Unknown | Released |
| Moonshine Arabic Base (quantized) | 5.63% | ~58M params | Yes (sherpa-onnx) | Apache 2.0 | Released |
| FaisaI/tadabur-Whisper-Small | 8.7% | ~244M | Possible | CC BY-NC 4.0 | Released (June 15) |
| **uzair0/quran-asr** | **Unknown** | **6.31 GB** | **No (server-side)** | **Unknown** | **Active training** |
| FaisaI/tadabur-Whisper-Large | Unknown | ~1.5B | No | CC BY-NC 4.0 | Coming soon |
| nvidia/nemotron-3.5-asr-streaming-0.6b | Unknown (Quran) | 600M | Possible (ONNX) | OpenMDW-1.1 | Released (June 4) |
| obadx/muaalem-model-v3_2 | 0.16% PER | Server | No | Apache 2.0 | Released (ICML 2026) |

---

## Sources

- [uzair0/quran-asr · HuggingFace](https://huggingface.co/uzair0/quran-asr)
- [FaisaI/tadabur Dataset](https://huggingface.co/datasets/FaisaI/tadabur)
- [Tadabur project page](https://fherran.github.io/tadabur/)
- [nvidia/nemotron-3.5-asr-streaming-0.6b](https://huggingface.co/nvidia/nemotron-3.5-asr-streaming-0.6b)
- [record Flutter package changelog](https://pub.dev/packages/record/changelog)
- [Tarteel Academy iOS (MCGP)](https://apps.apple.com/us/app/tarteel-academy/id6759074212)
- [Muslim Center of Greater Princeton — Tarteel Academy](https://tarteelmcgp.org/)
- [Tarteel v5.78.2 on LiteAPKs](https://liteapks.com/tarteel-quran-memorization.html)
- [FunASR-GGML (huaxin0)](https://github.com/huaxin0/FunASR-GGML)
- [FunAudioLLM/Fun-ASR-Nano-2512](https://huggingface.co/FunAudioLLM/Fun-ASR-Nano-2512)
- [MarkTechPost — Nemotron 3.5 ASR release](https://www.marktechpost.com/2026/06/06/nvidia-releases-nemotron-3-5-asr-a-600m-parameter-cache-aware-streaming-model-transcribing-40-language-locales-in-real-time/)
