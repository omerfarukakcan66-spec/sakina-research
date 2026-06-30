# Sakina Research — Round 49 Daytime (06:06 UTC, 2026-06-30)

**Topic:** Newest Arabic ASR Models on HuggingFace (2025–2026) + Monitors

---

## MAIN FINDING: `microsoft/VibeVoice-ASR` (arXiv:2601.18184, Jan 21 2026, MIT, 7B params) — 48-Round Blind Spot

### What it is
`huggingface.co/microsoft/VibeVoice-ASR` | `github.com/microsoft/VibeVoice`  
arXiv:2601.18184 (VIBEVOICE-ASR Technical Report, January 26, 2026)

Microsoft open-sourced VibeVoice-ASR on **January 21, 2026** as a unified speech understanding model. Architecture: 7B-parameter model built on **Qwen2.5-7B backbone** + continuous speech tokenizers (acoustic + semantic at 7.5 Hz ultra-low frame rate) + diffusion head. It is **NOT a fine-tune of Whisper** — it is a next-generation end-to-end ASR architecture trained from scratch on 60-minute long-form audio.

### Key capabilities
- **50+ languages including Arabic** (confirmed)  
- **60-minute single-pass processing** (up to 64K token length) — handles full Quran recitation sessions without chunking  
- Unifies **ASR + Speaker Diarization + Timestamping** in one forward pass  
- Handles code-switching natively (important for diaspora learners mixing Arabic/English)  
- **WER: 7.77% average** across 8 English benchmarks on Open ASR Leaderboard; 12.07% MLC-Challenge average  
- Arabic WER: **not yet published in available sources** (critical to benchmark)
- **LoRA fine-tuning supported**  
- Native HuggingFace Transformers support (as of March 6, 2026)  
- **License: MIT ✓** (fully commercial, fine-tuning derivatives permitted)  
- Also on Azure AI Foundry as `microsoft-vibevoice-asr-hf`

### VibeVoice full suite (all MIT)
| Model | Release | Params | Type | Arabic |
|---|---|---|---|---|
| VibeVoice-TTS | Aug 25, 2025 | 1.5B | TTS | ❌ (English/Chinese only) |
| VibeVoice-Realtime | Dec 3, 2025 | 0.5B | Streaming TTS | ❌ (no Arabic) |
| **VibeVoice-ASR** | **Jan 21, 2026** | **7B** | **ASR** | **✓ confirmed** |

### Why it matters for Sakina

**1. Long-form session ASR:** Every prior ASR model in 48 rounds requires chunked streaming to handle a full recitation session (5–20 minutes). VibeVoice-ASR processes up to **60 minutes in one pass** — it was designed for meeting transcription but this matches Sakina's live session length. No chunk-boundary errors, no rolling buffer complexity.

**2. Integrated timestamps:** VibeVoice-ASR produces word-level timestamps natively in the same pass as transcription. This could replace the `Qwen3-ForcedAligner-0.6B` (Round 43) for the `click-to-hear` teacher review UX — one model instead of two.

**3. Speaker diarization:** Teacher-student session audio has two speakers. VibeVoice-ASR's built-in diarization distinguishes teacher corrections from student recitation automatically — no separate diarization pass needed.

**4. MIT license:** No commercial restrictions. LoRA fine-tuning on `obadx/muaalem-annotated-compressed-v3` (848h, MIT) would be the first MIT 7B Arabic ASR model fine-tuned on Quran.

**5. 7B vs. production viability:** At 7B params, VibeVoice-ASR cannot run on-device (too large for iPhone). But for **cloud backend** (vLLM or Azure Foundry inference), 7B is perfectly serviceable — one A10G handles 10–20 concurrent sessions. This replaces the cloud ASR layer (currently Groq Whisper + Cohere Transcribe).

### Critical unknowns (must verify)
1. **Arabic WER on Quran:** No Quranic or Arabic benchmark in available tech report. Benchmark on `Buraaq/quran-audio-text-dataset` and `RetaSy/quranic_audio_dataset`.
2. **Streaming:** Technical report doesn't mention streaming — the 60-min single-pass design implies batch mode. For live real-time sessions, Voxtral Mini Realtime (Round 44, Apache 2.0, 200ms streaming) may still be preferred.
3. **vLLM support:** Verify VibeVoice-ASR is supported by vLLM for serving (Transformers-native suggests it should work).

### Sakina actions
- **(P1) Benchmark VibeVoice-ASR on `Buraaq/quran-audio-text-dataset`** — compare to KheemP 5.98%, Cohere 11.2%, Voxtral Mini. One A10G on Modal, ~2h.
- **(P1) Test on `RetaSy/quranic_audio_dataset`** (7K non-native learner recordings) for real-user degradation.
- **(P2) If Arabic WER < 15% OOTB → fine-tune on muaalem-annotated-compressed-v3** (MIT + MIT → 60-min-capable Quranic ASR with no commercial restrictions).
- **(P3) Evaluate integrated diarization** — does it correctly separate teacher voice from student recitation in a 10-min session? If yes, remove the dedicated diarization step from Sakina's pipeline.

---

## SECONDARY FINDING: `iTarek/Quran-Muaalem-iOS` (MIT, GitHub 2026) — Production iOS Reference for Sakina's Tajweed Layer

