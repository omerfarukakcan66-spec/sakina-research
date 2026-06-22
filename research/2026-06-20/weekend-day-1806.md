# Sakīna Research — Weekend Day Implementation Focus (Round 5)
**Date:** 2026-06-20 18:06 UTC  
**Focus:** Working code we can use TODAY — GitHub repos, demos, audio packages, APIs

---

## 1. Flutter + Real-Time Arabic ASR: GitHub Repos (2025-2026)

### 1.1 k2-fsa/sherpa-onnx ⭐ PRIMARY RECOMMENDATION
- **URL:** https://github.com/k2-fsa/sherpa-onnx
- **Stars:** 13,100+
- **Last commit:** June 18, 2026 (releases `asr-models-qnn-2` and `v1.13.3`)
- **pub.dev:** [`sherpa_onnx`](https://pub.dev/packages/sherpa_onnx) v1.13.3 — 113 likes, updated June 2026

**What it does:** Definitive offline speech engine for Flutter/Dart. On-device STT, TTS, VAD, speaker diarization, and speech enhancement. Full platform coverage: Android, iOS, Linux, macOS, Windows, HarmonyOS.

**Flutter-specific contents:**
- `flutter-examples/streaming_asr/` — complete real-time streaming ASR Flutter app
- `flutter-examples/non_streaming_vad_asr/` — batch ASR with VAD
- `flutter-examples/tts/` — TTS demo

**Arabic ASR path:**
- `sherpa-onnx-cohere-transcribe-14-lang-int8-2026-04-01` model supports Arabic (one of 14 languages)
- Whisper multilingual ONNX models (tiny/base/small/medium) all cover Arabic
- `sherpa-onnx-moonshine-base-ar-quantized-2026-02-27` — quantized Arabic-only Moonshine Base, 5.63% WER

**Key 2025-2026 changelog:**
- v1.13.3 (June 2026): Multilingual Nemotron-3.5 streaming ASR
- v1.12.35 (Apr 2026): Cohere Transcribe support with Dart/Flutter bindings
- v1.12.28: Moonshine v2 support
- v1.12.27: FireRedASR CTC models
- Ongoing: Qualcomm QNN NPU acceleration

**How to use today:** `flutter pub add sherpa_onnx`, copy `flutter-examples/streaming_asr/`, swap model config to Arabic. The `streaming_asr.dart` file is the complete mic-to-token pipeline.

---

### 1.2 CrispStrobe/CrisperWeaver
- **URL:** https://github.com/CrispStrobe/CrisperWeaver
- **Stars:** 21
- **Last commit:** June 12, 2026 (v0.7.9)
- **License:** AGPL-3.0

**What it does:** Cross-platform Flutter app for fully offline transcription and TTS backed by CrispASR C++ engine (whisper.cpp fork). 40+ ASR model families, 20+ TTS families. Audio never leaves device.

**Arabic ASR support:** Yes — via Qwen3-ASR (30+ languages), Gemma4-E2B (140+ languages), Whisper families. Arabic TTS available ("Lahgtna" Chatterbox variant).

**How to use:** Clone, build with CrispASR backend, select Qwen3-ASR or Whisper + Arabic language. Shows how to implement runtime model swapping in Flutter — architecture reference for Sakīna's offline/cloud toggle.

---

### 1.3 CrispStrobe/CrispASR (C++ backend)
- **URL:** https://github.com/CrispStrobe/CrispASR
- **Stars:** 327
- **Last commit:** June 14, 2026 (v0.7.2)
- **License:** MIT/AGPL

**What it does:** C++ ggml-based runtime supporting 26 ASR backends: Cohere Transcribe, Parakeet TDT, Voxtral, Canary 1B v2, Whisper variants. Arabic via Qwen3-ASR and Gemma4-E2B.

**How to use for Flutter:** Not a Flutter package directly — CrisperWeaver is the Flutter frontend. Could integrate via FFI/platform channels as a native plugin, but sherpa-onnx is a cleaner path.

---

## 2. sherpa-onnx Flutter Example (Confirmed 2025-2026)

**URL:** https://github.com/k2-fsa/sherpa-onnx/tree/master/flutter-examples/streaming_asr  
**Last activity:** Actively maintained alongside sherpa-onnx mainline (June 2026)

**What it does:** Official real-time streaming ASR Flutter example. Demonstrates mic input → VAD → live transcription on Android, iOS, Linux, macOS, Windows. Downloads models at runtime.

**Architecture:**
1. `record` package captures PCM16 from mic
2. Stream fed to sherpa-onnx online recognizer
3. Partial results emitted token-by-token while speaking
4. Complete utterances finalized on silence (VAD-gated)

**Arabic path:** Replace the default model config (`online_model.dart`) with Moonshine Arabic or Cohere Transcribe ONNX config. One file change.

---

## 3. Quran ASR Demos and Colab Notebooks (2025-2026)

### 3.1 tarteel-ai/whisper-base-ar-quran — Live HuggingFace Space
- **HF URL:** https://huggingface.co/tarteel-ai/whisper-base-ar-quran
- **Live Demo:** https://huggingface.co/spaces/tarteel-ai/demo-whisper-base-ar-quran
- **WER:** 5.75% on Quranic recitations
- **Dataset:** EveryAyah (25,000+ verses, multiple reciters, 117 GB)
- **Status:** Live inference UI — paste/record audio, get diacritized Arabic transcript

**Models on HuggingFace:**

| Model | Notes |
|---|---|
| `tarteel-ai/whisper-base-ar-quran` | 74M params, 5.75% WER, live demo |
| `tarteel-ai/whisper-tiny-ar-quran` | Lighter, faster, less accurate |
| `OdyAsh/faster-whisper-base-ar-quran` | CTranslate2 format, WhisperX compatible, up to 70× realtime on GPU |
| `IJyad/whisper-large-v3-Tarteel` | Community fine-tune of large-v3 on Tarteel data |
| `KheemP/whisper-base-quran-lora` | LoRA fine-tune, 5.98% WER |

**Key fact:** All commercial APIs strip diacritics (tashkeel). Tarteel models preserve them. This is critical for verse matching.

---

### 3.2 yazinsai/offline-tarteel — Research Document + ONNX Demo
- **URL:** https://github.com/yazinsai/offline-tarteel
- **Stars:** 221
- **Last updated:** March 1, 2026 (v0.1.0)
- **Live demo:** https://offline-tarteel.whhite.com

**What it does:** Offline Quran verse identifier using quantized NVIDIA FastConformer ONNX (115 MB). 16 kHz mono audio → CTC inference → fuzzy match against all 6,236 verses. 95%+ recall, 0.7s latency on Apple Silicon.

**Contains:** `RESEARCH-audio-to-verse.md` — most comprehensive 2026 survey of Quran ASR approaches (Feb 24, 2026 document). Covers WavLink, Moonshine v2, WhisperKit, FAISS verse lookup architectures.

**Platforms:** Browser (WASM), React Native, Python. Flutter port via same ONNX model + `flutter_onnxruntime` is a direct path.

---

### 3.3 fherran/tadabur — Dataset + Fine-Tuned Model
- **URL:** https://github.com/fherran/tadabur
- **HuggingFace:** https://huggingface.co/FaisaI/tadabur-whisper-small
- **Stars:** 162
- **Last updated:** April 22, 2026
- **License:** CC BY-NC 4.0

**What it does:** 1,400+ hours of Quranic audio, 600+ reciters, word-level timestamp alignment, entire Quran with structured JSON metadata. Fine-tuned model `FaisaI/tadabur-whisper-small` ready for inference.

**Use for Sakīna:** Best training corpus if we need to fine-tune beyond Tarteel's baseline. Already has a ready model.

---

### 3.4 Abdelrahman47-code/Quranic-Verse-Recognition — Jupyter Notebooks
- **URL:** https://github.com/Abdelrahman47-code/Quranic-Verse-Recognition
- **Stars:** 19

**What it does:** Python/Tkinter app + 2 Jupyter notebooks for local execution. Records audio → identifies Surah/Ayah using `tarteel-ai/whisper-tiny-ar-quran`. The notebooks are the closest thing to a runnable Colab for Quran ASR (run locally or upload to Colab manually).

---

## 4. Flutter Microphone + Streaming Audio Packages (2025-2026)

### 4.1 `record` ⭐ TOP CHOICE
| Field | Value |
|---|---|
| **pub.dev** | https://pub.dev/packages/record |
| **GitHub** | https://github.com/llfbandit/record |
| **Version** | 7.1.0 (released mid-June 2026 — ~10 days ago) |
| **Pub points** | 160/160 |
| **Likes** | 884 |
| **Downloads** | 717,000 |
| **Platforms** | Android, iOS, Linux, macOS, Web, Windows |
| **Streaming** | Yes — `Stream<Uint8List>` PCM16 and AAC-LC |

**Why it's the one:** Only package with 160/160 pub points, 3 releases in the past month, and cross-platform desktop + web support. Already used in sherpa-onnx's official Flutter streaming example. Do not use `mic_stream` (15 months stale) or `flutter_sound` (slower release cadence).

---

### 4.2 `flutter_sound`
| Field | Value |
|---|---|
| **pub.dev** | https://pub.dev/packages/flutter_sound |
| **Version** | 9.30.0 (December 2025) |
| **Pub points** | 140 |
| **Likes** | 1,640 |
| **Platforms** | Android, iOS, Web |
| **Streaming** | Yes — PCM Float32 and PCM Int16 Dart streams |

**When to use:** If you need simultaneous record + playback, or advanced audio routing. More feature-complete than `record`. Updated December 2025 — still in 2025-2026 window.

---

### 4.3 `sound_stream`
| Field | Value |
|---|---|
| **pub.dev** | https://pub.dev/packages/sound_stream |
| **Version** | 0.4.2 (October 2025) |
| **Pub points** | 140 |
| **Likes** | 114 |
| **Platforms** | Android, iOS |
| **Streaming** | Yes — pure streaming PCM16 mono, no file I/O |

**When to use:** Lowest overhead for mic-to-network streaming (feeding audio directly to a remote ASR API without touching disk). No Android + iOS desktop support.

---

### 4.4 `audio_streamer`
| Field | Value |
|---|---|
| **pub.dev** | https://pub.dev/packages/audio_streamer |
| **GitHub** | https://github.com/cph-cachet/flutter-plugins |
| **Version** | 4.3.0 (August 2025) |
| **Pub points** | 150 |
| **Downloads** | 11,600/week |
| **Platforms** | Android, iOS |
| **Streaming** | Raw PCM with adjustable sample rate |

**When to use:** Research/health context — CACHET (Copenhagen) maintains this. Strong download velocity suggests active userbase.

---

## 5. Arabic Speech Recognition APIs (Free/Cheap)

### Pricing Comparison Table

| Provider | Price/min | Free Tier | Arabic Quality | Quranic | Streaming |
|---|---|---|---|---|---|
| **Tarteel self-host** | $0 weights + compute | Free HF weights | Excellent (Quran-fine-tuned) | ✅ YES (diacritics) | WhisperX |
| **Groq Whisper** | $0.04/hr paid | 2,000 req/day + 7,200 sec/hr | Good (MSA) | No diacritics | No |
| **AssemblyAI** | $0.0025/min | $50 credits (~333 hrs) | Good | No | Yes (300ms) |
| **Soniox** | ~$0.002/min | Yes | Good | No | Yes (mid-sentence) |
| **Deepgram Nova-3** | $0.0043 (batch) / $0.0077 (stream) | $200 credits | Excellent (dialects) | No diacritics ❌ | Yes (WebSocket) |
| **OpenAI Whisper** | $0.003-0.017/min | $5 credits (3mo) | Good (MSA) | No | `gpt-realtime` only |
| **ElevenLabs Scribe v2** | $0.0065/min | Yes | Good | No | Yes (150ms) |
| **Azure Speech** | $0.006 (batch) / $0.0167 (RT) | 5 hr/mo | Good | Custom training | Yes |
| **Google STT v2** | $0.016/min | 60 min/mo | Good | No | Yes |
| **Munsit (CNTXT)** | Contact required | Contact | Best Arabic-native | Unconfirmed | Yes |

---

### 5.1 Tarteel Self-Hosted (Best for Quran)
- **URL:** https://huggingface.co/tarteel-ai
- **Cost:** Free weights; GPU hosting ~$20-60/month at low traffic (HuggingFace Endpoints, scale-to-zero)
- **Why critical:** Only option that preserves tashkeel (diacritics) — essential for Quranic verse matching
- **Quick start:** `OdyAsh/faster-whisper-base-ar-quran` enables WhisperX streaming, up to 70× realtime on T4 GPU

---

### 5.2 Groq Whisper (Best Free Prototype Tier)
- **URL:** https://console.groq.com
- **Endpoint:** `https://api.groq.com/openai/v1/audio/transcriptions`
- **Free tier:** 2,000 requests/day + 7,200 audio-seconds/hour (no credit card required)
- **Speed:** 164× real-time on Groq LPU hardware
- **Arabic:** `language=ar` parameter; OpenAI-compatible SDK
- **Limitation:** No diacritics; batch-only (no streaming WebSocket)
- **Best for:** Zero-cost prototype validation — ~2,000 Quran verse checks/day at zero cost

---

### 5.3 Deepgram Nova-3 (Best Commercial Arabic — Launched Jan 2026)
- **URL:** https://deepgram.com/product/speech-to-text/arabic
- **Free tier:** $200 credits (no credit card)
- **Pricing:** $0.0043/min batch; $0.0077/min streaming
- **Arabic:** 9 dialect groups, 17 regional variants, 40% lower WER vs competitors
- **Critical limitation:** Strips diacritics — dealbreaker for verse matching without post-processing

---

### 5.4 AssemblyAI (Cheapest Commercial)
- **URL:** https://www.assemblyai.com
- **Free tier:** $50 credits (~333 hours at Universal-2 rate)
- **Pricing:** $0.0025/min (Universal-2, 99 languages including Arabic)
- **Streaming:** ~$0.0042/min, 300ms latency
- **Best for:** Budget experiments

---

### 5.5 ElevenLabs Scribe v2 Realtime (Lowest Cloud Latency — 150ms)
- **URL:** https://elevenlabs.io/speech-to-text-api
- **Launched:** January 6, 2026
- **Pricing:** $0.0065/min realtime
- **Arabic:** 99 languages including Arabic (SA, UAE variants)
- **Best for:** Live recitation feedback UI where latency matters

---

### 5.6 Munsit (CNTXT) — Best Arabic-Native Commercial (Contact Required)
- **URL:** https://munsit.com
- **Status:** Ranked #1 on independent Arabic ASR benchmarks (NADI 2025)
- **Coverage:** 30,000 hours of Arabic speech, 25+ dialects, REST + streaming
- **Key:** Built in UAE, Arabic-first architecture
- **Action:** Contact for pricing; evaluate vs. Deepgram on Quran specifically

---

## Architecture Recommendations for Sakīna

### MVP Stack (Zero Cost, Today)
```
Flutter mic (record v7.1.0)
  → sherpa-onnx streaming_asr example
    → sherpa-onnx-moonshine-base-ar-quantized (on-device, 5.63% WER)
  OR → Groq Whisper API (cloud fallback, 2,000 req/day free)
  → verse matching against hafs_smart_v8
```

### Production Stack (Low Cost, Quranic Quality)
```
Flutter mic (record v7.1.0)
  → sherpa-onnx (offline) with tarteel-ai/whisper-base-ar-quran ONNX
  → Tarteel HF Endpoints ($20-60/month, scale-to-zero, diacritics preserved)
  → Deepgram Nova-3 ($200 free credits, fallback for speed)
  → verse matching + alignment (yayaiu6 Levenshtein algorithm)
```

### Critical Diacritics Warning
**All commercial APIs (Deepgram, AssemblyAI, OpenAI, ElevenLabs) strip diacritics (tashkeel/harakat).** Quranic verse matching requires them. Either use Tarteel self-hosted models, or implement a diacritization restoration layer on top of commercial output.

---

## Quick Reference: What to Use TODAY

| Task | Tool | Start Point |
|---|---|---|
| Flutter mic streaming | `record` v7.1.0 | `flutter pub add record` |
| On-device Arabic ASR | `sherpa_onnx` v1.13.3 | `flutter-examples/streaming_asr/` |
| Best offline Arabic model | Moonshine Arabic ONNX | `sherpa-onnx-moonshine-base-ar-quantized-2026-02-27` |
| Quran-specific accuracy | `tarteel-ai/whisper-base-ar-quran` | HF Inference or self-host |
| Free cloud ASR prototype | Groq Whisper | 2,000 req/day, no card |
| Best commercial Arabic | Deepgram Nova-3 | $200 free credits |
| Live Quran ASR demo | offline-tarteel HF Space | https://huggingface.co/spaces/tarteel-ai/demo-whisper-base-ar-quran |
| Quranic training data | Tadabur dataset | `FaisaI/tadabur` on HuggingFace |

---

## Sources
- https://github.com/k2-fsa/sherpa-onnx
- https://pub.dev/packages/sherpa_onnx
- https://github.com/k2-fsa/sherpa-onnx/tree/master/flutter-examples/streaming_asr
- https://github.com/CrispStrobe/CrisperWeaver
- https://github.com/CrispStrobe/CrispASR
- https://pub.dev/packages/record
- https://pub.dev/packages/flutter_sound
- https://pub.dev/packages/sound_stream
- https://pub.dev/packages/audio_streamer
- https://huggingface.co/tarteel-ai/whisper-base-ar-quran
- https://huggingface.co/spaces/tarteel-ai/demo-whisper-base-ar-quran
- https://github.com/yazinsai/offline-tarteel
- https://github.com/fherran/tadabur
- https://huggingface.co/FaisaI/tadabur-whisper-small
- https://github.com/Abdelrahman47-code/Quranic-Verse-Recognition
- https://deepgram.com/product/speech-to-text/arabic
- https://console.groq.com
- https://www.assemblyai.com
- https://elevenlabs.io/speech-to-text-api
- https://munsit.com
- https://soniox.com/speech-to-text/arabic
- https://platform.openai.com/docs/guides/speech-to-text
