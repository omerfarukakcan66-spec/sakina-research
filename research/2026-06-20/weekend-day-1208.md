# Sakīna Research — Working Code for TODAY
**Date:** 2026-06-20 12:08 UTC | Weekend Day — Implementation Focus (Round 3)

> CRITICAL: Only 2025–2026 sources. All findings verified for last-commit/publish date.

---

## 1. GitHub Repos — Real-Time Arabic ASR with Flutter

### 1A. k2-fsa/sherpa-onnx — Flutter streaming_asr Example ⭐ PRIMARY
- **URL:** https://github.com/k2-fsa/sherpa-onnx/tree/master/flutter-examples/streaming_asr
- **Source file:** https://github.com/k2-fsa/sherpa-onnx/blob/master/flutter-examples/streaming_asr/lib/streaming_asr.dart
- **Last release:** v1.13.3, ~June 15, 2026 (published ~5 days ago)
- **Stars:** 13,100+
- **What it does:** Production-quality Flutter app wiring mic → ONNX ASR model → real-time tokens. `AudioRecorder.startStream()` captures PCM16 at 16 kHz, converts to Float32, passes each chunk to `OnlineRecognizer.acceptWaveform()` → `decode()` → result text. Three example directories: `streaming_asr`, `non_streaming_vad_asr`, `tts`. Uses `record` and `sherpa_onnx` pub.dev packages.
- **Why useful for Sakīna:** Copy-paste starting point. Swap in the Arabic Moonshine model (see §2B) and you have end-to-end offline Arabic ASR in Flutter. Zero API cost, works completely offline.

### 1B. yayaiu6/Real-Time-Quran-recitation-tracker-System
- **URL:** https://github.com/yayaiu6/Real-Time-Quran-recitation-tracker-System
- **Stars:** 121 (13 forks)
- **What it does:** Python/Flask web app with word-by-word Quran tracking. Two ASR backends: (1) Groq Whisper API (216× real-time, cloud), (2) NVIDIA NeMo Conformer-CTC (local). Levenshtein + Needleman-Wunsch alignment for word-level error detection. `hafs_smart_v8` JSON for verse matching.
- **Why useful:** Not Flutter, but the alignment algorithm (fuzzy-match transcription against all 6,236 verses) is directly portable to Dart. Mine this for Sakīna's verse-tracking layer.

### 1C. yazinsai/offline-tarteel (Research Document)
- **URL:** https://github.com/yazinsai/offline-tarteel/blob/main/RESEARCH-audio-to-verse.md
- **Last updated:** February 24, 2026
- **What it does:** Comprehensive engineering analysis of three Quran audio-to-verse approaches: (1) Audio Embedding + FAISS vector search, (2) Whisper fine-tuned ASR → text match, (3) Audio Fingerprinting. Recommends `tarteel-ai/whisper-base-ar-quran` (5.75% WER). Reports WhisperKit achieves 0.46s mean hypothesis latency with Arabic support. Documents WavLink (January 2026) for sub-100-dimension verse embeddings.
- **Why useful:** Most thorough up-to-date engineering analysis of Quran ASR approaches — required reading before architecture decisions.

---

## 2. sherpa-onnx Flutter Examples — 2025–2026

### 2A. sherpa_onnx pub.dev Package (Official)
- **pub.dev:** https://pub.dev/packages/sherpa_onnx
- **Version:** v1.13.3, published ~June 15, 2026
- **Downloads:** 23,000+; 113 likes, 150 pub points
- **2025–2026 changelog highlights:**
  - v1.13.3: Multilingual Nemotron-3.5 streaming ASR; streaming zipformer transducer for Qualcomm NPU; onnxruntime → 1.26.0 for iOS; fixed Flutter CI and Android app building
  - v1.12.28: C++ runtime + Kotlin/Java APIs for Moonshine v2 models
  - v1.12.36: Qwen3-ASR initialization fix
  - v1.12.35: Cohere Transcribe API additions
- **Platforms:** Android, iOS, Linux, macOS, Windows, HarmonyOS — fully offline, no internet required.

### 2B. Moonshine Arabic Model in sherpa-onnx ⭐ CRITICAL
- **Model name:** `sherpa-onnx-moonshine-base-ar-quantized-2026-02-27`
- **Docs:** https://k2-fsa.github.io/sherpa/onnx/moonshine/index.html
- **Released:** February 27, 2026
- **What it does:** Quantized Arabic Moonshine Base model (58M params) packaged for sherpa-onnx. Supports real-time/streaming recognition on Android and iOS. WER for Arabic Moonshine Base: 5.63%.
- **Why useful:** This is the missing piece — an Arabic model that runs offline on mobile via the existing Flutter sherpa-onnx pipeline. Plug into `streaming_asr` example → done.

