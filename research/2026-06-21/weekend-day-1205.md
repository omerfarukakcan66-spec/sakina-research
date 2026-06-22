# Sakīna Research — Weekend Day Session
**Date:** 2026-06-21 · **Time:** 12:05 UTC  
**Focus:** Real-time Arabic ASR + Flutter — working code for TODAY

---

## 1. Flutter Repos with Real-Time Arabic ASR (Updated 2025-2026)

### A. k2-fsa/sherpa-onnx — ⭐ 13,100
- **GitHub:** https://github.com/k2-fsa/sherpa-onnx
- **Flutter streaming example:** https://github.com/k2-fsa/sherpa-onnx/tree/master/flutter-examples/streaming_asr
- **Last release:** June 18, 2026 (`asr-models-qnn-2`)
- **What it does:** On-device STT/TTS/diarization/VAD using ONNX Runtime — no internet required. Runs on Android, iOS, Windows, macOS, Linux.
- **Arabic specifics:** Whisper multilingual models (99 languages incl. Arabic) supported **offline (non-streaming)**. Streaming models are zipformer-based (Chinese/English/Korean/French). For Quran use, load `whisper-large-v3` via the offline ASR path.
- **Colab for Whisper:** https://github.com/k2-fsa/colab/blob/master/sherpa-onnx/sherpa_onnx_whisper_models.ipynb
- **pub.dev package:** `sherpa_onnx` v1.13.3 — published mid-June 2026, 113 likes, 150 pub points, 23.1k downloads.
- **Verdict:** **Best on-device option.** Active, huge community. Use offline Whisper path for Arabic/Quran.

### B. CrispStrobe/CrisperWeaver — ⭐ 21
- **GitHub:** https://github.com/CrispStrobe/CrisperWeaver
- **Last commit:** June 12, 2026 (v0.7.9)
- **Flutter version:** 3.44
- **What it does:** Offline Flutter app for live mic transcription + TTS. Uses 10-second sliding window with 3-second step intervals for real-time feel. Supports 40+ ASR model families.
- **Arabic specifics:** OmniASR (1,600+ languages), Whisper (99 languages). No dedicated Arabic model but both cover `ar`.
- **Verdict:** Low-star but cutting-edge Flutter implementation. Good reference for the sliding-window streaming pattern.

---

## 2. sherpa-onnx Flutter Streaming ASR Example (2025-2026)

- **Direct link:** https://github.com/k2-fsa/sherpa-onnx/tree/master/flutter-examples/streaming_asr
- **Key file:** `lib/streaming_asr.dart` — https://github.com/k2-fsa/sherpa-onnx/blob/master/flutter-examples/streaming_asr/lib/streaming_asr.dart
- **pub.dev:** https://pub.dev/packages/sherpa_onnx — v1.13.3, Apache-2.0
- **How it works:** Downloads a streaming zipformer model, places it in assets, runs real-time decoding via microphone on all platforms.
- **Arabic workaround:** No Arabic streaming model exists in sherpa-onnx yet. Recommended approach: use `SherpaOnnxOfflineRecognizer` with `whisper-large-v3` model chunked into 30-second windows from the mic buffer.
- **Pre-built APK:** Available on the releases page for testing without building.

---

## 3. Quran ASR — Working Models & Datasets (2025-2026)

### A. KheemP/whisper-base-quran-lora — **Best Ready-to-Use Model**
- **HuggingFace:** https://huggingface.co/KheemP/whisper-base-quran-lora
- **Architecture:** Whisper-base + LoRA fine-tune, diacritic-sensitive
- **Test WER:** ~5.98% — excellent for Quran recitation
- **Usage:** Load via `transformers` library or Inference API. Small enough to run cost-effectively.
- **Status:** Available on HuggingFace Hub (Inference API may be enabled).

### B. Nuwaisir/Quran_speech_recognizer
- **HuggingFace:** https://huggingface.co/Nuwaisir/Quran_speech_recognizer
- **Architecture:** wav2vec2-large-xlsr-53-Arabic fine-tuned on Quran ASR challenge data
- **Demo:** Run `run_ui.ipynb` — all-in-one Colab notebook
- **Accuracy:** Competitive with challenge leaderboard results.

### C. Buraaq/quran-audio-text-dataset — **NeurIPS 2025 Dataset**
- **HuggingFace:** https://huggingface.co/datasets/Buraaq/quran-audio-text-dataset
- **Scale:** 264,509 MP3 files — 187,080 verse-level + 77,429 word-level
- **Reciters:** 30 distinct reciters covering diverse tajweed styles
- **Use case:** Fine-tune Whisper on this dataset for a production Quran ASR model. Also enables tajweed detection.
- **Paper:** Quran-MD @ Muslims in ML Workshop, NeurIPS 2025 — https://arxiv.org/html/2601.17880v1

