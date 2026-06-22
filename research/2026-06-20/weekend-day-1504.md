# Sakīna Research — Weekend Implementation Day
**Date:** 2026-06-20 15:04 UTC  
**Focus:** Working code for real-time Arabic ASR in Flutter — TODAY-ready sources

---

## 1. sherpa-onnx Flutter Streaming ASR ⭐ TOP PICK

**GitHub:** https://github.com/k2-fsa/sherpa-onnx/tree/master/flutter-examples/streaming_asr  
**Main repo:** https://github.com/k2-fsa/sherpa-onnx  
**pub.dev:** https://pub.dev/packages/sherpa_onnx  
**Stars:** 13,100  
**Last release:** June 18 2026 ("asr-models-qnn-2") — 182 total releases, extremely active  
**pub.dev version:** 1.13.3 (published ~5 days ago)  
**Downloads:** 23,100  

**What it does:**  
Fully offline on-device streaming ASR via ONNX Runtime — no internet required. The `flutter-examples/streaming_asr` folder is a complete, runnable Flutter app demonstrating real-time microphone → transcript using streaming Zipformer or Paraformer models.

**Platform support:** Android, iOS, Linux, macOS, Windows (all confirmed on pub.dev)

**Arabic / multilingual status:**  
- CHANGELOG v1.13.3 explicitly mentions "Arabic variants" in multilingual model expansion  
- Supports Whisper-based models (which include Arabic)  
- Models can be downloaded from https://github.com/k2-fsa/sherpa-onnx/releases/tag/asr-models  

**Setup in 3 steps:**  
1. Download a streaming Arabic/multilingual model from the releases page  
2. Place in `assets/`, update `pubspec.yaml`  
3. Edit `online_model.dart` to point to your model files  

**Verdict:** Best option for offline on-device Quran ASR in Flutter. No API keys, no latency from network hops, runs on Android/iOS.

---

## 2. `record` Flutter Package — Microphone + Streaming Audio ⭐ TOP PICK

**pub.dev:** https://pub.dev/packages/record  
**GitHub:** https://github.com/llfbandit/record  
**Version:** 7.1.0 (published 10 days ago — June ~10 2026)  
**Likes:** 884 | Pub points: 160 | Downloads: 717,000  
**License:** BSD-3-Clause  

**What it does:**  
Audio recorder from microphone to file OR stream, with multiple codecs. Critically for real-time ASR: supports `pcm16bits` encoder in stream mode, outputting raw PCM chunks you can pipe directly to sherpa-onnx or Groq API.

**Platform support:** Android, iOS, Linux, macOS, Web, Windows — full coverage  

**Key API for streaming ASR:**
```dart
final recorder = AudioRecorder();
final stream = await recorder.startStream(RecordConfig(
  encoder: AudioEncoder.pcm16bits,
  sampleRate: 16000,
  numChannels: 1,
));
stream.listen((chunk) {
  // send chunk to sherpa-onnx or Groq API
});
```

**Verdict:** The go-to Flutter audio streaming package in 2026. Actively maintained, massive install base, the only package with streaming + full platform support.

---

## 3. Quran-Specific ASR Models on Hugging Face

### 3a. MaddoggProduction/whisper-l-v3-turbo-quran-lora-dataset-mix
**HuggingFace:** https://huggingface.co/MaddoggProduction/whisper-l-v3-turbo-quran-lora-dataset-mix  
**Base model:** openai/whisper-large-v3-turbo  
**Fine-tuning:** LoRA on Quranic recitation dataset mix  
**WER:** 12.69% on ahishamm/QURANICWhisperDataset test set  
**Best for:** Low-latency live transcription (turbo = 4 decoder layers vs 32 in full v3)  
**Caution:** Higher hallucination risk on long verses — use temperature=0 and beam search  

### 3b. tarteel-ai/whisper-base-ar-quran
**HuggingFace:** https://huggingface.co/tarteel-ai/whisper-base-ar-quran  
**Live demo space:** https://huggingface.co/spaces/tarteel-ai/whisper-tiny-demo-quran  
**What it does:** Whisper-base fine-tuned specifically on Quran recitation audio  
**Best for:** Lightweight deployment (whisper-base = ~74M params, fast on mobile)  

### 3c. Habib-HF/tarbiyah-ai-whisper-medium-merged
**HuggingFace:** https://huggingface.co/Habib-HF/tarbiyah-ai-whisper-medium-merged  
**What it does:** Merged two Quran fine-tuned Whisper-medium models for better coverage  
**Best for:** Higher accuracy when latency is less critical  

### 3d. obadx/recitation-segmenter-v2
**HuggingFace:** https://huggingface.co/obadx/recitation-segmenter-v2  
**Dataset:** 850+ hours, ~300K annotated utterances  
**What it does:** NOT full ASR — specializes in verse boundary segmentation and pronunciation error detection using a custom Quran Phonetic Script (QPS). Useful as a post-processing layer to detect tajweed mistakes.

