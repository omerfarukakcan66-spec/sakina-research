# Weekend Day Research — 2026-06-21 15:03 UTC (Round 9)
**Focus:** Working code usable TODAY — GitHub repos, sherpa-onnx Flutter, Quran ASR demos, mic packages, Arabic ASR APIs

---

## 1. GitHub Repos: Real-Time Arabic ASR + Flutter (2025–2026)

### 1a. CrisperWeaver — Flutter Offline ASR App (NEW)
- **GitHub:** https://github.com/CrispStrobe/CrisperWeaver
- **Stars:** 21
- **Last commit:** June 12, 2026 (v0.7.9)
- **License:** AGPL-3.0
- **What it does:** Cross-platform Flutter 3.44 app for fully offline audio transcription and synthesis. Powered by CrispASR (C++ ggml runtime). Supports 40+ open-weight ASR families including FunASR (31-language multilingual, with Arabic), Parakeet, Voxtral, Canary 1B v2. 20+ TTS families including "Lahgtna" (Arabic TTS). Drop files, paste URLs, or use mic. Audio never leaves device.
- **Sakīna relevance:** First Flutter ASR reference app found with confirmed Arabic support + active 2026 development. AGPL-3.0 means you cannot use the app code in a proprietary product directly, but the architecture (CrispASR engine behind Flutter UI) is a working blueprint. FunASR via CrispASR can run Arabic via on-device ggml — study `lib/` structure before implementing your own.
- **Warning:** AGPL-3.0 — code reuse requires open-sourcing your app OR getting a commercial license from CrispStrobe.

### 1b. FunASR — Industrial Arabic ASR Backend (Confirmed Active)
- **GitHub:** https://github.com/modelscope/FunASR
- **Stars:** 18,400+
- **Last commit:** June 20, 2026 (v1.3.11)
- **License:** MIT
- **What it does:** 50+ language ASR, speaker diarization, emotion detection, WebSocket streaming, OpenAI-compatible REST endpoint. `docker run modelscope/funasr:latest` → `localhost:8000/v1/audio/transcriptions`. 170× real-time speed on GPU.
- **Arabic WER on Quranic text:** Unverified. Benchmark before trusting.
- **Noted in:** Round 6 as self-hosted OpenAI-compatible backend. CrisperWeaver's engine (Round 9) confirms FunASR's Flutter + on-device path is production-viable.

---

## 2. sherpa-onnx Flutter Streaming ASR (2025–2026 Update)