`github.com/iTarek/Quran-Muaalem-iOS`

A production-ready iOS Swift application + FastAPI backend that deploys `obadx/muaalem-model-v3_2` (660M params, Wav2Vec2BERT, Apache-2.0) as a serverless GPU API on **Modal** with auto-scaling.

### Architecture
- **Backend:** FastAPI on Modal (serverless GPU), wraps muaalem-model-v3_2. Endpoints: phoneme-level comparison, Sifat analysis, word-by-word phoneme mapping, configurable Tajweed rules including Madd lengths.
- **iOS app (Swift):** Arabic UI → record screen (select Sura + Ayah with verse preview) → results screen (word-based scoring, error cards) → interactive letter highlighting when an error card is tapped.
- **License:** MIT (both the app and Modal deployment code).

### Why it matters for Sakina
This is the **most complete public reference implementation of Sakina's exact product** found in 49 rounds:
1. **Modal deployment pattern** is directly reusable — Modal serverless GPU auto-scales muaalem inference to zero when no sessions are running (vs. Sakina paying for idle GPU time). The `modal deploy modal_app.py` + REST API pattern applies directly to Sakina's backend.
2. **Swift UI patterns** for Arabic text display, letter-level error highlighting, and word scoring are ready to study for Sakina's iOS teacher/student apps.
3. **FastAPI wrapping muaalem** — resolves the biggest integration question: how to call muaalem's CTC model from a mobile app. The API schema (phoneme comparison endpoint, Sifat analysis) is a blueprint.

### What it does NOT have (Sakina's differentiation remains intact)
- No live WebRTC teacher session
- No real-time streaming ASR (batch only — record → submit → results)
- No teacher marketplace / booking
- No LiveKit / multi-user session
- No SRS / Hifdh tracking

### Sakina actions
- **(P0) Clone and study the Modal deployment** → adapt for Sakina's muaalem-v3_2 backend: use same `modal deploy` pattern, add streaming endpoint for live sessions
- **(P1) Study iOS Swift error-display patterns** → adapt letter highlighting + word scoring for Sakina's Flutter teacher review screen (via platform channel or as Flutter reference)
- **(P2) Contact iTarek** — potential collaborator or early user for Sakina

---

## THIRD FINDING: arXiv:2509.00094 = ICML 2026 Paper (Peer-Reviewed Confirmation of muaalem 0.16% PER)

`icml.cc/virtual/2026/80144` — The obadx "Automatic Pronunciation Error Detection and Correction of the Holy Quran's Learners Using Deep Learning" paper has been **accepted and presented at ICML 2026** (International Conference on Machine Learning — the world's top ML venue).

This confirms that `obadx/muaalem-model-v3_2` (the current primary scorer in Sakina's P1 pipeline) is backed by **peer-reviewed ICML 2026 results** (0.16% average PER). The pipeline (98% automated dataset construction, Tasmeea verification algorithm, QPS phonetic encoding, multi-level CTC, 848h / 286K utterances) is now the **highest peer-reviewed Quranic ASR accuracy result ever published**.

Additionally: `obadx/recitation-segmenter-v2` (HuggingFace) — a fine-tuned `facebook/w2v-bert-2.0` for segmenting Quran recitations at waqf (pause) points, at 20ms resolution, ~3GB GPU. Published as part of the same ICML 2026 paper pipeline. **New HuggingFace asset, not covered in prior rounds.** Useful for Sakina: pre-segment student audio at waqf boundaries before passing to the Tajweed scorer → cleaner phoneme alignment.

### Sakina action
- **(P2) Download `obadx/recitation-segmenter-v2`** → use as preprocessing for live session audio: segment at waqf → independent ASR + scoring per segment → cleaner alignment for duration-based rules (Madd, Ghunnah).

---

## MONITORS (Round 49 Status — All Unchanged)

| Monitor | Last confirmed status | Days stalled |
|---|---|---|
| **Tarteel** | v5.78.2 (June 19, 2026) — no v5.79+ on APKPure/Uptodown/Aptoide | **~11 days** (longest in 49 rounds) |
| `uzair0/quran-asr` | Still training, no WER published | 49+ rounds |
| `tadabur-Whisper-Large/Medium` | Still "Coming Soon" | 15+ days |
| `sherpa-onnx` | v1.13.3 (June 15, 2026), no v1.14.x | Stable |
| `livekit_client` | v2.8.1 stable, v2.9.0-dev.0 prerelease | Unchanged |

Tarteel stall now enters a 12th day with no release — the longest gap observed in 49 rounds. No Makharij announcement. No Tarteel blog posts since February 2026. The ship window for Sakina's Q3 2026 launch remains maximally open.

---

## Summary

| Find | Source | License | Priority |
|---|---|---|---|
| `microsoft/VibeVoice-ASR` (7B, 60-min, Arabic+, Jan 2026) | arXiv:2601.18184 | MIT | P1 benchmark |
| `iTarek/Quran-Muaalem-iOS` (Modal + muaalem + Swift iOS) | github.com/iTarek | MIT | P0 study |
| `obadx/recitation-segmenter-v2` (waqf boundary, 20ms, ICML 2026) | HuggingFace | Apache-2.0 | P2 |
| obadx muaalem = ICML 2026 accepted paper | icml.cc/virtual/2026/80144 | — | Pitch material |
