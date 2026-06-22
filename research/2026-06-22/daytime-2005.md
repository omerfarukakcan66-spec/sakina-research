# Research Report — 2026-06-22 Daytime 20:05 UTC (Round 18)

**Focus:** sherpa-onnx v1.13.3 Nemotron-3.5 Flutter integration status + New June 2026 tajweed papers + New Quran datasets

---

## Finding 1 — sherpa-onnx v1.13.3 (June 15, 2026): Nemotron-3.5 Multilingual Streaming ASR CONFIRMED LIVE in Flutter Package

### What Changed
`k2-fsa/sherpa-onnx` v1.13.3 released **June 15, 2026** is the first version with confirmed Nemotron-3.5 multilingual streaming ASR support (previously Round 17 was uncertain about integration status). The changelog explicitly lists "multilingual Nemotron-3.5 streaming ASR support" as a v1.13.3 feature. The pub.dev Flutter package `sherpa_onnx` 1.13.3 (published ~June 15, 2026) includes this support.

### Release Timeline (May–June 2026)
- v1.13.1 (May 8, 2026): NeMo transducer beam search bugfixes
- v1.13.2 (May 13, 2026): NeMo unified streaming 0.6b models added
- **v1.13.3 (June 15, 2026): Nemotron-3.5 multilingual streaming + iOS onnxruntime 1.26.0**

