# Weekend Day — Implementation Focus (2026-06-21 06:03 UTC)

> Round 6 of implementation research. Previous rounds (June 20) established the core offline stack:
> `sherpa-onnx` + `record` + Moonshine Arabic model. This round targets NEW angles not yet covered:
> FunASR self-hosted endpoint, Tarteel-to-ONNX conversion path, Soniox streaming API, and
> word-by-word Quran ASR. Only 2025-2026 sources.

---

## 1. Flutter Real-Time Arabic ASR — GitHub Repos

### 1a. CrisperWeaver — On-Device Multilingual Flutter ASR
- **GitHub:** https://github.com/CrispStrobe/CrisperWeaver
- **Last commit:** June 12, 2026 (v0.7.9)
- **Stars:** 21 (niche but very active)
- **License:** AGPL-3.0
- **What it does:** Full Flutter app (89% Dart) wrapping `CrispASR` C++ engine via FFI.
  Bundles 40+ ASR families: Whisper, Qwen3-ASR (52 langs incl. Arabic), OmniASR (1600+ langs),
  Gemma4-E2B (140+ langs), Parakeet, Voxtral, Cohere Transcribe, Canary 1B v2.
  Also TTS with Arabic variant "Lahgtna (Arabic)" via Chatterbox family.
  Includes offline model browser + download queue UX. Runs on Android, iOS, macOS, Linux, Windows.
- **Arabic path:** Use Qwen3-ASR 1.7B (52 langs) or OmniASR model via the model browser.
  No dedicated Quran fine-tune yet, but the engine accepts custom GGML models.
- **Sakīna relevance:** Reference architecture for offline/model-swap UX. AGPL means study it,
  don't ship derivative without open-sourcing. CrispASR backend is MIT (327 ⭐) — usable directly.

### 1b. modelscope/FunASR — Industrial Streaming ASR with OpenAI-Compatible Endpoint
- **GitHub:** https://github.com/modelscope/FunASR
- **Last commit:** June 20, 2026 (literally yesterday — v1.3.11)
- **Stars:** 18,400
- **License:** MIT
- **What it does:** Industrial-grade ASR toolkit. 170× real-time, 50+ languages, streaming WebSocket
  server, speaker diarization, VAD. **Key new capability (May 2026):** OpenAI-compatible REST endpoint
  (`funasr-server --device cuda → localhost:8000/v1/audio/transcriptions`) that accepts the same
  JSON format as OpenAI Whisper API. Self-hosted only — no cloud API. Docker image available.
- **Arabic support:** Fun-ASR-Nano-2512 (Dec 2025) covers 31 languages. "50+ languages" claim
  in the main repo includes Arabic but explicit Arabic WER benchmarks not published in README.
- **Sakīna path:** Run on a $5/mo VPS (GPU optional — CPU mode works for <4 concurrent users).
  Flutter calls `http://your-vps:8000/v1/audio/transcriptions` with WAV blob — identical to Groq.
  WebSocket path (`runtime/python/websocket/`) enables real-time token streaming.
- **Warning:** Arabic accuracy is unverified on Quranic text. Benchmark against Tarteel baseline
  (5.75% WER) before relying on this for verse matching.
- **Setup:** `docker run -p 8000:8000 modelscope/funasr:latest` — working in < 5 minutes.

---

## 2. sherpa-onnx Flutter Streaming Example — Updated 2025-2026

- **GitHub:** https://github.com/k2-fsa/sherpa-onnx
- **Directory:** `flutter-examples/streaming_asr/`
- **Stars:** 13,100
- **Last release:** `asr-models-qnn-2` — June 18, 2026
- **pub.dev package:** `sherpa_onnx` v1.13.3
- **What the Flutter example does:** `record` package → PCM16 stream → `OnlineRecognizer` →
  real-time token callbacks. Ships with Chinese-English Zipformer model by default. Swap
  `online_model.dart` for any Arabic model. Runs fully offline on Android + iOS.
