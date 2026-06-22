# Sakīna Research — Weekend Day Session
**Date:** 2026-06-20 09:04 UTC  
**Focus:** Working code for real-time Arabic/Quran ASR with Flutter  
**Source constraint:** 2025–2026 only

---

## 1. GitHub Repos: Real-Time Arabic ASR with Flutter

### 1a. CrisperWeaver
- **URL:** https://github.com/CrispStrobe/CrisperWeaver
- **Last commit:** June 12, 2026 (v0.7.9)
- **Stars:** 21
- **What it does:** On-device, offline Flutter STT app using ggml/Whisper. Supports 40+ ASR model families including Whisper (99 languages → Arabic included), Qwen3-ASR, Gemma4-E2B (140+ languages), FunASR. AGPL-3.0. Written in Dart (88.9%).
- **Arabic relevance:** Whisper is included and supports Arabic; "Lahgtna Arabic" TTS also bundled.
- **Verdict ✅:** Most recent Flutter + Arabic ASR repo with active commits in 2026. Good starting point to strip down for Sakīna.

### 1b. Flutter-EasySpeechRecognition
- **URL:** https://github.com/Jason-chen-coder/Flutter-EasySpeechRecognition
- **What it does:** Flutter real-time ASR using sherpa-onnx, extends the official streaming_asr example with additional UX features.
- **Verdict ✅:** Good companion to the sherpa-onnx example below.

---

## 2. sherpa-onnx Flutter Streaming ASR

### 2a. k2-fsa/sherpa-onnx — Main Repo
- **URL:** https://github.com/k2-fsa/sherpa-onnx
- **Stars:** 13,100+
- **Last meaningful update:** Active as of June 2026 (pub package v1.13.3 published ~June 15, 2026)
- **Flutter example path:** `flutter-examples/streaming_asr/`
- **Direct file:** https://github.com/k2-fsa/sherpa-onnx/blob/master/flutter-examples/streaming_asr/lib/streaming_asr.dart
- **What it does:** Offline speech-to-text, TTS, VAD, speaker diarization using ONNX runtime + next-gen Kaldi. Supports Android, iOS, Linux, macOS, Windows. Streaming and non-streaming modes.
- **Arabic/Whisper:** The framework supports loading any Whisper model (including Arabic fine-tunes); default example uses Chinese-English zipformer but model is swappable.

### 2b. sherpa_onnx Flutter Package (pub.dev)
- **URL:** https://pub.dev/packages/sherpa_onnx
- **Version:** 1.13.3 (published ~June 15, 2026)
- **Weekly downloads:** 22,900
- **Pub score:** 150 pts, 113 likes
- **Platforms:** Android, iOS, Linux, macOS, Windows
- **Verdict ✅:** The gold-standard for on-device streaming ASR in Flutter. Load `tarteel-ai/whisper-base-ar-quran` converted to ONNX → plug into this.

### 2c. How to wire Arabic Whisper into sherpa-onnx Flutter
Key steps (from HuggingFace forum thread):
1. Download `tarteel-ai/whisper-base-ar-quran` from HuggingFace
2. Convert to ONNX via `sherpa-onnx` export scripts
3. Set `OnlineRecognizer` config to Whisper model type in Dart
4. Stream mic audio via `mic_stream` → feed frames to recognizer

---

## 3. Quran ASR — Working Demos & Notebooks

### 3a. offline-tarteel ⭐ TOP PICK
- **URL:** https://github.com/yazinsai/offline-tarteel
- **Stars:** 221
- **What it does:** Identifies Quran surah/ayah from audio recitation offline. NVIDIA FastConformer quantized to ONNX (131 MB). Sub-second latency, 95%+ recall. Runs in browser, React Native, and Python.
- **Live demo:** https://offline-tarteel.whhite.com (FastAPI backend + React frontend)
- **Model approach:** Mel spectrogram → ONNX inference → CTC decode → fuzzy-match against Quran verse corpus.
- **Verdict ✅:** Best offline Quran ASR with working demo RIGHT NOW. React Native compatible means Flutter port is feasible via same ONNX model.

### 3b. Real-Time-Quran-recitation-tracker-System
- **URL:** https://github.com/yayaiu6/Real-Time-Quran-recitation-tracker-System
- **Stars:** 121
- **What it does:** Real-time recitation tracking, word-level alignment, error detection, adaptive feedback. Python + JavaScript web app.
- **ASR backends:** (1) Groq Whisper API — cloud, (2) NVIDIA NeMo Conformer-CTC (MostafaAhmed98/Conformer-CTC-Arabic-ASR) — local. Claims 60% speed improvement with local model.
- **Demo:** YouTube demo at https://youtu.be/QQmGZw-mYtY
- **Verdict ✅:** Shows production-ready architecture for real-time word alignment + error detection — directly applicable to Sakīna's feedback loop.

### 3c. Tadabur Dataset + Whisper Model
- **URL:** https://github.com/fherran/tadabur
- **Stars:** 161
- **Dataset:** https://huggingface.co/datasets/FaisaI/tadabur — 1,400+ hours, 600+ reciters, word-level timestamps
- **Model:** https://huggingface.co/FaisaI/tadabur-Whisper-Small — Whisper Small fine-tuned on Tadabur, ready to use
- **License:** CC BY-NC 4.0 (research/education only)
- **Verdict ✅:** Best dataset + fine-tuned model combo for Quran-specific ASR training.

