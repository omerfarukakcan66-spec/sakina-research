# Sakīna Research — Weekend Day Implementation Focus
**Date:** 2026-06-20 07:20 UTC  
**Topic:** Working Code for Real-Time Arabic ASR in Flutter — Actionable Finds Only

---

## 1. GitHub Repos: Real-Time Arabic ASR + Flutter

### 1a. `k2-fsa/sherpa-onnx` ⭐ BEST BET FOR FLUTTER
- **GitHub:** https://github.com/k2-fsa/sherpa-onnx
- **Stars:** 13,100+
- **Last commit:** Active (1,934+ commits, 2026 active)
- **Flutter example:** `flutter-examples/streaming_asr/` — fully wired streaming pipeline
- **Arabic model (2026-04-01):** `sherpa-onnx-cohere-transcribe-14-lang-int8-2026-04-01`  
  Supports 14 languages including **MENA: Arabic**
- **What it does:** On-device offline streaming ASR via ONNX Runtime. Works on Android, iOS, macOS, Linux, Windows. Dart/Flutter package published to pub.dev (`sherpa_onnx`).
- **Why it matters for Sakīna:** Fully offline, no API cost, real-time word-by-word output, can be loaded with the Arabic model above. Swap the model file → same Flutter streaming code works.
- **Caveat:** Arabic model is multilingual (14-lang), not Quran-specific. Quran diacritics may drift.

### 1b. `CrispStrobe/CrisperWeaver`
- **GitHub:** https://github.com/CrispStrobe/CrisperWeaver
- **Stars:** 21
- **Last commit:** June 12, 2026 (v0.7.9)
- **What it does:** Flutter 3.44 on-device STT app using CrispASR (ggml/Whisper). 40+ open-weight ASR models, 20+ TTS families. Real-time streaming transcription. Cross-platform (Android, iOS, macOS, Linux, Windows, Web).
- **Arabic:** Arabic TTS via Chatterbox ("Lahgtna"). ASR language-agnostic — load any Whisper-compatible ggml model.
- **License:** AGPL-3.0
- **Why it matters:** Reference implementation for wiring microphone → ggml Whisper model in Flutter. Can load a Quran-fine-tuned Whisper ggml model.

### 1c. `sayedmahmoud266/quran-ai-transcriping` ⭐ QURAN-SPECIFIC
- **GitHub:** https://github.com/sayedmahmoud266/quran-ai-transcriping
- **Stars:** 25
- **Last commit:** October 2025
- **What it does:** Python FastAPI backend for Quran verse detection from audio. Uses `tarteel-ai/whisper-base-ar-quran`, constraint propagation for Ayah matching, backward gap filling, fuzzy match against all 6,236 verses with tashkeel. WER 5.75%.
- **Not Flutter** but can be self-hosted as a backend API Sakīna calls.
- **Streaming:** Not yet (roadmapped). Current: file upload → JSON with verse matches + timestamps.
- **Inputs:** MP3, WAV, M4A, FLAC; auto-converts to 16kHz mono.
- **Why it matters:** Production-quality Ayah matching logic we can steal or call as a service.

---

## 2. sherpa-onnx Flutter Streaming ASR — Technical Detail

**Repo path:** `k2-fsa/sherpa-onnx/flutter-examples/streaming_asr/`  
**Key file:** `lib/streaming_asr.dart`

**How it works:**
1. `record` package captures PCM audio from mic
2. Audio chunks fed to sherpa-onnx ONNX runtime via Dart FFI
3. Model returns partial transcription tokens in real time
4. UI updates word-by-word

**Arabic model to use:**  
`sherpa-onnx-cohere-transcribe-14-lang-int8-2026-04-01`  
Available at: https://github.com/k2-fsa/sherpa-onnx/releases/tag/asr-models  
Arabic is listed as `MENA: Arabic` in the 14-language roster.

**Integration path for Sakīna:**
```
pubspec.yaml → add sherpa_onnx: ^x.x.x
assets/ → drop in Arabic model files (.onnx + tokens.txt)
online_model.dart → configure model path
streaming_asr.dart → already handles microphone → tokens loop
```

---

## 3. Quran-Specific ASR: Models & Colab Demos

### 3a. `tarteel-ai/whisper-base-ar-quran` — THE FOUNDATION MODEL
- **HuggingFace:** https://huggingface.co/tarteel-ai/whisper-base-ar-quran
- **What it is:** Whisper-base fine-tuned by Tarteel AI specifically on Quranic Arabic
- **WER:** 5.75% (excellent for Quran domain)
- **Use via:** HuggingFace Inference API (free tier, ~100s req/hr) or self-host
- **Inference code:**
```python
from transformers import pipeline
pipe = pipeline("automatic-speech-recognition", model="tarteel-ai/whisper-base-ar-quran")
result = pipe("recitation.wav")
print(result["text"])  # Arabic text with diacritics
```
- **API endpoint (free):** `https://api-inference.huggingface.co/models/tarteel-ai/whisper-base-ar-quran`

