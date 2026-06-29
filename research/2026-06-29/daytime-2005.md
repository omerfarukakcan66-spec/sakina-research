# Sakina Research — Round 46
**Date:** 2026-06-29 20:05 UTC
**Focus:** `audio_io` v0.3.0 (Flutter, 1.5ms Realtime Buffer, 45-Round Blind Spot) — First Flutter Package Designed for Sub-3ms Audio Capture Latency + Tarteel Stall Day 20+ Confirmed

---

## Insight 1 — `audio_io` v0.3.0 (pub.dev, Flutter): 1.5ms Realtime Buffer, Cross-Platform, Stream-Based API — 45-Round Blind Spot for Sakina's Audio Capture Layer

**pub.dev:** `pub.dev/packages/audio_io`
**Current version:** v0.3.0
**License:** Unknown (check pub.dev "License" tab before shipping)
**Platform support:** iOS, macOS, Android, Web, Linux, Windows

### What It Does
`audio_io` is a Flutter plugin providing **real-time, cross-platform audio streaming** with three configurable latency modes:

| Mode | Buffer | CPU | Use Case |
|---|---|---|---|
| `AudioIoLatency.Realtime` | **~1.5ms** | High | Live session: ASR + teacher stream |
| `AudioIoLatency.Balanced` | ~3ms | Medium | Practice sessions |
| `AudioIoLatency.Powersave` | ~6ms | Low | Background recording |

Audio format: **Float64, 48kHz, mono** — consistent across all platforms (eliminates the per-platform PCM format negotiation currently required by `record`). Simple `Stream<Float64List>` API — subscribe to the stream → receive audio frames.

### Why This Matters for Sakina — the 1.5ms Capture Layer

The current Sakina pipeline uses `record` 7.1.0 as the audio capture layer:
- `record` is a general-purpose recorder (file-oriented, streaming secondary feature)
- PCM format varies by platform (iOS → PCM16, Android → PCM16, Web → Float32)
- No configurable latency mode — OS chooses the buffer size

`audio_io` was explicitly designed for real-time audio processing. The 1.5ms Realtime buffer puts audio in the Flutter layer **before the next ASR chunk window opens** — meaning Sakina's 250ms on-device ASR chunk (sherpa-onnx Nemotron-3.5) has the most recent 1.5ms of audio rather than whatever `record`'s default OS buffer holds.

**Concrete pipeline delta:** Replace `record.startStream()` with `AudioIo.instance.startRealtime()` → `stream.listen((Float64List chunk) { ... })`. Add one downsampling step (48kHz Float64 → 16kHz PCM16) before sherpa-onnx `acceptWaveform()`. The capture-once-fork pattern (Round 31) still applies: one audio stream → fork to (sherpa-onnx, WebRTC).

### Caveats / Unknowns
1. **Format conversion cost:** audio_io delivers Float64/48kHz; sherpa-onnx expects PCM16/16kHz. Requires 3× downsampling + float→int16 cast in Dart. Cost is ~0.05ms per 1.5ms frame on modern ARM — negligible but must be tested.
2. **iOS AVAudioSession:** The documented failure mode (Round 31) was `record` + `flutter_webrtc` conflicting on AVAudioSession. audio_io uses a separate audio engine path — unclear if it avoids the same conflict. **Must test on real iOS device before adopting.**
3. **Pub score + maintenance:** v0.3.0 suggests early-stage (pre-v1.0). Check GitHub issues for iOS crash reports before shipping. The `record` 7.1.0 (BSD-3) has a much larger community footprint.
4. **License:** Not confirmed in search results. If GPL, cannot ship in closed-source app.

### Sakina Actions
1. **P1: Benchmark audio_io vs. record on real devices** — iOS 17/18 + Android 14. Measure actual microphone-to-sherpa `acceptWaveform()` round-trip latency. If audio_io delivers <5ms consistently, adopt it as the Realtime capture layer.
2. **P1: Test AVAudioSession conflict** — run `AudioIo.Realtime` + `flutter_webrtc` simultaneously on iPhone 15. If no conflict → direct replacement for `record` + `audio_session`.
3. **P2: Write Float64/48kHz → PCM16/16kHz converter in Dart** — `dart:typed_data` Float64List → downsampled Int16List via linear interpolation. Target <0.1ms per 1.5ms frame.
4. **P2: Check `audio_chat` (pub.dev/packages/audio_chat)** — appeared in same search cluster; may offer bidirectional audio for teacher-student voice without a full WebRTC stack.

---

## Monitors — All Unchanged, Tarteel Stall Now Day 10+ Since Last Confirmed v5.78.2 (Day 20+ Total)

### Tarteel (v5.78.2 → No v5.79+)
**APKMirror latest confirmed:** v5.77.3 (indexed May 27, 2026). v5.78.2 confirmed on APKPure + Aptoide + LiteAPKs as the current latest stable. No v5.79.x, v5.80.x, or higher found on any legitimate distribution channel.
- **Stall total: ~20 days** (last official release in the v5.78.x range; prior monitoring showed v5.78.2 June 19; today is June 29)
- **Makharij/Makhraj feature:** NOT launched. Competitor analysis (QariAI blog, April 2026) explicitly confirms Tarteel does not offer makharij guidance as of April 2026; no Tarteel blog announcement found since then.
- **Ship window assessment:** FULLY OPEN. 20-day stall is the longest documented gap in 46 rounds.

### uzair0/quran-asr (Apache-2.0, 6.31GB)
**Status:** No WER published. Training at step ~2000 as of June 26 (3 days ago). No model card evaluation section found. Not on Open Universal Arabic ASR Leaderboard (elmresearchcenter Space). Trigger condition NOT met. Monitor continues.

### FaisaI/tadabur-Whisper-Large + Medium
**Status:** Still "Coming Soon" — confirmed by Tadabur project site (fherran.github.io/tadabur) + GitHub (fherran/tadabur, 23 commits). tadabur-Whisper-Small (8.7% WER, CC BY-NC 4.0) is the only released model. Stall: 15+ days since Small released. Monitor continues.

### sherpa-onnx
**Latest:** v1.13.3 (June 15, 2026, PyPI confirmed). No v1.14.x. v1.13.3 release sequence: v1.13.3 (Jun 15) → v1.13.2 (May 13) → v1.13.1 (May 8) → v1.13.0 (Apr 28). Next version likely v1.13.4 or v1.14.0. No Cohere Transcribe Flutter integration in Flutter pub.dev package yet (JS API merged PR #3458 but not in pub.dev package). Monitor continues.

---

## Landscape — No New Quranic/Arabic ASR Paper Since Round 45

Search across arXiv for June 26-29, 2026 papers on Arabic ASR, Quranic ASR, and tajweed returned no new paper not already covered. The most recent Quranic ASR paper remains **arXiv:2606.19747** (June 18, 2026, covered in Round 45). The Arabic ASR space is quiet for 4 days.

**Open action items still P0 from Round 45:**
- Test undiacritized `initial_prompt` in Whisper calls (paper-predicted WER improvement)
- Monitor `huggingface.co/NabilMH` for arXiv:2606.19747 model release
- Benchmark Mega-ASR (`zhifeixie/Mega-ASR`) on `RetaSy/quranic_audio_dataset` (7K non-native recordings)
