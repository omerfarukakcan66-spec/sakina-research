# Sakina Research — 2026-06-25 Daytime (07:31 UTC) — Round 44

**Focus:** Voxtral Mini 4B Realtime (Apache 2.0, End-to-End Streaming) + Tadabur Full Release (1400h, Whisper Fine-Tuned, Word-Level Alignments) + IQRA 2026 Generative LALM Techniques for Tajweed MDD

---

## Insight 1 — `mistralai/Voxtral-Mini-4B-Realtime-2602` (Apache 2.0, Feb 4 2026): First Open-Source End-to-End Streaming ASR, <500ms Latency — Never Mentioned in 43 Prior Rounds

**Source:** arXiv:2602.11298 | HuggingFace: `mistralai/Voxtral-Mini-4B-Realtime-2602` | VentureBeat Feb 4 2026 | Apache 2.0

Mistral AI released **Voxtral Mini 4B Realtime 2602** on February 4, 2026 — the first open-source ASR model trained **end-to-end for streaming** rather than adapted from an offline model via chunking or sliding windows.

### Architecture — Why This Is Different

All prior streaming ASR approaches (WhisperFlow, faster-whisper chunked mode, sherpa-onnx streaming) adapt offline models: they split audio into chunks and process each chunk independently, creating errors at chunk boundaries and accumulating latency. Voxtral Realtime uses:

- **Delayed Streams Modeling (DSM)** framework — causal audio encoder with explicit alignment between audio and text streams
- **Ada RMS-Norm** for improved delay conditioning
- Trained end-to-end for streaming, not post-hoc adapted
- 4B parameters (Voxtral Mini size)
- Configurable latency: **as low as 200ms**, with <500ms being the production target

### Performance

- **Multilingual:** 13 languages — Arabic confirmed supported
- Matches **offline transcription quality** at sub-second latency (unlike chunking approaches that degrade)
- "Among the first open-source solutions to achieve accuracy comparable to offline systems" at <500ms delay
- Available on HuggingFace's Transformers library under `VoxtralRealtime` model doc

### License

**Apache 2.0** — full commercial use, fine-tuning, redistribution permitted.

### Why This Matters for Sakina (P0)

Sakina's live session architecture currently relies on buffered Whisper segments or streaming CTC (sherpa-onnx Cohere/Nemotron). The bottleneck: segment-boundary errors cause the ASR to misidentify ayah position, breaking the real-time tracking UX during live teacher sessions.

Voxtral Realtime solves this structurally:
1. **No segment boundaries** → no boundary artifacts → accurate ayah tracking during live recitation
2. **200ms configurable latency** → word-by-word feedback is possible during recitation (not just after), enabling "correction at the point of error" UX
3. **4B parameters on vLLM** → Red Hat AI already published a Day-1 deploy guide (Feb 6, 2026); fits in GPU infra most Sakina deployment targets support
4. **Arabic included** → direct use for Quranic ASR without additional language adaptation

**Comparison to current Sakina stack:**
- vs. sherpa-onnx Cohere INT8: Voxtral has better Arabic (Cohere has 11.2% Quran WER OOTB); Voxtral not yet in Flutter ONNX → use cloud vLLM deployment
- vs. Groq Whisper (batch): Voxtral is streaming; Groq is batch (not suitable for real-time word feedback)
- vs. sherpa-onnx Nemotron streaming: Voxtral's end-to-end training vs. chunked adaptation — Voxtral should have fewer boundary errors

**Sakina actions (P0):**
1. Deploy `mistralai/Voxtral-Mini-4B-Realtime-2602` on vLLM (follow Red Hat guide); test 200ms latency on Arabic Quran audio from `Buraaq/quran-audio-text-dataset`
2. Integrate via WebSocket audio stream from Flutter → vLLM Voxtral → streaming transcript tokens → word diff UI update
3. Benchmark Quranic WER vs. KheemP (5.98%), Cohere Transcribe (11.2%), tadabur-Whisper-Small (8.7%)
4. If Quranic WER acceptable (<12%), replace chunk-based WebSocket ASR with Voxtral Realtime in live session backend

---

## Insight 2 — `FaisaI/tadabur` + `fherran/tadabur` Whisper Fine-Tuned Models (CC BY-NC 4.0, arXiv:2604.18932, April 2026): 1400h / 600 Reciters + Word-Level Timestamps + Tajweed-Aware ASR — Full Picture (43 Rounds of Partial Coverage)

**Source:** arXiv:2604.18932 | HuggingFace: `FaisaI/tadabur` | GitHub: `fherran/tadabur` | April 2026