---

## 3. Quran ASR Demos, Models & Datasets (2025–2026)

### 3A. tarteel-ai/whisper-base-ar-quran ⭐ BEST SMALL MODEL
- **URL:** https://huggingface.co/tarteel-ai/whisper-base-ar-quran
- **Live demo:** https://huggingface.co/spaces/tarteel-ai/demo-whisper-base-ar-quran
- **Last updated:** June 14, 2025 (safetensors variant added)
- **WER:** 5.75%
- **What it does:** Whisper-base (74M params) fine-tuned on Quranic Arabic audio, tajwīd-aware. Free inference via HuggingFace API (`POST /models/tarteel-ai/whisper-base-ar-quran`). Can be exported to ONNX for offline sherpa-onnx use.
- **Why useful:** Best publicly available small Quran ASR model. Use as cloud fallback while sherpa-onnx runs on-device.

### 3B. IqraEval Shared Task @ ArabicNLP 2025
- **URL:** https://huggingface.co/spaces/IqraEval/SharedTask_ArabicNLP2025
- **Paper:** https://aclanthology.org/2025.arabicnlp-sharedtasks.61/
- **What it does:** First open benchmark for Mispronunciation Detection & Diagnosis (MDD) in Quranic recitation. Multiple teams published models. One team adapted Whisper-large-v3 as a speech-to-phoneme model. QuranMB benchmark dataset included.
- **Why useful:** Provides the phoneme-level error detection layer Sakīna needs for tajweed feedback — beyond basic ASR.

### 3C. Tadabur Large-Scale Quran Dataset (2026)
- **arXiv:** https://arxiv.org/html/2604.18932
- **HuggingFace dataset:** https://huggingface.co/datasets/FaisaI/tadabur
- **Stars:** 161 (fherran/tadabur on GitHub)
- **What it does:** 1,400+ hours, 600+ reciters, word-level timestamps. `FaisaI/tadabur-Whisper-Small` ready for inference now. CC BY-NC 4.0. Whisper fine-tuned on Tadabur handles tajwīd phoneme durations across acoustic diversity.
- **Why useful:** The training dataset to use for fine-tuning a custom Sakīna model.

### 3D. Quran Pronunciation Error Detection Pipeline (Sep 2025)
- **URL:** https://arxiv.org/pdf/2509.00094
- **Published:** September 2025
- **What it does:** 850+ hour, 300,000+ annotated utterance pipeline using fine-tuned wav2vec2-BERT for segmentation. 0.16% average Phoneme Error Rate. 98% automated pipeline for high-quality Quranic ASR training data.
- **Why useful:** This is the technical ceiling Sakīna's tajweed pipeline should aim for.

---

## 4. Flutter Microphone + Streaming Audio Packages (2025–2026)

### 4A. `record` by llfbandit ⭐ TOP RECOMMENDATION
- **pub.dev:** https://pub.dev/packages/record
- **GitHub:** https://github.com/llfbandit/record
- **Stars:** 315
- **Version:** 7.1.0, published ~June 10, 2026
- **Platforms:** Android, iOS, macOS, Windows, Linux, Web
- **PCM streaming:** YES — `AudioRecorder.startStream()` → `Stream<Uint8List>` in `pcm16bits` encoding at 16 kHz
- **Why useful:** Already integrated in the sherpa-onnx Flutter `streaming_asr` example. v7.0.0+ requires Flutter 3.44 / Dart 3.12. This is the definitive mic-streaming package for Flutter in 2026.

### 4B. `flutter_recorder` by Marco Bavagnoli
- **pub.dev:** https://pub.dev/packages/flutter_recorder
- **Version:** 1.1.5, published ~May 2026
- **Platforms:** Android, iOS, macOS, Linux, Windows, Web (WASM-compatible)
- **PCM streaming:** YES — `startStreamingData()` → `uint8ListStream` of raw PCM audio
- **Backend:** miniaudio (low-level C), very low latency + silence detection
- **Why useful:** Alternative to `record` if you need WASM web support for a browser demo of Sakīna.

### 4C. `flutter_sound` by Canardoux
- **pub.dev:** https://pub.dev/packages/flutter_sound
- **Last update:** November 2025
- **PCM streaming:** YES — records to Dart Stream of PCM Float32 or PCM Int16
- **Why useful:** Feature-rich fallback if you need network audio relay alongside local ASR. Otherwise prefer `record`.