### sherpa-onnx / flutter-examples/streaming_asr
- **GitHub:** https://github.com/k2-fsa/sherpa-onnx/tree/master/flutter-examples/streaming_asr
- **Stars (main repo):** 13,100
- **Latest release:** June 18, 2026 (`asr-models-qnn-2`)
- **pub.dev version:** `sherpa_onnx` v1.13.3 — published 6 days ago
- **License:** Apache-2.0
- **What the Flutter example does:** Real-time streaming speech recognition across Android, iOS, Windows, macOS, Linux. Uses `record` package for mic input → `OnlineRecognizer` from sherpa-onnx → live transcript display. Pre-built APKs available for testing.
- **2025–2026 additions:** Zipformer CTC with Whisper features, Zipformer transducer with Whisper features, Nemotron-3.5 streaming, Linux aarch64 Dart/Flutter, QNN (Qualcomm NPU) support.
- **Arabic model status:** ❌ NO Arabic streaming model in the model zoo (as of June 2026). Recent releases show Japanese Parakeet, English Nemotron/Parakeet, Albanian/Chinese TTS — no Arabic ASR model. Whisper in sherpa-onnx is **offline/batch only** (non-streaming). Path to Arabic: convert `tarteel-ai/whisper-base-ar-quran` to ONNX via `sherpa-onnx/scripts/whisper/export-onnx.py` and use `SherpaOnnxOfflineRecognizer`.
- **Code entry point:** [`streaming_asr.dart`](https://github.com/k2-fsa/sherpa-onnx/blob/master/flutter-examples/streaming_asr/lib/streaming_asr.dart)
- **HuggingFace discussion (fine-tuned Whisper + Flutter):** https://discuss.huggingface.co/t/using-a-fine-tuned-whisper-sherpa-onnx-model-to-create-a-android-app-with-flutter/134467

---

## 3. Working Demos / Colab / Notebooks for Quran ASR (2025–2026)

### 3a. Tadabur Dataset + Tadabur-Whisper-Small (NEW — Best Quran ASR Dataset 2026)
- **GitHub:** https://github.com/fherran/tadabur
- **Stars:** 162
- **Last updated:** April 22, 2026
- **License:** CC BY-NC 4.0 (research/educational use only)
- **Dataset:** 1,400+ hours of Quranic audio, 600+ reciters, word-level timestamp alignment, JSON metadata (surah/ayah/word position, Arabic text, audio filename). Available on HuggingFace: `FaisaI/tadabur`.
- **Fine-tuned model:** `Tadabur-Whisper-Small` on HuggingFace — Whisper-Small fine-tuned specifically on Tadabur data. Domain-adapted for prolonged phoneme durations, tajwīd rules, melodic articulation, and acoustic diversity of Quranic recitation.
- **Why it matters for Sakīna:** 600+ reciters means the model handles recitation style variation — the problem Tarteel's models struggled with on non-standard reciters. No WER benchmark published yet but training data diversity is 3–4× larger than any other public Quran ASR corpus.
- **Colab/Demo:** No public Colab linked in the repository. Use HF Inference API on `Tadabur-Whisper-Small` directly.

### 3b. MaddoggProduction/whisper-l-v3-turbo-quran-lora-dataset-mix (NEW)
- **HuggingFace:** https://huggingface.co/MaddoggProduction/whisper-l-v3-turbo-quran-lora-dataset-mix
- **Architecture:** Whisper Large v3 Turbo (4-layer decoder) + LoRA, fine-tuned on:
  - `tarteel-ai/everyayah`
  - `MohamedRashad/Quran-Recitations`
  - `ahishamm/QURANICWhisperDataset`
- **WER:** 12.69% on `ahishamm/QURANICWhisperDataset` test split
- **Training approach:** Curriculum learning (progressive Tajweed complexity) + data augmentation (gain, spectral masking, white noise)
- **Diacritics:** Trained with tashkeel — outputs fully diacritized Arabic text
- **vs. other Quran models:** Higher WER than KheemP/whisper-base-quran-lora (5.98%) but faster inference (Turbo architecture) and diacritized output. The diacritics output is uniquely useful for Tajweed feedback — you can diff diacritized expected vs. recognized text at the character level.
- **Usage:** Via HuggingFace Transformers: `pipeline("automatic-speech-recognition", model="MaddoggProduction/whisper-l-v3-turbo-quran-lora-dataset-mix")`

### 3c. Quran-MD Dataset (NeurIPS 2025 Muslims in ML)
- **HuggingFace:** https://huggingface.co/datasets/Buraaq/quran-audio-text-dataset
- **ArXiv:** https://arxiv.org/abs/2601.17880
- **OpenReview:** https://openreview.net/forum?id=NQ6er5I4PK
- **What it provides:** Verse-level audio from 32 reciters + word-level audio alignment + Arabic text + English translation + transliteration. Fine-grained multimodal dataset accepted at NeurIPS 2025.
- **264,509 MP3 files** across all reciters and verses
- **Colab:** No public Colab linked but dataset is directly loadable via `datasets.load_dataset("Buraaq/quran-audio-text-dataset")`.

### 3d. Nuwaisir/Quran_speech_recognizer (HuggingFace Model)
- **HuggingFace:** https://huggingface.co/Nuwaisir/Quran_speech_recognizer
- **Architecture:** wav2vec2 fine-tuned on Quran ASR challenge data
- **Access:** HF Inference API (free tier)
- **Colab:** Modeled in Google Colab using Whisper — achieving ~85% transcription accuracy (CER 0.142) in a Colab environment with no local GPU needed.

---

## 4. Flutter Microphone + Streaming Audio Packages (2025–2026 Active)

### 4a. `record` v7.1.0 — RECOMMENDED
- **pub.dev:** https://pub.dev/packages/record
- **Last published:** 11 days ago (June ~10, 2026)
- **Downloads:** 717,000+ total, 884 likes, 160 pub points
- **License:** BSD-3-Clause
- **Platforms:** Android, iOS, Windows, macOS, Linux, **Web**
- **Streaming:** ✅ `startStream()` returns `Stream<Uint8List>` in PCM16 — pipes directly into sherpa-onnx `OnlineRecognizer` or chunked HTTP upload to Groq
- **Used by sherpa-onnx:** Yes — the `flutter-examples/streaming_asr` example uses `record` as its audio layer
- **Why it wins:** The only actively maintained Flutter mic package with streaming PCM output in 2026. `mic_stream` is 15 months stale AND GPL-3.0.

### 4b. `mic_stream` v0.7.2 — NOT RECOMMENDED
- **pub.dev:** https://pub.dev/packages/mic_stream
- **Last published:** 15 months ago (March 2025)
- **License:** GPL-3.0 ⚠️ — license risk for proprietary app
- **Streaming:** ✅ `MicStream.microphone()` returns `Stream<Uint8List>` with configurable sample rate
- **Verdict:** Stale + GPL. Avoid for Sakīna.

### 4c. `mic_stream_recorder` v1.1.2 — NICHE ONLY
- **pub.dev:** https://pub.dev/packages/mic_stream_recorder
- **GitHub:** https://github.com/yasinarik/mic_stream_recorder
- **Last published:** 12 months ago (June 2025), 3 ⭐
- **Platforms:** iOS 12.0+, Android API 21+ only (no desktop/web)
- **Output:** M4A/AAC — not raw PCM. Must decode before passing to ASR models.
- **Verdict:** Too new, too narrow. For simultaneous record+playback edge cases only.

---

## 5. Open API Endpoints for Arabic Speech Recognition (Free/Cheap)

### 5a. Groq API — BEST CHOICE (Confirmed)
- **Endpoint:** `POST https://api.groq.com/openai/v1/audio/transcriptions`
- **Models:** `whisper-large-v3` ($0.111/hr), `whisper-large-v3-turbo` ($0.04/hr)
- **Arabic:** ✅ Pass `language=ar` — full Arabic support via Whisper multilingual
- **Free tier:** 2,000 requests/day + 7,200 audio-seconds/hour — no credit card
- **Speed:** 164–228× real-time (Whisper Large v3 / Turbo)
- **OpenAI-compatible:** Same `openai` Dart SDK, just change `baseUrl` to `https://api.groq.com/openai/v1`
- **10-second billing minimum** per request — batch short clips together
- **Docs:** https://console.groq.com/docs/speech-to-text

### 5b. OpenAI Whisper API — BACKUP
- **Price:** $0.006/min ($0.36/hr) — 9× more expensive than Groq
- **Arabic:** ✅ Full support
- **Streaming:** ❌ File upload only, not real-time
- **Free tier:** None (credit card required)

### 5c. ElevenLabs Scribe — PREMIUM / HIGH ACCURACY
- **Arabic WER:** 3.1% on FLEURS benchmark — best available for Arabic
- **Price:** Paid (no free tier)
- **Use case:** Post-processing / evaluation baseline only

### 5d. Voicegain Whisper API
- **Price:** $0.18/hr (50% less than OpenAI) with **15,000 free minutes** on signup
- **Arabic:** Via Whisper multilingual
- **Docs:** https://www.voicegain.ai/whisper

---

## Summary Table

| Category | Resource | Stars | Last Updated | Action |
|---|---|---|---|---|
| Flutter ASR App | CrisperWeaver | 21 | Jun 12, 2026 | Study arch (AGPL — don't copy code) |
| Streaming ASR Framework | sherpa-onnx Flutter | 13,100 | Jun 18, 2026 | Clone + use `streaming_asr` example |
| Quran Dataset | Tadabur (fherran) | 162 | Apr 22, 2026 | Use Tadabur-Whisper-Small via HF |
| Quran ASR Model | whisper-l-v3-turbo-quran | — | 2025 | Use for diacritized output feedback |
| Quran Dataset | Quran-MD (Buraaq) | — | NeurIPS 2025 | Load via `datasets` for fine-tuning |
| Flutter Mic Package | `record` v7.1.0 | — | Jun 10, 2026 | Only viable choice |
| Cloud ASR API | Groq Whisper | — | Active | 2,000 req/day free, `language=ar` |

---

## Implementation Path for Sakīna THIS Weekend

```
1. Flutter mic: record v7.1.0 → startStream() → Stream<Uint8List> PCM16
2. Cloud ASR: POST chunks to Groq /v1/audio/transcriptions (language=ar, free)
3. Verse match: sayedmahmoud266/quran-ai-transcriping FastAPI (local, Round 7)
4. On-device path: sherpa-onnx streaming_asr + export-onnx.py on tarteel-ai/whisper-base-ar-quran
5. Diacritized feedback: MaddoggProduction Turbo-Quran-LoRA for tashkeel-aware diff
6. Training data: Tadabur 1400h (CC-BY-NC) or Quran-MD NeurIPS 2025 (264k MP3s)
```

---

## Sources
- [CrisperWeaver GitHub](https://github.com/CrispStrobe/CrisperWeaver)
- [sherpa-onnx GitHub](https://github.com/k2-fsa/sherpa-onnx)
- [sherpa-onnx streaming_asr Flutter](https://github.com/k2-fsa/sherpa-onnx/tree/master/flutter-examples/streaming_asr)
- [sherpa_onnx pub.dev](https://pub.dev/packages/sherpa_onnx/changelog)
- [FaisaI/tadabur HuggingFace](https://huggingface.co/datasets/FaisaI/tadabur)
- [fherran/tadabur GitHub](https://github.com/fherran/tadabur)
- [MaddoggProduction Quran Whisper Turbo](https://huggingface.co/MaddoggProduction/whisper-l-v3-turbo-quran-lora-dataset-mix)
- [tarteel-ai/whisper-base-ar-quran](https://huggingface.co/tarteel-ai/whisper-base-ar-quran)
- [KheemP/whisper-base-quran-lora](https://huggingface.co/KheemP/whisper-base-quran-lora)
- [Nuwaisir/Quran_speech_recognizer](https://huggingface.co/Nuwaisir/Quran_speech_recognizer)
- [Buraaq/quran-audio-text-dataset](https://huggingface.co/datasets/Buraaq/quran-audio-text-dataset)
- [Quran-MD ArXiv](https://arxiv.org/abs/2601.17880)
- [record pub.dev](https://pub.dev/packages/record)
- [mic_stream pub.dev](https://pub.dev/packages/mic_stream)
- [mic_stream_recorder pub.dev](https://pub.dev/packages/mic_stream_recorder)
- [Groq Speech-to-Text Docs](https://console.groq.com/docs/speech-to-text)
- [Groq Whisper Large v3 Turbo](https://groq.com/blog/whisper-large-v3-turbo-now-available-on-groq-combining-speed-quality-for-speech-recognition/)
- [Groq Pricing](https://groq.com/pricing)
- [arabic-speech-recognition GitHub Topics](https://github.com/topics/arabic-speech-recognition)
- [FunASR GitHub](https://github.com/modelscope/FunASR)
