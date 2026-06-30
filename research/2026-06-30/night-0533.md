# Research Report — 2026-06-30 Night (05:33 UTC) — Round 48

**Focus:** Qwen3-ASR Transformers Native API (June 26) + Voxtral TTS Arabic Voice Cloning + Qwen3.5-Omni-Light 113-Language ASR — All Three Missed Across Prior Rounds

---

## Monitor Status (All Unchanged)

| Monitor | Status | Notes |
|---|---|---|
| Tarteel | **v5.78.2 (Day 11+ stall)** | APKPure still v5.78.2; APKMirror v5.77.3. No v5.79+. No Makharij announcement. Longest gap in 48 rounds. |
| `uzair0/quran-asr` | Still training | No WER published. Apache 2.0. |
| `tadabur-Whisper-Large/Medium` | Still "Coming Soon" | 15+ day stall since Small released. |
| `sherpa-onnx` | v1.13.3 stable | No v1.14.x found. |
| `livekit_client` | v2.8.1 stable | No v2.9.0 stable yet. |

**Window status:** Tarteel's 11-day stall is the longest ever observed in this monitoring series. Sakina's Q3 2026 ship window remains maximally open.

---

## Finding 1 — `Qwen/Qwen3-ASR-1.7B` Now Natively Supported by HuggingFace Transformers (June 26, 2026) — Fine-Tune With Standard Trainer, No Custom Package

**Source:** `github.com/QwenLM/Qwen3-ASR` + `huggingface.co/Qwen/Qwen3-ASR-1.7B` model card update dated June 26, 2026.

**What changed:** As of June 26, 2026, `Qwen/Qwen3-ASR-1.7B` and `Qwen/Qwen3-ASR-0.6B` are natively supported by the HuggingFace `transformers` library with `torch.compile` support for faster inference. Previously, using these models required the custom `qwen-asr` pip package (a separate installation step). Now:

```python
from transformers import pipeline
pipe = pipeline("automatic-speech-recognition", model="Qwen/Qwen3-ASR-1.7B")
result = pipe("recitation.wav")
```

**And fine-tuning with the standard HuggingFace Trainer:**
```python
from transformers import Trainer, TrainingArguments, AutoModelForSpeechSeq2Seq
# Fine-tune on muaalem-annotated-compressed-v3 (MIT, 848h) → standard SFT recipe
```

**Why this matters for Sakina — the friction is gone:**

Prior to June 26, fine-tuning Qwen3-ASR required the custom `qwen-asr` package with its own training script. This created friction for:
1. Integration with HuggingFace Trainer (gradient checkpointing, mixed precision, LoRA via PEFT)
2. Using ONNX export pipelines (`optimum-cli`)
3. Deploying via vLLM (standard Transformers model path)

Now all three routes are open:
- **Fine-tune:** `obadx/muaalem-annotated-compressed-v3` (MIT, 848h, 286K utterances) + standard HuggingFace Trainer → first fine-tuned Apache 2.0 Quranic Qwen3-ASR, expected <8% WER (from Tadabur benchmark comparison)
- **Quantize:** `optimum-cli export onnx` → ONNX-INT8 → sherpa-onnx Flutter (pending v1.14.x Qwen3-ASR support, which now has a clear path since the model is Transformers-native)
- **Deploy:** vLLM deployment guide (Red Hat) that already exists for Voxtral Realtime applies equally to any Transformers-native ASR model

**License:** Apache 2.0 ✓