Tadabur has been referenced across several rounds but the complete picture has not been assembled. This is the definitive summary.

### What Tadabur Is

- **1400+ hours** of Quranic recitation audio from **600+ distinct reciters**
- Complete coverage of all 113 surahs (Al-Fatiha excluded — zero-indexed issue in the dataset)
- Styles: **murattal** (measured recitation, common for learning) + **mujawwad** (melodic, performance recitation)
- **Word-level temporal alignments** automatically derived: each audio file includes JSON metadata with start/end timestamps per Quranic word
- Broad acoustic diversity: different recording conditions, vocal characteristics, microphone quality — intentionally not studio-clean

### Fine-Tuned Whisper Models Released

Alongside the dataset, the authors released Whisper models fine-tuned on Tadabur:
- **tadabur-Whisper-Small** — already released (confirmed at Round 39+), WER 8.7% on Tadabur benchmark
- **tadabur-Whisper-Medium** — "Coming Soon" (Day 10+ as of Round 43)
- **tadabur-Whisper-Large** — "Coming Soon" (Day 10+ as of Round 43)
- Fine-tunes handle: prolonged phoneme durations (madd), tajwid rules, melodic articulation, wide acoustic diversity

### License

**CC BY-NC 4.0** — non-commercial research use only. This is the binding constraint: Sakina CANNOT use Tadabur-fine-tuned models directly in a commercial product without negotiating a separate license with the authors. The dataset itself may be usable for internal research/benchmarking.

### Why Tadabur Matters Despite License Constraint

1. **Benchmark standard:** Tadabur is emerging as the canonical Quranic ASR benchmark (Cohere Transcribe 11.2% OOTB, Qwen3-ASR evaluated there, KheemP is the current SOTA at 5.98%)
2. **Word-level alignments:** Even if the dataset is non-commercial, the alignment methodology (JSON schema per-file) is a blueprint for Sakina's own data pipeline. Every teacher session can generate aligned word-audio pairs via the same approach
3. **Fine-tune recipe:** The training recipe (tajweed-aware training objective, prolonged phoneme handling) is described in the paper — reproducible on Sakina's Apache 2.0 base models (Qwen3-ASR, Cohere Transcribe)
4. **Mujawwad coverage:** No other dataset covers mujawwad style at scale — relevant for advanced students learning to recite with melody

**Sakina actions:**
1. Read arXiv:2604.18932 for the fine-tuning recipe (tajweed-aware loss, prolonged phoneme handling) — reproduce on `CohereLabs/cohere-transcribe-03-2026` (Apache 2.0) using `obadx/muaalem-annotated-compressed-v3` (MIT, 848h) as training data
2. Contact Faisal Alherran (author, GitHub: fherran) to negotiate a commercial license for tadabur-Whisper models — QCRI/Islamic AI researchers are typically receptive to partnership discussions
3. Use Tadabur as the evaluation benchmark (CC BY-NC 4.0 permits benchmarking for research)
4. Once tadabur-Whisper-Large releases ("Coming Soon"), immediately benchmark vs. KheemP to confirm new SOTA

---

## Insight 3 — IQRA 2026 (Interspeech 2026, arXiv:2603.29087): 19 Teams, Best System F1 +0.2787, Generative LALMs for Arabic Mispronunciation Detection — Direct Tajweed Error Pipeline Blueprint

**Source:** arXiv:2603.29087 | HuggingFace Spaces: `IqraEval/IqraEval_Interspeech_26` | Interspeech 2026

IQRA 2026 is the second edition of the Automatic Pronunciation Assessment challenge for Modern Standard Arabic (MSA), with Quranic recitation as the primary evaluation domain. This is the same team as Fanar 2.0 (Ahmed Ali, QCRI/HBKU).

### Results

- **19 competing teams** — nearly double the first edition
- Best system: **absolute F1 improvement of +0.2787** over the organizer baseline
- Key techniques from top systems:
  - **Novel temporal alignment strategies** — better phoneme-level timing than forced alignment alone
  - **Rigorous data curation** (removing ambiguous mispronunciations from training labels)
  - **First application of generative LALMs (Large Audio Language Models) to Arabic MDD** — using audio LLMs to both detect and classify mispronunciation type (substitution/deletion/insertion) in a single forward pass

### New Dataset: `Iqra_Extra_IS26`

Introduced alongside the challenge: authentic human mispronounced Arabic speech (NOT synthesized or noise-augmented). This is the only confirmed public dataset of real human Arabic mispronunciations — directly relevant to Sakina's tajweed error detection.

