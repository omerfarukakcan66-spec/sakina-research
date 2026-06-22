# Weekend Day Research — 2026-06-21 09:04

**Focus:** Working code for Sakina (live Quran recitation app) — Flutter + Arabic ASR stack  
**Constraint:** 2025–2026 sources only

---

## 1. GitHub Repos — Real-Time Arabic ASR with Flutter

### operonte/transcripcion
- **URL:** https://github.com/operonte/transcripcion
- **Last Commit:** 2026-06-20
- **Stars:** 0 (brand new, created 2026-06-20)
- **Language:** Dart / Flutter
- **What it does:** Live EN↔ES subtitles from system audio or microphone using sherpa-onnx streaming + Whisper offline on Flutter Linux desktop. The most recent real sherpa-onnx + Flutter streaming demo found. While not Arabic-specific, the architecture directly maps to Sakina: sherpa-onnx streaming model + Flutter mic input + real-time display.
- **Relevance:** Copy the plumbing (sherpa-onnx Dart FFI + mic stream → chunked inference → display) and swap the model for an Arabic one.

### k2-fsa/sherpa-onnx (main monorepo)
- **URL:** https://github.com/k2-fsa/sherpa-onnx
- **Last Commit:** 2026-06-18 (commit `6faa814` — packaging fix; `feb870c` — Parakeet CTC QNN support)
- **Stars:** ~1,400 (actively maintained, 975+ commits in 2025-2026)
- **Language:** C++ core; Flutter/Dart, Python, Rust, Go, Swift, Kotlin bindings
- **What it does:** Offline speech-to-text, TTS, speaker diarization, VAD using ONNX Runtime. No internet required. Supports Android (arm64, armeabi-v7a, x86, x86_64), iOS (arm64), Linux, macOS, Windows.
- **Flutter example path:** `flutter-examples/streaming_asr/` — uses ZipFormer CTC/Transducer models for real-time streaming ASR in Flutter.
- **Arabic support:** Whisper multilingual models (tiny through large-v3) run via sherpa-onnx and handle Arabic. The tarteel-ai fine-tuned Quran model can be converted to ONNX and loaded here.
- **Action:** This is the primary offline ASR path for Sakina. Use `sherpa_onnx` pub.dev package (v1.13.3, published 6 days ago) + `flutter-examples/streaming_asr` as the integration template.

---

## 2. sherpa-onnx Flutter Example (2025-2026 Confirmed)

### sherpa_onnx (pub.dev package)
- **URL:** https://pub.dev/packages/sherpa_onnx
- **Version:** 1.13.3 (published ~2026-06-15, 6 days ago as of today)
- **Likes:** 113 | **Points:** 150
- **Platforms:** Android, iOS, Linux, macOS, Windows
- **What it does:** Flutter package wrapping the sherpa-onnx C++ core. Drop this into `pubspec.yaml` and you get streaming ASR, TTS, VAD, speaker ID — all offline.

### Streaming ASR Flutter Example
- **URL:** https://github.com/k2-fsa/sherpa-onnx/tree/master/flutter-examples/streaming_asr
- **Key files:**
  - `streaming_asr.dart` — main app logic
  - `online_model.dart` — model configuration list (add Arabic model here)
  - `assets/` — model files go here
- **Models supported:** ZipFormer bilingual zh-en (default), any sherpa-onnx compatible streaming model
- **Integration:** Download `tarteel-ai/whisper-base-ar-quran` → convert to ONNX → load via sherpa-onnx → plug into this example.

### Colab Notebook for Sherpa-onnx + Whisper
- **URL:** https://github.com/k2-fsa/colab/blob/master/sherpa-onnx/sherpa_onnx_whisper_large_v3.ipynb
- Shows how to run Whisper Large v3 via sherpa-onnx in Colab — usable as conversion pipeline for Arabic Quran model.

---

## 3. Quran ASR Demos and Models (2025-2026)