**No runnable Colab found for 2025-2026.** The tarteel-ai HuggingFace Space is the closest live demo.

---

## 4. Groq API — Arabic Whisper Cloud Endpoint

**Endpoint:** `https://api.groq.com/openai/v1/audio/transcriptions`  
**Models:** `whisper-large-v3` and `whisper-large-v3-turbo`  
**OpenAI-compatible:** Yes — drop-in replacement for `openai.audio.transcriptions.create()`  
**Arabic support:** Yes (Whisper multilingual)  
**Speed:** 164× real-time on Groq LPU hardware  

**Free tier limits (2026):**
| Metric | Limit |
|--------|-------|
| Requests/day | 2,000 |
| Audio seconds/hour | 7,200 (~2 hrs of audio/hr clock time) |
| Max file size | 25 MB (free), 100 MB (dev tier) |
| Min billing unit | 10 seconds per request |

**Flutter integration pattern:**
```dart
// Use http package — POST multipart form to Groq endpoint
// Authorization: Bearer $GROQ_API_KEY
// model: whisper-large-v3-turbo
// language: ar
// response_format: json
```

**Verdict:** Best cloud option. Free tier is sufficient for development and light production. 2,000 req/day = 2,000 Quran verse checks/day at no cost.

**Docs:** https://console.groq.com/docs/speech-to-text  
**Pricing overview:** https://groq.com/blog/whisper-large-v3-turbo-now-available-on-groq-combining-speed-recognition

---

## 5. CrisperWeaver — Flutter Offline ASR App (Reference Architecture)

**GitHub:** https://github.com/CrispStrobe/CrisperWeaver  
**Stars:** 21  
**Last commit:** June 12 2026 (v0.7.9)  
**License:** AGPL-3.0  

**What it does:**  
Cross-platform Flutter app for fully offline audio transcription supporting 40+ ASR model families: Whisper, FunASR (31 languages including Arabic), Parakeet, Qwen3-ASR. Good reference for how to wire ASR backends into a Flutter app.

**Arabic support:** FunASR backend includes Arabic; "Lahgtna Arabic" for TTS. Not Quran-optimized.  
**Caution:** AGPL-3.0 license — requires open-sourcing your app if you ship it commercially.  
**Use as:** Architectural reference, not a direct dependency.

---

## 6. VOSK — Offline Arabic ASR (Considered, Lower Priority)

**GitHub:** https://github.com/alphacep/vosk-flutter (75 stars)  
**Arabic model:** ~50 MB download from alphacephei.com/vosk/models  
**Streaming:** Yes, zero-latency streaming API  
**iOS support:** No (Android + Linux + Windows only)  
**Last meaningful update:** 2024 (stale in 2025-2026)  

**Verdict:** Skip for Sakīna — iOS support is a hard requirement and the project appears inactive.

---

## Implementation Recommendation for This Weekend

### Path A — Fully Offline (preferred for privacy + no API keys)
```
record (v7.1.0) → PCM stream → sherpa-onnx (1.13.3) → transcript
```
- Model: download a Whisper/Zipformer multilingual model from sherpa-onnx releases
- For Quran accuracy: fine-tune or replace encoder with tarteel-ai/whisper-base-ar-quran weights (export to ONNX)

### Path B — Cloud API (faster to prototype today)
```
record (v7.1.0) → audio chunks → Groq API (whisper-large-v3-turbo) → transcript
```
- Model on Groq: already trained on Arabic including Quranic text
- Free tier sufficient for dev — no setup beyond API key

### Best TODAY action:
1. `flutter pub add record` + `flutter pub add sherpa_onnx`  
2. Copy flutter-examples/streaming_asr as starting point  
3. Test with Groq API first (no model download/ONNX setup) to validate the pipeline  
4. Swap in sherpa-onnx offline once the UI/UX is working  

---

## Sources
- https://github.com/k2-fsa/sherpa-onnx
- https://github.com/k2-fsa/sherpa-onnx/tree/master/flutter-examples/streaming_asr
- https://pub.dev/packages/sherpa_onnx
- https://pub.dev/packages/record
- https://pub.dev/packages/mic_stream
- https://pub.dev/packages/sound_stream
- https://github.com/CrispStrobe/CrisperWeaver
- https://github.com/alphacep/vosk-flutter
- https://huggingface.co/MaddoggProduction/whisper-l-v3-turbo-quran-lora-dataset-mix
- https://huggingface.co/tarteel-ai/whisper-base-ar-quran
- https://huggingface.co/spaces/tarteel-ai/whisper-tiny-demo-quran
- https://huggingface.co/Habib-HF/tarbiyah-ai-whisper-medium-merged
- https://huggingface.co/obadx/recitation-segmenter-v2
- https://groq.com/blog/whisper-large-v3-turbo-now-available-on-groq-combining-speed-recognition
- https://groq.com/blog/largest-most-capable-asr-model-now-faster-on-groqcloud
- https://github.com/FunAudioLLM/Fun-ASR
- https://github.com/mbzuai-nlp/ArTST