**Sakina actions:**
- **(P0)** Fine-tune `Qwen/Qwen3-ASR-1.7B` on `obadx/muaalem-annotated-compressed-v3` using HuggingFace Trainer (now standard). Target: first Apache 2.0 fine-tuned Quranic Qwen3-ASR, benchmark on Tadabur. The custom package barrier is gone.
- **(P0)** Use `torch.compile` when deploying OOTB Qwen3-ASR-1.7B for real-time cloud inference — this is the latency optimization route before fine-tuning completes.
- **(P1)** Watch sherpa-onnx for Qwen3-ASR ONNX export support (now trivially addable via `optimum` since it's Transformers-native) — on-device Qwen3-ASR in Flutter becomes realistic within weeks.

---

## Finding 2 — `mistralai/Voxtral-4B-TTS-2603` (March 26, 2026, CC BY-NC 4.0 Weights, $0.016/1K Chars API): Arabic TTS With Voice Cloning From 3s Reference — Missed Across All 47 Prior Rounds

**Source:** `huggingface.co/mistralai/Voxtral-4B-TTS-2603` | `mistral.ai/news/voxtral-tts/` | TechCrunch, March 26, 2026.

**What it is:** Mistral's 4B-parameter TTS model completing the Voxtral speech stack (ASR=Realtime covered Round 44; TTS=this model, missed until now). Key specs:
- **9 languages including Arabic ✓** (full list: English, French, German, Spanish, Dutch, Portuguese, Italian, Hindi, Arabic)
- **Voice cloning from 3s reference audio** — captures voice, accent, inflection, intonation, even disfluencies
- **Open weights:** CC BY-NC 4.0 (research/non-commercial); commercial via Mistral API
- **API price:** $0.016/1,000 characters of generated audio
- **Latency:** sub-100ms time-to-first-audio (streaming)
- **Quality:** Human evaluations rate naturalness superior to ElevenLabs Flash v2.5
- **HuggingFace:** `mistralai/Voxtral-4B-TTS-2603`

**Prior coverage (Groq Orpheus, Round 25):** `canopylabs/orpheus-arabic-saudi` (Groq API, $40/1M chars = $0.04/1K chars) — Saudi dialect only, no voice cloning. **Voxtral TTS is 2.5× cheaper** at $0.016/1K chars, and adds voice cloning — the decisive differentiator.

**Why voice cloning matters for Sakina's tajweed UX:**

In Sakina's AI feedback loop (planned: mispronounced word → AI demonstrates correct pronunciation), the reference voice matters culturally. With Voxtral TTS voice cloning:
1. Capture a 3-second sample from a real certified sheikh (with permission)
2. Clone that voice for all AI feedback audio
3. Student hears the correction in a recognizable, culturally authentic reciter voice rather than a generic TTS voice

This creates a UX no competitor has: "AI feedback in [chosen sheikh's] voice." This is defensible and culturally resonant for the GCC market.

**Additional use case — teacher session UX:**
- Teacher records 3s voice sample on profile setup
- AI pre-session warmup (Groq Whisper → tajweed scoring → feedback) voices corrections in the teacher's own voice
- When teacher joins live, the voice transition is seamless

**Comparison table (TTS options for Sakina):**

| TTS | Language | Price/1K chars | Voice Cloning | License |
|---|---|---|---|---|
| Groq Orpheus (`aisha`) | Saudi Arabic | $0.04 | No | Commercial |
| Voxtral TTS | Arabic + 8 others | $0.016 | Yes (3s ref) | CC-NC weights / $0.016 API |
| ElevenLabs | Arabic | ~$0.02 | Yes | Proprietary |

**Sakina actions:**
- **(P1)** Test Voxtral TTS Arabic voice quality on tajweed feedback phrases ("أعد النطق بالإخفاء هنا"). Compare naturalness to Groq Orpheus Aisha voice.
- **(P1)** Prototype voice-cloning UX: record 3s from a certified sheikh → clone → use for all AI correction audio in beta sessions.
- **(P2)** Evaluate commercial API terms (Mistral API) for production use — confirm no Quran-specific restrictions.

---

## Finding 3 — `Qwen/Qwen3.5-Omni-Light` (March 30, 2026, Open Weight on HuggingFace): 113-Language ASR Including Arabic — 47-Round Blind Spot, Crucial for Non-Native Learner Audio

**Source:** `huggingface.co/spaces/Qwen/Qwen3.5-Omni-Offline-Demo` | `huggingface.co/collections/Qwen/qwen35` | arXiv:2604.15804 (Qwen3.5-Omni Technical Report, April 2026).

**What it is:** Qwen3.5-Omni (March 30, 2026) is Alibaba's unified multimodal model processing text, audio, image, and video. The full model (30B MoE) is proprietary/API-only. **However, the Light variant is open-weight on HuggingFace.** Key ASR capability for Sakina:
- **113 languages/dialects** for speech recognition input (vs. Qwen3-ASR's 52 languages)
- **36 languages** for speech generation output
- Claims SOTA WER in 10 languages, competitive in others
- Arabic is confirmed supported
- Based on Qwen3-Omni architecture (predecessor was Apache 2.0 open-weight)
- License: Expected Apache 2.0 (Qwen3-Omni precedent) — verify on model card before use

**Why 113 languages matters vs. Qwen3-ASR's 52:**

Sakina's core demographic is diaspora Muslims whose mother tongue is NOT Arabic. Key learner populations:
- Turkish speakers (~80M Muslims, Arabic as second language)
- Indonesian/Malay speakers (~230M Muslims)
- Urdu/Bengali speakers (~400M Muslims)  
- Somali speakers (~30M)
- Swahili speakers (~50M)
- French-African speakers (~120M Muslims)

When these learners recite Arabic, their ASR accuracy degrades because ASR models hear their accent as "noise." A model trained on 113 languages has seen more cross-linguistic accent variation and may better handle Arabic pronounced with a Turkish or Indonesian accent. **This is the exact population `RetaSy/quranic_audio_dataset` (7,000 non-native recordings, 11+ countries) was designed to test.**

**Critical unknown:** No published WER on Quran or `RetaSy` dataset. Must benchmark.

**Qwen3.5-Omni-Light vs. alternatives for non-native learner audio:**

| Model | Languages | Non-native Arabic handling | License | On-device? |
|---|---|---|---|---|
| Qwen3-ASR-1.7B | 52 | Unknown | Apache 2.0 | Possible (ONNX) |
| Qwen3.5-Omni-Light | 113 | Likely better | Verify Apache 2.0 | Harder (omni-modal) |
| Mega-ASR (Round 45) | English/Chinese | 20% WER ↓ degraded | Apache 2.0 | Apple Silicon MLX |
| KheemP whisper-base | ~100 (Whisper) | Tested on experts only | Apache 2.0 | Yes (sherpa) |

**Sakina actions:**
- **(P1)** Confirm Qwen3.5-Omni-Light license on HuggingFace model card; if Apache 2.0 → benchmark on `RetaSy/quranic_audio_dataset` (non-native, 7K recordings).
- **(P1)** Compare Qwen3.5-Omni-Light Arabic WER vs. Qwen3-ASR-1.7B on RetaSy: if >10% relative improvement on non-native audio → use Qwen3.5-Omni-Light for diaspora learner sessions.
- **(P2)** Check if Qwen3.5-Omni-Light supports streaming (for live session use case); if not, use as fallback post-session scoring.

---

## Secondary Findings

### AyahFinder (getayahfinder.com) — Second "Shazam for Quran" App, Not Previously Catalogued
`getayahfinder.com` — 96% accuracy across 500+ reciters, 2–3 second recognition, 600,000+ training samples. Functionally identical to RecitID (Round 42). Market positioning: Quran listening discovery, NOT learning/teaching. **Not a direct Sakina competitor.** Note: AyahFinder runs a competitive accuracy blog (`blog/46-quran-recognition-apps-tested-accuracy-showdown`) that could be a useful benchmarking source for Sakina's verse-ID accuracy claims. **Action: Read this blog post** for competitor accuracy data usable in Sakina pitch materials.

### `realtime_audio` Flutter Package (pub.dev, Updated Nov 2025) — Audio Chunk Streaming for OpenAI Realtime / ElevenLabs
`pub.dev/packages/realtime_audio` — handles variable-chunk-length audio recording and chunk-queue playback for realtime API integration (OpenAI Realtime API, ElevenLabs Voice, HumeAI Voice). Use case for Sakina: If integrating Voxtral TTS via its streaming API, this package provides the Flutter-side audio queue for streaming TTS output. Not a capture tool (that's `audio_io`/`record`); it's a playback-of-streaming-chunks tool. Updated November 2025.

---

## What's Still Unknown / Still Watching

- **uzair0/quran-asr:** No WER published. Still actively checkpointing. Apache 2.0 confirmed. Benchmark trigger: when checkpoint frequency drops from daily to weekly.
- **tadabur-Whisper-Large/Medium:** 15+ day stall. Author (Faisal Alherran) has not published these sizes. Monitor daily.
- **NabilMH (arXiv:2606.19747 author):** Model release on `huggingface.co/NabilMH` not yet confirmed. The undiacritized-labels finding from Round 45 would be validated by a released model.
- **Qwen3.5-Omni-Light license:** Not confirmed Apache 2.0; must check model card directly.
- **sherpa-onnx Qwen3-ASR ONNX:** Now that Qwen3-ASR is Transformers-native, the path is clear but no sherpa PR opened yet. Watch `k2-fsa/sherpa-onnx` issues for Qwen3-ASR export request.