### sayedmahmoud266/quran-ai-transcriping
- **URL:** https://github.com/sayedmahmoud266/quran-ai-transcriping
- **Last Commit:** October 2025
- **Stars:** 25 | **Forks:** 7
- **Language:** Python (90%)
- **What it does:** FastAPI REST API that transcribes Quranic audio → identifies exact Surah + Ayah + timestamp for each verse. Claims 100% verse detection accuracy. Uses tarteel-ai/whisper-base-ar-quran model + RapidFuzz for fuzzy verse matching against PyQuran's 6,236 verse database (with diacritics).
- **Action TODAY:** Clone, run locally with `uvicorn`, point Sakina's Flutter app at `localhost:8000` for immediate verse detection during development. No GPU needed for whisper-base.

### obadx/recitations-segmenter + recitation-segmenter-v2
- **GitHub:** https://github.com/obadx/recitations-segmenter
- **HuggingFace:** https://huggingface.co/obadx/recitation-segmenter-v2
- **Model:** Fine-tuned Wav2Vec2Bert for sequence-frame-level classification at 20ms resolution
- **What it does:** Splits continuous Quran recitation at waqf (pause) boundaries — not at Ayah breaks, but at actual recitation pauses. Trained on 850+ hours / 300K annotated utterances.
- **Why it matters for Sakina:** This is the segmentation layer Sakina needs before verse matching — split the mic stream at natural pauses, then match each segment to an Ayah.

### tarteel-ai/whisper-base-ar-quran (HuggingFace model)
- **URL:** https://huggingface.co/tarteel-ai/whisper-base-ar-quran
- **What it does:** OpenAI Whisper base fine-tuned specifically for Quranic Arabic recitation. This is the core recognition model used by quran-ai-transcriping above.
- **Integration paths:**
  1. **Cloud:** Call directly via HuggingFace Inference API
  2. **Local server:** `quran-ai-transcriping` wraps it as a FastAPI endpoint
  3. **On-device:** Convert to ONNX → load in sherpa-onnx for offline Flutter use

### obadx/muaalem-model-v3_2 (pronunciation error detection)
- **URL:** https://huggingface.co/obadx/muaalem-model-v3_2
- **What it does:** ASR model for detecting pronunciation errors in Quran learners. Goes beyond transcription to identify tajweed mistakes. Paper: "Automatic Pronunciation Error Detection and Correction of the Holy Quran's Learners Using Deep Learning" (2025).
- **Relevance:** Phase 2 feature for Sakina — score recitation quality, not just identify verses.

---

## 4. Flutter Microphone + Streaming Audio Packages

### record (RECOMMENDED)
- **URL:** https://pub.dev/packages/record
- **Version:** 7.1.0 (published ~2026-06-11, 10 days ago)
- **Likes:** 884 | **Points:** 160 | **Downloads:** 717k
- **Platforms:** Android, iOS, Windows, macOS, Linux, Web
- **License:** BSD-3-Clause
- **Streaming:** YES — `startStream()` returns `Stream<Uint8List>` in PCM16bits, perfect for real-time ASR
- **Codecs:** AAC, Opus, WAV, FLAC, PCM16bits
- **Why recommended:** Most downloaded, actively maintained (latest release 10 days ago), broadest platform coverage, permissive license, native streaming support. This is the one to use.

### yasinarik/mic_stream_recorder
- **URL:** https://github.com/yasinarik/mic_stream_recorder
- **Created:** 2025-06-10 | **Last push:** 2025-06-11
- **Stars:** 3
- **What it does:** Flutter plugin for real-time microphone audio stream recording on iOS and Android. Lightweight alternative if `record` is too heavy.
- **Note:** Very new, minimal adoption. Use `record` instead unless you need something specific this provides.

### mic_stream
- **URL:** https://pub.dev/packages/mic_stream
- **Version:** 0.7.2
- **Likes:** 99 | **Points:** 130
- **License:** GPL-3.0 ⚠️ — This license is incompatible with a proprietary app. Avoid for Sakina.
- **Platforms:** Android, iOS, macOS only