### D. Research Baseline (2025)
- **MDPI paper (2025):** Whisper fine-tuned for Quran — CER 0.142 (≈85% accuracy) — https://www.mdpi.com/2076-3417/15/17/9521
- **Arabic Pronunciation Benchmark (June 2026):** Quranic recitation as benchmark case — https://arxiv.org/pdf/2506.07722

---

## 4. Flutter Microphone + Streaming Audio Packages (Active 2025-2026)

| Package | Version | Last Published | Likes | Downloads/wk | License | Best For |
|---------|---------|---------------|-------|-------------|---------|---------|
| **flutter_sound** | 9.30.0 | 6 months ago | 1,640 | 75k | MPL-2.0 | `record_to_stream` PCM pipeline |
| **sound_stream** | 0.4.2 | 8 months ago | 115 | — | GPL-3.0 | Simple mic→network streaming |
| **flutter_audio_capture** | 1.1.12 | 5 months ago | 33 | — | — | Low-level buffer callbacks |
| **mic_stream** | 0.7.2 | 15 months ago | 99 | 984 | — | Raw `Stream<Uint8List>` |

### Recommended: flutter_sound
- **pub.dev:** https://pub.dev/packages/flutter_sound
- **GitHub:** https://github.com/dooboolab-community/flutter_sound
- **Key API for Quran app:**
  ```dart
  // Record PCM Int16 stream directly to Dart stream
  var recordingDataController = StreamController<Food>();
  await recorder.startRecorder(
    toStream: recordingDataController.sink,
    codec: Codec.pcm16,
    numChannels: 1,
    sampleRate: 16000,
  );
  recordingDataController.stream.listen((buffer) {
    // Send buffer chunks to ASR (Groq API or on-device sherpa-onnx)
  });
  ```
- Supports Android, iOS, Web, Linux, macOS, Windows.
- MPL-2.0 = can use in proprietary app if you don't modify flutter_sound itself.

---

## 5. Open API Endpoints for Arabic Speech Recognition

### A. Groq Whisper API — **Best Free Option**
- **Endpoint:** `POST https://api.groq.com/openai/v1/audio/transcriptions`
- **Models:** `whisper-large-v3`, `whisper-large-v3-turbo`
- **Free tier:** 2,000 requests/day · 7,200 audio-seconds/hour (~2 hrs audio/clock-hour)
- **No credit card required** for free tier
- **Arabic:** Pass `language: "ar"` — improves accuracy and latency
- **Speed:** 164× real-time factor (Whisper-large-v3 on Groq LPU)
- **Pricing (paid):** whisper-large-v3 = $0.111/hr audio · whisper-large-v3-turbo = $0.04/hr audio
- **Docs:** https://console.groq.com/docs/speech-to-text

```dart
// Flutter integration pattern
final request = http.MultipartRequest(
  'POST',
  Uri.parse('https://api.groq.com/openai/v1/audio/transcriptions'),
);
request.headers['Authorization'] = 'Bearer $groqApiKey';
request.fields['model'] = 'whisper-large-v3-turbo';
request.fields['language'] = 'ar';
request.files.add(http.MultipartFile.fromBytes('file', audioBytes, filename: 'audio.wav'));
final response = await request.send();
```

### B. OpenAI Whisper API
- **Endpoint:** `POST https://api.openai.com/v1/audio/transcriptions`
- **Model:** `whisper-1`
- **Pricing:** $0.006/minute (~$0.36/hour)
- **No free tier**, but cheap at low volume
- **Arabic:** Supported, same `language: "ar"` param

### C. HuggingFace Inference API (Free)
- Endpoint: `https://api-inference.huggingface.co/models/KheemP/whisper-base-quran-lora`
- Free for rate-limited inference on hosted models
- **Best for:** Testing Quran-specific LoRA model without self-hosting

---

## Summary & Recommendations for Sakīna

### Fastest Path to Working Prototype Today

1. **Audio capture:** Add `flutter_sound: ^9.30.0` to `pubspec.yaml` → use `record_to_stream` for PCM16 at 16kHz mono
2. **ASR (cloud, immediate):** Send audio chunks to **Groq Whisper API** with `language: "ar"` — free tier covers development
3. **ASR (on-device, offline):** Integrate `sherpa_onnx: ^1.13.3` with downloaded `whisper-large-v3` ONNX weights — works offline, no API costs
4. **Quran-specific accuracy:** Fine-tune `whisper-base-quran-lora` (WER 5.98%) on Buraaq dataset for tajweed-aware recognition
5. **Reference Flutter app:** Study CrisperWeaver's sliding-window streaming pattern for real-time UX

### Architecture Decision
```
Mic → flutter_sound (PCM16, 16kHz) → 
  [Dev/Online]: Groq Whisper API → transcription
  [Prod/Offline]: sherpa_onnx Whisper → on-device transcription
  → Display with Quran text highlighting
```

---

*Sources verified: GitHub, pub.dev, HuggingFace, Groq docs — all links checked 2026-06-21*