---

## 5. Arabic Speech Recognition APIs (Free or Cheap)

### 5A. Groq Whisper Large v3 Turbo ⭐ BEST FREE OPTION
- **API docs:** https://console.groq.com/docs/speech-text
- **Free tier:** 2,000 requests/day, 7,200 audio seconds/hour — no credit card required
- **Paid rate:** $0.04/hour (Whisper Large v3 Turbo); $0.111/hour (Whisper Large v3)
- **Speed:** 228× real-time — 1 hour of audio in ~16 seconds
- **Arabic support:** YES (Whisper Large v3 supports 99+ languages including Arabic)
- **Endpoint:** OpenAI-compatible `/openai/v1/audio/transcriptions` — swap between Groq and OpenAI with a single base URL change
- **Why useful:** 2 hours of daily Arabic recitation recognition at zero cost during development.

### 5B. Deepgram Nova-3 Arabic ⭐ MOST GENEROUS FREE CREDITS
- **URL:** https://deepgram.com/learn/nova-3-arabic-speech-to-text-production-grade-stt
- **Free tier:** $200 in credits (~750 hours) — no credit card required
- **Paid rate:** ~$0.46/hour for streaming
- **Arabic support:** YES — 17 regional dialect variants: `ar`, `ar-AE`, `ar-SA`, `ar-EG`, `ar-MA`, `ar-IQ`, and 11 more
- **Why useful:** $200 free credits is the most generous free tier. ~40% lower WER than competing systems on conversational Arabic. Dialect specificity (ar-SA, ar-EG) useful for targeting specific recitation styles.

### 5C. OpenAI Whisper API
- **Endpoint:** `POST https://api.openai.com/v1/audio/transcriptions`
- **Pricing:** $0.006/minute ($0.36/hour) — $5 one-time credit for new accounts
- **Arabic support:** YES
- **Why useful:** Most widely documented. At $0.006/min, 30 min/day of testing = $0.18/day — affordable, but Groq free tier is superior for development.

---

## Architecture Recommendation for Sakīna

**Offline path (zero cost, production):**
1. Audio: `record` v7.1.0 → `AudioRecorder.startStream()` at 16 kHz PCM16
2. ASR: `sherpa_onnx` v1.13.3 with `sherpa-onnx-moonshine-base-ar-quantized-2026-02-27`
3. Verse matching: Levenshtein distance against `hafs_smart_v8` JSON (port from yayaiu6/repo)
4. Starting code: `k2-fsa/sherpa-onnx/flutter-examples/streaming_asr/lib/streaming_asr.dart`

**Cloud fallback (better accuracy, free during development):**
- Groq Whisper Large v3 Turbo: `/openai/v1/audio/transcriptions` (2,000 free req/day)
- Or: Deepgram Nova-3 `language=ar` (start with $200 free credits)

**Pronunciation error detection (beyond ASR):**
- IqraEval 2025 models from `huggingface.co/spaces/IqraEval/SharedTask_ArabicNLP2025`
- Fine-tune `tarteel-ai/whisper-base-ar-quran` on Tadabur dataset for target recitation style

---

## Sources
- https://github.com/k2-fsa/sherpa-onnx
- https://github.com/k2-fsa/sherpa-onnx/tree/master/flutter-examples/streaming_asr
- https://pub.dev/packages/sherpa_onnx
- https://k2-fsa.github.io/sherpa/onnx/moonshine/index.html
- https://github.com/yayaiu6/Real-Time-Quran-recitation-tracker-System
- https://github.com/yazinsai/offline-tarteel/blob/main/RESEARCH-audio-to-verse.md
- https://huggingface.co/tarteel-ai/whisper-base-ar-quran
- https://huggingface.co/spaces/tarteel-ai/demo-whisper-base-ar-quran
- https://huggingface.co/tarteel-ai/whisper-tiny-ar-quran
- https://huggingface.co/spaces/IqraEval/SharedTask_ArabicNLP2025
- https://aclanthology.org/2025.arabicnlp-sharedtasks.61/
- https://arxiv.org/html/2604.18932
- https://huggingface.co/datasets/FaisaI/tadabur
- https://arxiv.org/pdf/2509.00094
- https://pub.dev/packages/record
- https://github.com/llfbandit/record
- https://pub.dev/packages/flutter_recorder
- https://pub.dev/packages/flutter_sound
- https://console.groq.com/docs/speech-text
- https://deepgram.com/learn/nova-3-arabic-speech-to-text-production-grade-stt