### 3b. `KheemP/whisper-base-quran-lora`
- **HuggingFace:** https://huggingface.co/KheemP/whisper-base-quran-lora
- **What it is:** LoRA fine-tune ON TOP of tarteel-ai's model. Diacritic-sensitive (tashkeel preserved).
- **WER:** 5.98% — marginal vs base but diacritic accuracy better
- **When to use:** When Tajweed mark accuracy matters for Sakīna feedback engine

### 3c. `MaddoggProduction/whisper-l-v3-turbo-quran-lora-dataset-mix`
- **HuggingFace:** https://huggingface.co/MaddoggProduction/whisper-l-v3-turbo-quran-lora-dataset-mix
- **What it is:** Whisper-large-v3-turbo LoRA, trained on mixed Quran dataset
- **WER:** 12.69% on QURANICWhisperDataset test set
- **Speed:** Fast inference (turbo architecture)
- **Trade-off:** Higher WER than base-ar-quran but larger model = better generalization

### 3d. `Habib-HF/tarbiyah-ai-whisper-medium-merged`
- **HuggingFace:** https://huggingface.co/Habib-HF/tarbiyah-ai-whisper-medium-merged
- **What it is:** whisper-medium fine-tuned on diverse Qiraat styles (not just one reciter)
- **Strength:** Handles multiple recitation styles — important for user diversity in Sakīna
- **Use via:** transformers pipeline, ready for inference

### 3e. `OdyAsh/faster-whisper-base-ar-quran`
- **HuggingFace:** https://huggingface.co/OdyAsh/faster-whisper-base-ar-quran
- **What it is:** CTranslate2/faster-whisper conversion of tarteel model
- **Why it matters:** 4–8x faster CPU inference than standard Whisper — better for mobile-adjacent server or Raspberry Pi self-host

### 3f. Dataset: `Buraaq/quran-audio-text-dataset`
- **HuggingFace:** https://huggingface.co/datasets/Buraaq/quran-audio-text-dataset
- **What it is:** Paired audio-text dataset for Quran ASR training/evaluation
- **Use:** Fine-tuning or benchmarking your own model

---

## 4. Flutter Microphone + Streaming Audio Packages

### 4a. `record` (pub.dev) ⭐ RECOMMENDED
- **URL:** https://pub.dev/packages/record
- **Version:** 7.1.0
- **Last published:** 9 days ago (June 2026)
- **Pub points:** 160 | **Likes:** 884 | **Downloads:** 717k
- **Streaming support:** YES — `RecordStream` outputs `Stream<Uint8List>` in PCM16 or AAC
- **Platforms:** Android, iOS, Linux, macOS, Web, Windows (all 6)
- **Why it's the one:** Most maintained, highest adoption, streaming output ready to pipe to sherpa-onnx or Whisper

### 4b. `mic_stream` (pub.dev)
- **URL:** https://pub.dev/packages/mic_stream
- **Version:** 0.7.2
- **Last published:** 15 months ago (March 2025)
- **Pub points:** 130 | **Likes:** 99
- **Streaming support:** YES — `MicStream.microphone()` returns `Stream<Uint8List>` raw PCM
- **Platforms:** Android, iOS, macOS only (no Linux/Windows/Web)
- **Note:** Pre-release 0.7.3-dev exists. Less maintained than `record` but provides raw bytes without encoder overhead — marginally better for real-time streaming to ONNX models.

### 4c. `mic_stream_recorder` (pub.dev)
- **URL:** https://pub.dev/packages/mic_stream_recorder  
- **GitHub:** https://github.com/yasinarik/mic_stream_recorder
- **Version:** 1.1.2
- **Last published:** 12 months ago (June 2025)
- **Platforms:** iOS 12.0+, Android API 21+ only
- **Streaming:** Amplitude stream for monitoring, records to M4A file (not raw stream)
- **Verdict:** Not suitable for real-time ASR — output is file, not byte stream

---

## 5. Open API Endpoints for Arabic Speech Recognition

### 5a. OpenAI Whisper API ⭐ EASIEST TO START
- **Endpoint:** `POST https://api.openai.com/v1/audio/transcriptions`
- **Model:** `whisper-1` or `gpt-4o-mini-transcribe`
- **Arabic:** Fully supported (99+ languages, auto-detection)
- **Cost:**
  - `whisper-1`: $0.006/min
  - `gpt-4o-mini-transcribe`: $0.003/min (50% cheaper)