---

## 5. Open API Endpoints for Arabic Speech Recognition

### Groq API (BEST: Free + Fast + Arabic)
- **Docs:** https://console.groq.com/docs/speech-to-text
- **Models:** Whisper Large v3 ($0.111/hr) and Whisper Large v3 Turbo ($0.04/hr)
- **Free tier:** 2,000 requests/day + 7,200 audio seconds/hour — NO credit card required
- **Speed:** Turbo processes at 228× real-time (60-min file in <16 seconds)
- **Arabic support:** Yes — Whisper multilingual supports Arabic
- **API compatibility:** OpenAI-compatible API — same SDK call as OpenAI, just swap base URL
- **Action TODAY:** Sign up at console.groq.com → get API key → use `openai` Dart package with `baseUrl: 'https://api.groq.com/openai/v1'` — zero extra code needed.

### OpenAI Transcription API
- **Models:** `gpt-4o-mini-transcribe` (recommended), `gpt-4o-transcribe`, `whisper-1`
- **Price:** ~$0.006/min for gpt-4o-mini-transcribe; Whisper-1 at $0.006/min
- **Arabic support:** Yes — 99+ languages
- **Note:** Groq is 89% cheaper for Whisper and equally capable on Arabic. Start with Groq free tier.

### ElevenLabs Scribe
- **URL:** https://elevenlabs.io/speech-to-text/arabic
- **Arabic WER:** 3.1% on FLEURS benchmark — best-in-class accuracy
- **Use case:** If Groq accuracy is insufficient for Quran recitation, escalate to Scribe for production

### Speechmatics
- **URL:** https://www.speechmatics.com/speech-to-text/arabic
- **Arabic accuracy:** 96% word accuracy, 24% lower WER than Google
- **Note:** Enterprise pricing, but has the best Arabic-specific model including bilingual Arabic-English

---

## Recommended Stack for Sakina This Weekend

```
Flutter mic (record v7.1.0)
  └─ PCM16 stream (startStream())
       ├─ [OFFLINE PATH] sherpa_onnx v1.13.3
       │     └─ tarteel-ai/whisper-base-ar-quran (ONNX converted)
       │          └─ verse text → fuzzy match vs PyQuran DB
       └─ [CLOUD PATH] Groq API free tier
             └─ Whisper Large v3 Turbo  
                  └─ quran-ai-transcriping FastAPI (local dev)
                       └─ verse + timestamp → Sakina UI
```

**Minimum viable path for today:**
1. Add `record: ^7.1.0` to pubspec.yaml
2. Clone `quran-ai-transcriping` → `uvicorn main:app` locally
3. Stream mic PCM → buffer 3-5 seconds → POST to local API → receive Surah+Ayah
4. No GPU, no model conversion, works today

**Weekend goal:**
- Saturday: Get local API + Flutter mic recording working end-to-end
- Sunday: Swap local API for Groq free-tier cloud, test latency on real recitations

---

## Sources
- https://github.com/k2-fsa/sherpa-onnx
- https://github.com/k2-fsa/sherpa-onnx/tree/master/flutter-examples/streaming_asr
- https://pub.dev/packages/sherpa_onnx
- https://github.com/sayedmahmoud266/quran-ai-transcriping
- https://github.com/obadx/recitations-segmenter
- https://huggingface.co/obadx/recitation-segmenter-v2
- https://huggingface.co/obadx/muaalem-model-v3_2
- https://pub.dev/packages/record
- https://pub.dev/packages/mic_stream
- https://github.com/yasinarik/mic_stream_recorder
- https://github.com/operonte/transcripcion
- https://console.groq.com/docs/speech-to-text
- https://k2-fsa.github.io/sherpa/onnx/index.html
- https://northflank.com/blog/best-open-source-speech-to-text-stt-model-in-2026-benchmarks
- https://www.gladia.io/blog/best-open-source-speech-to-text-models