### Practical Integration
The Nemotron-3.5 multilingual variant requires a **6th encoder input tensor** `prompt_index` (int64, shape [batch]):
- `prompt_index = 101` → auto-detect language (USE THIS FOR ARABIC)
- Specific language codes exist for each locale (exact Arabic integer not publicly documented; use 101 for auto or consult model's `tokens.txt` / config)
- The prompt_index is fused into the encoder at export time — no extra MLP, just pass the tensor

### ONNX Model Available
- **`onnx-community/nemotron-3.5-asr-streaming-0.6b-onnx-int4`** (pre-converted, no export work needed)
  - Size: **0.67 GB** (INT4 quantized)
  - WER: **8.20%** (English LibriSpeech — 0.17% behind FP32 baseline 8.03%)
  - Latency: **0.56 seconds** end-to-end
  - **Arabic WER: UNPUBLISHED** — this is the critical benchmark gap
- Also: `FluidInference/Nemotron-3.5-ASR-Streaming-Multilingual-0.6b-CoreML` for iOS native

### Flutter Integration Path (Exact Steps)
```dart
// In pubspec.yaml
dependencies:
  sherpa_onnx: ^1.13.3  // pub.dev, June 15 2026
  record: ^7.1.0         // for mic streaming

// Model: download onnx-community/nemotron-3.5-asr-streaming-0.6b-onnx-int4
// Drop into flutter-examples/streaming_asr/ assets
// Configure with prompt_index=101 for Arabic auto-detect
```

### Independent Validation (arXiv:2604.14493, April 2026)
"Pushing the Limits of On-Device Streaming ASR: A Compact, High-Accuracy English Model for Low-Latency Inference" (Banfic et al., Apple/NVIDIA) benchmarked **50+ ASR configurations** (encoder-decoder, transducer, LLM-based) in batch/chunked/streaming modes and concluded: **NVIDIA Nemotron Speech Streaming is the strongest candidate for real-time streaming on resource-constrained hardware**. English-only benchmark but provides independent architectural confirmation for choosing Nemotron for Sakīna.

### Sakīna Action (URGENT)
1. `flutter pub add sherpa_onnx:1.13.3` + copy `flutter-examples/streaming_asr/`
2. Download `onnx-community/nemotron-3.5-asr-streaming-0.6b-onnx-int4` (0.67GB)
3. Set `prompt_index=101` in the NeMo transducer config
4. Test 20 Quran clips from `Buraaq/quran-audio-text-dataset`
5. If Arabic WER < 10% on Quran → this becomes primary on-device streaming model

---

## Finding 2 — arXiv:2503.23470 (March 2026): EfficientNet-B0 + Mel-Spectrogram Vision Approach Achieves 95–99% Accuracy on 3 Tajweed Rules — New Architecture Template

### Paper Details
**Title:** "Evaluation of the Pronunciation of Tajweed Rules Based on DNN as a Step Towards Interactive Recitation Learning"  
**Authors:** Dim Shaiakhmetov, Gulnaz Gimaletdinova, Selcuk Cankurt, Kadyrmamat Momunov  
**arXiv:** 2503.23470 (March 2026)  
**Status:** Submitted 2025, revised 2026 (v2 available at arxiv.org/html/2503.23470v2)

### Methodology — Different Architecture from Previous Research
Previous Sakīna research (Rounds 10–17) focuses on **CTC/transducer acoustic models** for tajweed detection. This paper uses a **completely different approach**:
1. Audio → **Normalized mel-spectrogram images**
2. Images → **EfficientNet-B0 + Squeeze-and-Excitation (SE) block** (image classifier, NOT ASR)
3. Output: 3-class tajweed rule classification

This is a vision-transformer-style approach using a lightweight CNN, not an audio encoder.

### Results (QDAT Dataset, 1,505 recordings)
| Tajweed Rule | Accuracy |
|---|---|
| Al-Mad (Separate Stretching) | **95.35%** |
| Ghunnah (Tight Noon) | **99.34%** |
| Ikhfaa (Hide) | **97.01%** |

"Learning curve analysis confirms the model's robustness and absence of overfitting."

### Dataset: QDAT
- **1,505 audio recordings** of Quran recitation (Surah Al-Maidah verse 109)
- **Correct + incorrect** recitations of the 3 tajweed rules
- Published 2021 IEEE ICALP, but used here for the first time with deep mel-spectrogram approach
- Covers the same rules as the LSTM approach (arXiv:2305.06429) but with dramatically higher accuracy

### Why This Matters for Sakīna
The **mel-spectrogram → EfficientNet-B0** architecture is:
- **Architecturally orthogonal** to the CTC-based whu-iasp approach (Round 12)
- **Much simpler to implement**: standard CV pipeline, not ASR pipeline
- **Faster to train**: EfficientNet-B0 is ~5.3M params, trains in hours on a single GPU
- **No phoneme labels needed**: learns directly from audio spectrograms

**Potential ensemble**: CTC-based detection (phoneme-level) + EfficientNet vision check (spectrogram-level) → vote for final tajweed verdict. Each architecture catches different error patterns.

### No Public Code/Model Found
No HuggingFace model or GitHub repository linked in the paper. Contact authors for code.

---

## Finding 3 — hetchyy/quranic-universal-ayahs: New Dataset — 174k Rows with Word + Letter + Waqf Timestamps, Community-Verified

### Dataset Details
**HuggingFace:** `hetchyy/quranic-universal-ayahs`  
**Creator:** Same `hetchyy` org as `Hetchy/quranic-phonemizer` (Round 3, 23 ⭐, MIT, NeurIPS 2025)  
**Project:** "Quranic Universal Audio (QUA)" — unifies Quran recitations with forced-alignment timing data  
**Space:** `hetchyy/quranic-universal-aligner` (live HuggingFace Space)

### What's In It
Each row (per ayah) contains:
- Embedded ayah audio clip
- Recited Uthmani text (exactly as recited, with repeated words preserved)
- **Word-level timestamps** (ms, relative to ayah clip)
- **Letter-level timestamps** (ms, relative to ayah clip)
- **Waqf-aware segment data** (pause points)
- `source_offset_ms` → maps back to original full-recitation audio

**Size:** 174,000 rows (ayah × reciter combinations)

### Also: hetchyy/everyayah-phonemes
A companion dataset providing phoneme data specifically for EveryAyah recitations — extends the `Hetchy/quranic-phonemizer` work with pre-computed phoneme sequences.

### Why This Matters for Sakīna
The **letter-level timestamps** are what distinguishes this from Quran-MD (word-level only) and Tadabur (word-level only). Letter-level timing enables:
- **Madd duration measurement** (is the elongation held long enough?)
- **Qalqalah echo detection** (brief vibration after sukun letters)
- **Ghunnah duration** (nasal resonance timing)

These are exactly the measurements Sakīna's tajweed classifier needs. Combined with `hetchyy/everyayah-phonemes`, this gives a complete phoneme → timestamp → tajweed-rule pipeline WITHOUT needing to run forced alignment (which was the bottleneck in TajweedAI from Round 11).

### Sakīna Action
1. `datasets.load_dataset("hetchyy/quranic-universal-ayahs")` 
2. Use letter-timestamps as supervision signal for madd/qalqalah/ghunnah timing
3. Use `hetchyy/everyayah-phonemes` for expected phoneme sequences
4. Replace `ctc-forced-aligner` step in TajweedAI pipeline with these pre-computed timestamps

---

## Finding 4 — Flutter Audio Recording: `audio_io` v0.3.3 (June ~14, 2026) Claims 1.5ms Buffer — Too New, Don't Adopt Yet

### New Package Found
**`audio_io` v0.3.3** (pub.dev, MIT, published ~June 14, 2026):
- Claims **~1.5ms buffer latency** in "Realtime" mode (vs. `record` v7.1.0 which doesn't publish buffer specs)
- **Float64, 48kHz, mono** — problematic: most ASR models expect Float32 or Int16 @ 16kHz, so conversion needed
- **3 likes, 428 downloads** — extremely new and unproven
- Platforms: iOS, macOS, Android, Web, Linux, Windows

### Verdict: Don't Replace `record` Yet
`record` v7.1.0 (717k downloads, 884 likes, BSD-3-Clause) is still the safe choice. `audio_io` needs:
1. More adoption to validate stability
2. Test that Float64→Float32/Int16 conversion doesn't add latency
3. iOS/Android real-device testing at v0.x

**Monitor**: Check `audio_io` again in Round 20+ when it reaches v1.0 or 5k+ downloads.

---

## Summary of Critical Gaps Remaining
1. **Nemotron-3.5 Arabic Quran WER**: Still unpublished — benchmark is the #1 priority task
2. **Arabic `prompt_index` integer value**: Use `101` (auto) for now; exact Arabic locale code not found
3. **hetchyy/quranic-universal-ayahs license**: Not confirmed — check before building on it
4. **arXiv:2503.23470 code**: Not released — contact Shaiakhmetov et al.
5. **QDAT dataset availability**: IEEE 2021, may need institutional access

---

## Sources
- [sherpa-onnx releases](https://github.com/k2-fsa/sherpa-onnx/releases)
- [sherpa-onnx Flutter pub.dev changelog](https://pub.dev/packages/sherpa_onnx/changelog)
- [sherpa-onnx issue #3664: Nemotron-3.5 multilingual support](https://github.com/k2-fsa/sherpa-onnx/issues/3664)
- [onnx-community/nemotron-3.5-asr-streaming-0.6b-onnx-int4](https://huggingface.co/onnx-community/nemotron-3.5-asr-streaming-0.6b-onnx-int4)
- [NVIDIA Nemotron 3.5 ASR — MarkTechPost](https://www.marktechpost.com/2026/06/06/nvidia-releases-nemotron-3-5-asr-a-600m-parameter-cache-aware-streaming-model-transcribing-40-language-locales-in-real-time/)
- [arXiv:2604.14493 — On-Device Streaming ASR benchmark](https://arxiv.org/abs/2604.14493)
- [arXiv:2503.23470 — Tajweed DNN evaluation](https://arxiv.org/abs/2503.23470)
- [hetchyy/quranic-universal-ayahs](https://huggingface.co/datasets/hetchyy/quranic-universal-ayahs)
- [hetchyy/everyayah-phonemes](https://huggingface.co/datasets/hetchyy/everyayah-phonemes)
- [Quranic Universal Aligner Space](https://huggingface.co/spaces/hetchyy/quranic-universal-aligner)
- [audio_io Flutter package](https://pub.dev/packages/audio_io)
- [flutter_recorder Flutter package](https://pub.dev/packages/flutter_recorder)