- **2026 Arabic model options for sherpa-onnx:**
  - `sherpa-onnx-moonshine-base-ar-quantized-2026-02-27` (Feb 27, 2026) — 5.63% WER, 58M params ← RECOMMENDED
  - **NEW path (not in previous runs):** Export Tarteel fine-tunes to ONNX using
    `scripts/whisper/export-onnx.py` in the sherpa-onnx repo. Process:
    1. `pip install openai-whisper onnxruntime onnx`
    2. `python export-onnx.py --model tarteel-ai/whisper-base-ar-quran`
    3. Copy `.onnx` output → Flutter app assets
    This converts Tarteel's 5.75% WER Quran fine-tune to a fully offline, on-device model
    usable inside the existing `streaming_asr` Flutter example. **Unlocks the best of both worlds:
    Quran-specific accuracy + offline on-device inference.**
- **Streaming note:** Whisper models (offline ASR) in sherpa-onnx use the `OfflineRecognizer`
  API with VAD chunking — not a true streaming decoder. The Moonshine model is the best option
  for low-latency real-time via `OnlineRecognizer`.

---

## 3. Quran ASR Demos and Colab Notebooks — 2025-2026

### 3a. Nuwaisir/Quran_speech_recognizer (HuggingFace)
- **URL:** https://huggingface.co/Nuwaisir/Quran_speech_recognizer
- **Base model:** `elgeish/wav2vec2-large-xlsr-53-arabic` fine-tuned on Quran ASR Challenge data
- **What it does:** Records 5 seconds of recitation → transcribes to Arabic text → matches
  against the 30th juzz (Surah 78–114) using edit distance → returns most probable ayah.
- **Demo:** Interactive Colab notebook (`run_ui.ipynb`) — run all cells, last cell records mic.
- **Coverage:** 30th juzz only (Surah 78–114). Not full Quran.
- **Sakīna relevance:** Proof-of-concept for ayah matching via edit distance on top of ASR output.
  The matching logic (ASR text → edit distance → ranked ayah candidates) is directly reusable.

### 3b. HamzaSidhu786/wav2vec2-base-word-by-word-quran-asr (HuggingFace)
- **URL:** https://huggingface.co/HamzaSidhu786/wav2vec2-base-word-by-word-quran-asr
- **WER:** 7.90% (training loss: 0.0415)
- **Input:** WAV, 16kHz sample rate
- **What it does:** Word-by-word Quran ASR — recognizes individual words rather than full ayahs.
  This is architecturally different from sentence-level ASR: it outputs one word at a time,
  enabling word-level tajweed feedback ("you mispronounced كَلِمَة" not just "verse X has errors").
- **Sakīna relevance:** The **word-level feedback loop** is Sakīna's core differentiator vs
  Tarteel. This model is the closest public implementation of that feature. No live demo found
  (no HuggingFace Space linked) — but callable via HF Inference API.

### 3c. MDPI 2025 Paper — Enhanced Neural Quranic ASR via Large Audio Model
- **URL:** https://www.mdpi.com/2076-3417/15/17/9521
- **What it covers:** Fine-tunes a "Large Audio Model" (likely Whisper-large or similar) on
  Quranic recitation. Average CER of 14.2% on cross-sheikh test set using Whisper baseline.
- **Sakīna relevance:** Confirms Whisper-class models achieve ~85% character accuracy on unseen
  reciters without fine-tuning. Fine-tuning on Quran data is the necessary next step.

---

## 4. Flutter Microphone + Streaming Audio — Active 2025-2026 Packages

| Package | Version | Last Published | Platform | Stream Type | Notes |
|---------|---------|---------------|----------|------------|-------|
| `record` | v7.1.0 | June 2026 | Android/iOS/Desktop | `Stream<Uint8List>` PCM16 | **RECOMMENDED** — 717k downloads, 160 pub points, pipes directly to sherpa-onnx |
| `mic_stream` | 0.7.2 | **15 months ago** (Mar 2025) | Android/iOS/macOS | `Stream<Uint8List>` raw bytes | 99 likes, GPL-3.0 — **CONCERN**: stable version is stale; 0.7.3-dev exists in prerelease |
| `sound_stream` | latest | Sep 2025 | Android/iOS | `Stream<Uint8List>` | Simpler API; last update Sep 2025 — less actively maintained than `record` |
| `audio_streamer` | — | 2025 | Android/iOS | `Stream<List<double>>` float | Returns float not int; extra conversion step needed for sherpa-onnx |

