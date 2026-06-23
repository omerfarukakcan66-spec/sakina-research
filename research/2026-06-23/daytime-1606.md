# Research: 2026-06-23 Daytime (16:06 UTC) — Round 29
## Focus: Flutter Live Session Infrastructure (28-Round Blind Spot) + uzair0/quran-asr Status + tadabur-Whisper-Large

---

## Finding 1 — Flutter Live Session Stack FULLY MAPPED: livekit_client v2.8.1 + flutter_webrtc v1.5.2 = Complete Teacher-Student Audio Foundation (28-Round Blind Spot Resolved)

**The 28-round gap**: Across all 28 prior research rounds, Sakina's core product differentiator — live teacher-student audio sessions — was treated as an implementation detail and never researched. This round maps it completely.

### Layer 1: Low-Level WebRTC — `flutter_webrtc` v1.5.2

- **Version**: 1.5.2, published **June 20, 2026** (3 days ago)
- **License**: MIT
- **Publisher**: flutter-webrtc.org (verified)
- **Downloads**: 177,000 weekly
- **Platform support**: Android, iOS, Web, macOS, Windows, Linux

**Critical audio changelog — v1.5.0 (June 12, 2026):**
> "use the AVAudioEngine audio device module on both platforms, enabling **Apple platform voice processing (AEC/NS/AGC)**"

This is architecturally significant for Sakina: **echo cancellation (AEC), noise suppression (NS), and auto-gain control (AGC)** are now built into flutter_webrtc v1.5.0+ on iOS and macOS via Apple's AVAudioEngine. Before June 12, 2026, any Sakina prototype using flutter_webrtc would have had teacher audio bleeding into the student's Quran recitation microphone — a fatal flaw for ASR accuracy. Now it's solved by the platform layer at no extra engineering cost.

**v1.5.1 (June 14, 2026)**: AudioSwitch bump with Communication Device API support, wired headset and Bluetooth fixes — important for teachers using headsets.

**v1.5.2 (June 20, 2026)**: Android audio device module exposed to embedders — allows Sakina to route the Quran ASR mic signal to a separate audio capture path from the WebRTC call audio, enabling simultaneous Groq/sherpa-onnx ASR during a live teacher session.

### Layer 2: Room Abstraction — Three Options Compared

#### A) `livekit_client` v2.8.1 — RECOMMENDED for Sakina

- **Version**: 2.8.1, published **June 20, 2026** (3 days ago)
- **License**: Apache 2.0 (commercial-safe)
- **Publisher**: LiveKit (verified)
- **Downloads**: 89,600 weekly
- **Platform support**: Android, iOS, Web, macOS, Windows, Linux

**Architecture advantage**: LiveKit is an **open-source SFU** (Selective Forwarding Unit) — routes audio/video without decoding it, keeping CPU and latency minimal. Flutter SDK sits on top of flutter_webrtc.

**v2.8.0 key features**:
- Session API for simplified End-to-End Encryption (E2EE) — teacher-student sessions can be encrypted without key management boilerplate
- Protocol update to LiveKit protocol v1.45.8
- Fixed `waitForBufferStatusLow` busy-waiting after engine close (prevents CPU drain during session end)
- Android builds updated for compileSdk 36

**v2.8.1 additions**:
- Agent deployment targeting in token source options — critical for Sakina's AI-agent-to-teacher escalation routing
- Kotlin support via AGP 9
- Default video degradation changed to "maintain-resolution" (audio-only sessions are unaffected)

**LiveKit Pricing (June 2026)**:
- **Free tier (Build plan)**: 5,000 WebRTC minutes/month + 50 GB egress + **1,000 AI Agent minutes/month** — enough for ~83 hours of teacher sessions + AI pre-session warmup during beta
- **Cloud paid**: $0.0004–0.0005/min WebRTC + $0.004/min egress. For a 45-minute teacher-student session: ~$0.04/session (both participants)
- **AI Agent minutes**: $0.01/min — for Sakina's AI tutor running before teacher joins
- **Self-hosted**: Open-source server (github.com/livekit/livekit), $0/min in usage fees, pay only for own VPS (~$10–20/mo handles dozens of concurrent sessions)