- **Quran accuracy:** Generic Arabic model — decent but not fine-tuned for Quran diacritics
- **Limitation:** File upload only (no streaming). Max 25MB. Latency: 1–5s per chunk.
- **Flutter integration:** `http` or `dio` package, multipart POST with audio file

### 5b. HuggingFace Inference API — Free Tier
- **Endpoint:** `https://api-inference.huggingface.co/models/tarteel-ai/whisper-base-ar-quran`
- **Cost:** FREE (rate-limited: ~100-300 req/hr on free tier)
- **Method:** POST with audio bytes in body, Authorization: Bearer HF_TOKEN
- **Response:** JSON with transcribed Arabic text
- **Limitation:** Cold start latency (30–60s first request), shared CPU, rate limits
- **Best for:** Development, low-volume production without cost
- **Upgrade path:** HuggingFace Inference Endpoints (dedicated GPU, ~$0.50/hr, scale-to-zero)

### 5c. Self-Host `quran-ai-transcriping` API
- **Source:** https://github.com/sayedmahmoud266/quran-ai-transcriping
- **Stack:** FastAPI + tarteel Whisper + PyQuran
- **Cost:** Server cost only (e.g., Fly.io free tier for low volume)
- **Benefit:** Full Ayah matching built in — not just transcription but verse ID + timestamps
- **Limitation:** No streaming, file upload only, Python backend required

### 5d. FunASR (via WebSocket server)
- **Repo:** https://github.com/FunAudioLLM/Fun-ASR
- **Arabic:** Supported among 31 languages
- **Streaming:** YES — vLLM Inference Engine with WebSocket real-time service (as of 2026-05)
- **Model:** Fun-ASR-Nano-2512 (Dec 2025) — low-latency, 31-lang
- **Self-host only** — no public free API
- **Flutter integration:** WebSocket connection to self-hosted server

---

## Implementation Recommendation for Sakīna

### Path A: Fully On-Device (Offline, No Cost)
```
Flutter mic (record v7) 
  → PCM16 stream 
  → sherpa-onnx (Arabic 14-lang model) 
  → partial transcription
  → local Quran verse matcher (dart, using quran package)
```
**Pros:** Offline, no latency, no API cost, works in airplane mode  
**Cons:** 14-lang model not Quran-fine-tuned, larger model bundle (~80MB)

### Path B: Cloud Whisper + Quran Matching (Best Accuracy)
```
Flutter mic (record v7) 
  → buffer 3–5s audio chunks 
  → POST to HuggingFace tarteel-ai/whisper-base-ar-quran (free) 
  → Arabic text with diacritics 
  → Flutter Quran verse matcher
```
**Pros:** Best Quran accuracy (WER 5.75%), diacritics preserved, free to start  
**Cons:** Network required, ~1-3s latency per chunk, rate-limited

### Path C: Hybrid (Recommended for Launch)
- On-device VAD (sherpa-onnx VAD model) to detect speech
- Buffer to cloud API (HuggingFace free → upgrade to OpenAI if rate-limited)
- Local verse matching for instant feedback, cloud for accuracy confirmation

---

## Next Steps
1. Clone `k2-fsa/sherpa-onnx/flutter-examples/streaming_asr/` and get it running
2. Download `sherpa-onnx-cohere-transcribe-14-lang-int8-2026-04-01` Arabic model
3. Test against 3–5 Quran verses to measure WER vs tarteel-ai/whisper-base-ar-quran
4. Self-host `quran-ai-transcriping` on Fly.io to get verse ID matching
5. Decide on Path A/B/C based on WER test results

---

*Sources:*
- https://github.com/k2-fsa/sherpa-onnx
- https://github.com/CrispStrobe/CrisperWeaver
- https://github.com/sayedmahmoud266/quran-ai-transcriping
- https://huggingface.co/tarteel-ai/whisper-base-ar-quran
- https://huggingface.co/KheemP/whisper-base-quran-lora
- https://huggingface.co/MaddoggProduction/whisper-l-v3-turbo-quran-lora-dataset-mix
- https://huggingface.co/Habib-HF/tarbiyah-ai-whisper-medium-merged
- https://huggingface.co/OdyAsh/faster-whisper-base-ar-quran
- https://huggingface.co/datasets/Buraaq/quran-audio-text-dataset
- https://pub.dev/packages/record
- https://pub.dev/packages/mic_stream
- https://pub.dev/packages/mic_stream_recorder
- https://github.com/FunAudioLLM/Fun-ASR
- https://github.com/ruzhila/voiceapi
- https://openai.com/api/ (Whisper API pricing)
- https://huggingface.co/docs/inference-providers/