### 3d. tarteel-ai/whisper-base-ar-quran (HuggingFace)
- **URL:** https://huggingface.co/tarteel-ai/whisper-base-ar-quran
- **Faster-Whisper variant:** https://huggingface.co/OdyAsh/faster-whisper-base-ar-quran
- **What it does:** Whisper-base fine-tuned specifically on Quranic Arabic recitations by Tarteel AI.
- **API status:** No HuggingFace Inference API deployment — must self-host or convert to ONNX/CTranslate2.
- **Verdict ✅:** The model we want. Use `faster-whisper` variant for low-latency mobile inference or convert to sherpa-onnx format.

---

## 4. Flutter Microphone + Streaming Audio Packages

| Package | Last Updated | What it does | Verdict |
|---------|-------------|--------------|---------|
| `sound_stream` | Sep 2025 | Streams mic PCM to STT, designed for STT/TTS pipelines | ✅ Best fit for Sakīna |
| `mic_stream_recorder` | Jun 11, 2025 | Mic recording + real-time amplitude monitoring | ✅ Active |
| `mic_stream` | Mar 2025 | Raw PCM stream `Stream<Uint8List>` from mic | ✅ Widely used |
| `sherpa_onnx` (built-in) | Jun 2026 | Has its own mic capture for streaming ASR examples | ✅ Use with sherpa-onnx |

- **pub.dev:** https://pub.dev/packages/sound_stream  
- **pub.dev:** https://pub.dev/packages/mic_stream  
- **Recommendation:** Use `sherpa_onnx` package's built-in audio pipeline for streaming + feed to on-device model. Fall back to `sound_stream` for cloud API path.

---

## 5. Open API Endpoints for Arabic Speech Recognition

### 5a. Groq Whisper API ⭐ BEST FREE OPTION
- **URL:** https://groq.com
- **Models:** Whisper Large v3 ($0.111/hr) | Whisper Large v3 Turbo ($0.04/hr)
- **Speed:** 216–228× real-time (1 hour of audio → ~15 seconds)
- **Free tier:** 2,000 RPD (requests per day), no credit card required
- **Arabic:** Whisper supports Arabic natively; for Quran use, pair with Tarteel model or prompt engineering
- **Verdict ✅:** Best latency + free tier for prototyping. Use for cloud fallback in Sakīna when on-device model is insufficient.

### 5b. VOSK Offline API
- **URL:** https://alphacephei.com/vosk / https://github.com/alphacep/vosk-api
- **Arabic model:** ~50 MB, fully offline, zero-latency streaming
- **Languages:** 20+ including Arabic
- **Cost:** Free, open-source (Apache 2.0)
- **Flutter binding:** Not official, but Dart/C++ FFI possible via sherpa-onnx-style bridge
- **Verdict ⚠️:** Good for ultra-low-resource offline, but Quran-specific accuracy will be lower than Tarteel/Whisper fine-tunes.

### 5c. Cohere Transcribe (New March 2026)
- **Coverage:** Arabic + 13 other languages, 2B parameter model, consumer-GPU deployable
- **Source:** https://techcrunch.com/2026/03/26/cohere-launches-an-open-source-voice-model-specifically-for-transcription/
- **Verdict ⚠️:** Too new to have Quran-specific fine-tunes, but worth watching.

---

## Architecture Recommendation for Sakīna

```
Flutter App
  ├── mic_stream / sherpa_onnx audio capture
  ├── [On-device] sherpa-onnx + tarteel-ai/whisper-base-ar-quran (ONNX)
  │     → sub-second latency, offline, ~150 MB model
  └── [Cloud fallback] Groq Whisper Large v3 Turbo API
        → 216× real-time, $0.04/hr, free tier 2000 RPD

Verse matching layer (from offline-tarteel):
  → CTC decode → fuzzy-match Quran verse corpus
  → Word-level alignment (from yayaiu6 system)
```

---

## Key Links Summary

| Item | URL |
|------|-----|
| CrisperWeaver (Flutter+Whisper) | https://github.com/CrispStrobe/CrisperWeaver |
| sherpa-onnx repo | https://github.com/k2-fsa/sherpa-onnx |
| sherpa-onnx Flutter streaming example | https://github.com/k2-fsa/sherpa-onnx/tree/master/flutter-examples/streaming_asr |
| sherpa_onnx pub.dev | https://pub.dev/packages/sherpa_onnx |
| offline-tarteel (BEST Quran ASR demo) | https://github.com/yazinsai/offline-tarteel |
| offline-tarteel live demo | https://offline-tarteel.whhite.com |
| yayaiu6 real-time tracker | https://github.com/yayaiu6/Real-Time-Quran-recitation-tracker-System |
| Tadabur dataset | https://huggingface.co/datasets/FaisaI/tadabur |
| Tadabur-Whisper-Small model | https://huggingface.co/FaisaI/tadabur-Whisper-Small |
| tarteel-ai/whisper-base-ar-quran | https://huggingface.co/tarteel-ai/whisper-base-ar-quran |
| faster-whisper Quran variant | https://huggingface.co/OdyAsh/faster-whisper-base-ar-quran |
| sound_stream pub.dev | https://pub.dev/packages/sound_stream |
| mic_stream pub.dev | https://pub.dev/packages/mic_stream |
| Groq Whisper API | https://groq.com |
| VOSK Arabic | https://alphacephei.com/vosk/models |