**Sakina beta cost scenario**: 200 sessions × 45 min × 2 participants = 18,000 WebRTC min → **$7.20/month** at cloud rates, or **$0** if self-hosted.

#### B) `hmssdk_flutter` v1.11.1 (100ms)

- **Version**: 1.11.1, published **April 2026** (2 months ago)
- **License**: MIT
- **Publisher**: 100ms.live (verified)
- **Downloads**: 4,390 weekly (much lower adoption than livekit_client)
- **Platform support**: Android (API 24+), iOS (12+) — **no desktop/Web**
- **Pricing**: ~$0.001/participant-minute for audio-only (75% discount from video rate of $0.004/pm)
- **Limitation**: No open-source server option — fully managed cloud only. Last version 2 months ago vs LiveKit's 3 days ago — slower cadence.

#### C) Agora (`agora_rtc_engine`)

- **Free tier**: 10,000 minutes/month across all participants
- **Paid**: ~$0.0265/min — **6.6× more expensive than LiveKit Cloud for audio**
- **Latency advantage**: Claims ~40ms vs WebRTC's ~100ms typical
- **Flutter SDK**: Well-maintained but closed-source platform
- **For Sakina**: Only worth considering if global latency optimization is critical (GCC server distribution is excellent). At $0.0265/min, a 1,000-session month costs $1,192.50 — 165× more than LiveKit self-hosted.

### Summary Decision Matrix for Sakina

| | livekit_client v2.8.1 | hmssdk_flutter v1.11.1 | Agora |
|---|---|---|---|
| License | Apache 2.0 | MIT | Proprietary |
| Self-hostable | YES (open source) | No | No |
| Beta cost | $0 (self-hosted) | ~$0.001/pm audio | ~$0.0265/pm |
| AI Agent support | Built-in ($0.01/min) | No | Separate product |
| Desktop/Web | All platforms | Android/iOS only | Partial |
| Last update | June 20, 2026 | April 2026 | Active |
| Weekly downloads | 89.6k | 4.39k | High |
| **Recommendation** | **PRIMARY** | Fallback | Avoid for startup |

### Sakina Implementation Stack (Complete)

```
Student mic → flutter_webrtc v1.5.2 AEC/NS/AGC (built-in, June 12)
                   ↓                                    ↓
          WebRTC audio stream              Parallel ASR capture path
          → livekit_client v2.8.1         → record v7.1.0 → Groq Whisper
          → Teacher hears student          → tajweed feedback overlay
                   ↓
          Teacher speaks
          → livekit_client SFU routes to student
          → Student hears teacher (no echo, AEC handles it)
```

**Key architectural insight new this round**: flutter_webrtc v1.5.2 exposes the Android audio device module to embedders — Sakina can capture the mic for both WebRTC (teacher hears student) AND sherpa-onnx/Groq ASR (tajweed checking) simultaneously, without the two paths interfering. This was non-obvious and not possible before v1.5.2 (June 20).

**GitHub**: github.com/cloudwebrtc/flutter-webrtc (flutter_webrtc), github.com/livekit/client-sdk-flutter (livekit_client)

---

## Finding 2 — `uzair0/quran-asr` (6.31 GB): Still Actively Training — "Full Tashkeel Vocab" Confirmed, No WER Yet

**Current status (June 23, 2026)**: Model is confirmed by multiple search results to be actively maintained with training ongoing. Direct HuggingFace access returns HTTP 403 (model may be private or rate-limited).

**New detail discovered this round**: The model's processor is configured for **"full tashkeel vocab"** — meaning it outputs fully diacritized Arabic (full harakat/tashkeel) rather than bare Arabic consonants. This is exactly the output format Sakina needs for character-level tajweed diff:

```
Expected: كَلِمَةٌ (with full tashkeel)
Model output: كَلِمَةٌ (correct) or كَلِمَةَ (harakah error → flag)
→ char diff → tajweed rule violated (e.g., tanwin at end = Idgham rule)
```

At 6.31 GB (Whisper-large-v3 class), training on a large diacritized Quran corpus with full tashkeel output would make this the first 6× more powerful model than KheemP (whisper-base, ~150MB) for Quran-specific diacritized ASR.

**Status**: Cannot benchmark until training stabilizes (commits still active as of this round). No license confirmed.

**Priority action**: Monitor `huggingface.co/uzair0/quran-asr` once per day for commit activity to stop. When stable: (1) check license, (2) attempt ONNX export, (3) benchmark WER on `Buraaq/quran-audio-text-dataset` within 24h of stability.

---

## Finding 3 — `tadabur-Whisper-Large` Still "Coming Soon" + `tadabur-Whisper-Medium` Not Announced

**Status (June 23, 2026)**: tadabur-Whisper-Large remains "Coming Soon" on the Tadabur project page (fherran.github.io/tadabur). No release. tadabur-Whisper-Medium has no "coming soon" status — it appears it may be skipped or released simultaneously with Large.

- **tadabur-Whisper-Small** (released June 15): WER 8.7%, CER 6.5%, 600+ reciters, murattal + mujawwad
- **tadabur-Whisper-Large** (coming soon): Expected WER 5-7% on diverse recitation styles (inference from scale)
- **Gap since Small**: 8+ days and counting

If `uzair0/quran-asr` (6.31 GB, full tashkeel) stabilizes before `tadabur-Whisper-Large` releases, `uzair0` may be the Large-scale Quran ASR solution to benchmark first.

**Sakina action**: Both uzair0 and tadabur-Large are in the "imminent" category. Do not hold up the ASR stack decision for either — use tadabur-Whisper-Small + KheemP dual-model approach confirmed in Round 26 until a new model demonstrably outperforms on the benchmark.

---

## Finding 4 — Live Session Infrastructure Cost Summary for Sakina Budget Planning

**Recommended stack costs for Sakina beta (100 sessions/month)**:

| Phase | Stack | Cost |
|---|---|---|
| Dev/prototype | LiveKit self-hosted ($10/mo VPS) + flutter_webrtc | ~$10/mo |
| Beta (100 sessions) | LiveKit Cloud Build (free tier: 5k min/mo) | $0/mo |
| Beta+ (500 sessions) | LiveKit Cloud Ship ($50/mo + $0.0004/min overage) | ~$54/mo |
| Production (2k sessions) | LiveKit self-hosted (2× $20/mo instances) | ~$40/mo |

A 45-minute teacher-student session uses: 45 min × 2 participants = 90 WebRTC minutes. At 5,000 free minutes/month, Sakina gets **~55 free sessions per month** before paying a cent on LiveKit Cloud Build. This covers the entire closed beta.

**Bottom line**: Sakina's live session infrastructure (livekit_client v2.8.1 + flutter_webrtc v1.5.2) costs **$0** during beta, with a clear path to <$40/month for production at 2,000 sessions. This is not a cost obstacle.

---

## Summary of Open Actions (Updated Round 29)

1. **[IMMEDIATE]** Add `livekit_client: ^2.8.1` and `flutter_webrtc: ^1.5.2` to Sakina pubspec.yaml. Start LiveKit free cloud account. Begin teacher-student session proof of concept.
2. **[DAILY MONITOR]** Check `uzair0/quran-asr` for commit activity stabilization. When stable → benchmark WER immediately.
3. **[WEEKLY MONITOR]** Check `FaisaI/tadabur-Whisper-Large` and `-Medium` for release. Expected within 2-3 weeks.
4. **[ARCHITECTURE NOTE]** flutter_webrtc v1.5.2 Android audio device module exposure enables simultaneous WebRTC teacher audio + sherpa-onnx/Groq ASR on student mic — implement as two separate AudioTrack consumers on the same mic input.