### Why Generative LALMs for Arabic MDD Matters

Prior Sakina pipeline: Whisper ASR → transcript diff vs. expected text → rule-based tajweed classification. This has two failure modes:
1. ASR transcription error ≠ actual mispronunciation (the student said "w" but ASR wrote the wrong character)
2. Rule-based classification misses contextual mispronunciations (a phoneme is correct in isolation but incorrect in a specific tajweed context)

Generative LALMs bypass both: the LALM processes raw audio + expected text → directly outputs mispronunciation type (substitution/deletion/insertion) without an intermediate transcript. The IQRA 2026 winners show this achieves SOTA on Arabic MDD.

**Models in scope:** `Qwen2-Audio`, `Qwen-Audio` (Qwen team's audio LLMs), `Gemini 1.5 Pro` (multimodal). Open-source LALM option: `Qwen/Qwen2-Audio-7B` (Apache 2.0).

**Sakina actions:**
1. Obtain `Iqra_Extra_IS26` dataset from HuggingFace Spaces `IqraEval/IqraEval_Interspeech_26` — evaluate whether it's publicly downloadable or challenge-only
2. Test `Qwen/Qwen2-Audio-7B` on mispronunciation detection: input = student audio clip + expected verse text → output = mispronunciation type + phoneme location
3. Compare LALM-based MDD vs. current transcript-diff approach on `RetaSy/quranic_audio_dataset` (non-native learner audio, 7,000 recordings)
4. If LALM MDD achieves >70% F1 on learner audio, replace the rule-based tajweed classifier with Qwen2-Audio-7B inference — this would be the most accurate open-source tajweed error detector available

---

## Flutter Real-Time Audio Architecture Notes (2026 Synthesis)

Based on multiple search threads:

**Recommended package stack for Sakina (June 2026):**
| Layer | Package | Notes |
|---|---|---|
| Mic capture | `record` (pub.dev) | PCM stream, 16kHz, iOS + Android |
| Raw PCM processing | `flutter_sound` | PCM Float32/Int16 streams to Dart, in-process DSP |
| Speech-specific buffering | `picovoice/flutter-voice-processor` | Handles VAD + frame buffering for speech models |
| Streaming ASR | WebSocket to vLLM Voxtral | Sub-500ms round-trip for cloud |
| On-device ASR fallback | `sherpa_onnx` v1.13.3 | Cohere INT8 ONNX (Arabic, 14 langs, offline) |

**Critical optimization (missed in prior rounds):** Set mic sample rate to **exactly 16,000 Hz** in `record` config. All Whisper-family and Conformer models expect 16kHz — resampling in Flutter adds 5-15ms unnecessary latency. Configure at capture time, not post-process.

**Isolates pattern for tajweed scoring:** Run phoneme duration measurement (Madd timing from Qwen3-ForcedAligner timestamps) in a Dart Isolate to avoid blocking the audio capture thread. The ASR WebSocket and the scorer should be on separate Isolates.

---

## Monitor Status — Round 44

| Item | Status | Change from Round 43 |
|---|---|---|
| Tarteel version | v5.78.2 (Jun 19) — **Day 10+ stall** | No change — stall extends |
| `uzair0/quran-asr` | Still training (last checkpoint ~daily) | No completion signal |
| `tadabur-Whisper-Large/Medium` | Still "Coming Soon" | Day 10+ since Small released |
| `sherpa-onnx` | v1.13.3 stable | No v1.14.x |
| `livekit_client` | v2.8.1 stable / v2.9.0-dev.0 prerelease | Unchanged |
| Voxtral Realtime | **NEW — Apache 2.0, Feb 4 2026** | First identified this round |

---

## Sources

- arXiv:2602.11298 (Voxtral Realtime)
- `mistralai/Voxtral-Mini-4B-Realtime-2602` — HuggingFace
- VentureBeat: Voxtral Transcribe 2 launch (Feb 4, 2026)
- Red Hat Developer: Run Voxtral Mini 4B Realtime on vLLM (Feb 6, 2026)
- arXiv:2604.18932 (Tadabur paper)
- GitHub: `fherran/tadabur`
- HuggingFace: `FaisaI/tadabur`
- arXiv:2603.29087 (IQRA 2026)
- HuggingFace Spaces: `IqraEval/IqraEval_Interspeech_26`
- Interspeech 2026 challenges page
- dasroot.net: Building Flutter Voice Assistants with Local Speech Recognition (Dec 2025)
- GitHub: `Picovoice/flutter-voice-processor`
- OpenASR Leaderboard (arXiv:2412.13788)