**Recommendation:** Use `record` v7.1.0 (established in Round 4). `mic_stream` is the runner-up
but its 15-month-old stable version is a risk signal — the prerelease 0.7.3-dev is fine for
internal prototyping but shouldn't go to production without testing.

---

## 5. Open API Endpoints for Arabic Speech Recognition — Free or Cheap

| Provider | Price (streaming) | Free Tier | Arabic Quality | Quran Diacritics | Notes |
|----------|-----------------|-----------|---------------|-----------------|-------|
| **Groq** (Whisper L v3 Turbo) | Free tier | 2,000 req/day, 7,200 audio-sec/hr, no card | Good general Arabic | ❌ strips tashkeel | Best zero-cost prototype ← USE THIS FIRST |
| **Soniox** | $0.12/hr streaming | None found | "60+ languages", Arabic listed; ultra-low latency | ❓ unverified | WebSocket API, built-in turn detection; good for voice agents. Compare accuracy on Quran before committing |
| **Deepgram Nova-3** | $0.0077/min ($0.46/hr) | $200 free credits | Good; Jan 2026 Arabic launch; 40% lower WER vs competitors | ❌ strips tashkeel | Best general Arabic commercial option |
| **AssemblyAI** | $0.0025/min ($0.15/hr) | $50 free credits | Decent | ❌ strips tashkeel | Cheapest commercial option |
| **HF Inference API** (tarteel fine-tune) | Free (rate-limited) | Yes | Quran-specific, 5.75% WER | ✅ preserves tashkeel | Best free Quran-specific API; rate-limited but sufficient for prototype |
| **FunASR self-hosted** | ~$5/mo VPS (CPU) | N/A — self-hosted | 50+ langs; Arabic WER unknown | ❓ unknown | MIT license; OpenAI-compatible endpoint; need to benchmark on Quran |
| **ElevenLabs Scribe** | — | Some free | Listed as Arabic; "real-time coming soon" | ❌ | Skip for now — real-time not ready |

**Key new finding this run:** Soniox ($0.12/hr, WebSocket, turn detection) is a more credible
streaming-first option than Deepgram for voice-agent use cases, but its Arabic Quran accuracy
is unverified. If latency is the priority (live in-ear feedback during recitation), Soniox
deserves a benchmark slot alongside Groq and Deepgram.

---

## Action Items / What's New Vs. Previous Runs

1. **NEW: FunASR self-hosted OpenAI endpoint** — `docker run modelscope/funasr` → instant local
   API compatible with the same Flutter HTTP client used for Groq. Zero cloud cost. Test Arabic WER.

2. **NEW: Tarteel → ONNX export path** — `sherpa-onnx/scripts/whisper/export-onnx.py` converts
   `tarteel-ai/whisper-base-ar-quran` to `.onnx` for fully offline Flutter use. Closes the gap
   between "best Quran model" and "best offline model."

3. **NEW: Word-by-word Quran model** — `HamzaSidhu786/wav2vec2-base-word-by-word-quran-asr`
   (7.9% WER) is the closest public implementation of Sakīna's word-level feedback differentiator.

4. **CONFIRM: `record` v7.1.0 remains the Flutter mic package** — `mic_stream` is riskier
   (stale stable), `sound_stream` is secondary option. `record` is the only active 2026 choice.

5. **CONFIRM: Groq still the best day-1 cloud API** — free, fast, OpenAI-compatible. Add Soniox
   to benchmark queue for latency comparison.

---

## Sources

- https://github.com/k2-fsa/sherpa-onnx
- https://github.com/k2-fsa/sherpa-onnx/tree/master/flutter-examples/streaming_asr
- https://k2-fsa.github.io/sherpa/onnx/pretrained_models/whisper/export-onnx.html
- https://github.com/CrispStrobe/CrisperWeaver
- https://github.com/modelscope/FunASR
- https://huggingface.co/Nuwaisir/Quran_speech_recognizer
- https://huggingface.co/HamzaSidhu786/wav2vec2-base-word-by-word-quran-asr
- https://www.mdpi.com/2076-3417/15/17/9521
- https://pub.dev/packages/mic_stream
- https://pub.dev/packages/sound_stream
- https://soniox.com/pricing
- https://deepgram.com/pricing
- https://github.com/FunAudioLLM/Fun-ASR
