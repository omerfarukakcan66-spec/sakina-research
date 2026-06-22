# Sakina Research — Rolling Summary

Cumulative top insights across all research runs, newest first.

---

## 2026-06-19 Night (08:57 UTC)
*Full report: [research/2026-06-19/night-0857.md](2026-06-19/night-0857.md)*

### Insight 1 — Moonshine v2 Arabic is the Streaming ASR to Beat Tarteel With
Released Feb 23, 2026 (open weights, Apache 2.0). Ergodic streaming encoder with a dedicated Arabic model — delivers bounded time-to-first-token regardless of utterance length. At 27M parameters it fits on edge devices and outperforms WhisperLargeV3 on real-time benchmarks. This is architecturally superior to Tarteel's Whisper-based approach for *live* verse tracking, because Moonshine emits tokens while the user is still speaking. Immediate action: pull the Arabic weights, run on a 30-second Quran clip, measure TTFT vs. WhisperKit.

### Insight 2 — WavLink Embeddings Enable Sub-500ms On-Device Verse Fingerprinting for 6 MB
WavLink (January 2026) adds a single learnable global token to Whisper's encoder, compressing audio to a Matryoshka-style embedding as small as sub-100 dimensions. Pre-compute embeddings for all 6,236 Quran verses × 10 reciters = 62,360 embeddings = ~6–12 MB on-device. FAISS nearest-neighbor lookup identifies the surah/ayah before any decoding completes — this is Phase 1 of a three-phase pipeline that bypasses Tarteel's slow segment-level ASR approach entirely. Tarteel does not appear to use this technique.

### Insight 3 — IQRA 2026 (Interspeech, Sydney Sep 27–Oct 1) is the Active Benchmark for Quran Pronunciation AI
The second IqraEval challenge attracted 19 teams and released `Iqra_Extra_IS26`, the first real human mispronounced MSA speech corpus (prior work used only TTS). Best system achieved +0.2787 F1 over baseline using generative LALMs for mispronunciation diagnosis. Sakina must use this dataset and benchmark to train and evaluate tajweed error feedback — it is the only standardized, community-validated dataset for this exact problem. Hugging Face Space: `IqraEval/IqraEval_Interspeech_26`.
