# Research Run — 2026-06-25 Night (04:33 UTC) — Round 41

**Gap from prior run:** ~1 hour (previous: night-0333.md, same date)
**Mission areas:** Arabic Quran ASR, Flutter real-time audio, Tarteel monitoring, new ML models, open-source implementations

---

## Executive Summary

This is a monitoring run following the same-date 03:33 session. With only a 1-hour gap, no major technical developments occurred between runs. Key findings: **QariAI confirmed stagnant for 5 months** (nearest phoneme-feedback competitor stopped shipping in February); **Tarteel stall extends to 8 days** (longest in 40+ rounds); **`Nuwaisir/Quran_speech_recognizer` surfaced** as a previously uncatalogued verse-positioning model; all four active monitors (uzair0, tadabur-Large, sherpa-onnx, livekit_client) remain in prior state.

---

## 1. Competitive Intelligence — Tarteel

**Status: STALLED DAY 8 (June 19 → June 25)**

- Latest confirmed version: **v5.78.2 (Android, June 19, 2026)**
- No v5.79+ found on any tracker (Google Play, APKPure, Uptodown, soft112)
- No Makharij/Tajweed announcement on blog or social
- Most recent blog post: February 2026
- iOS remains behind Android (frozen at v5.76.4 per prior round data)
- Interpretation unchanged from Round 40: either preparing a large bundled release or the hotfix sprint simply ended
- **Strategic implication:** Sakina's Q3 2026 ship window remains fully open. Every day of Tarteel stagnation is runway for Sakina to ship first.

---

## 2. Competitive Intelligence — QariAI (NEW SIGNAL)

**Status: 5-MONTH STAGNATION CONFIRMED**

`qariai.app` — the primary near-term competitor for live phoneme-level Tajweed feedback (as established in Round 27):

- Latest version: **v1.1.6 (February 1, 2026)** — confirmed via AppBrain and App Store metadata
- **No update in ~147 days** (February → June 25)
- User reviews from March and April 2026 still praise functionality (not complaining about bugs)
- No public roadmap announcements found

**Why this matters:**
QariAI was positioned in Round 27 as "the near-term benchmark — already ships real-time phoneme-level Makharij correction on both stores." 5 months of zero releases is a significant signal:
1. Possible development resource constraints (small team, AI-only app without teacher revenue)
2. The competitor that Tarteel couldn't build is also not iterating
3. User trust from positive reviews but no new features = churn window opening

**Competitive reframe:** The phoneme-feedback AI-only space (QariAI, TajweedMate) is stagnating while the live-teacher space (Sakina's lane) has zero competitors. The "near-term benchmark" hasn't moved in 5 months.

**Sakina pitch addition:** *"QariAI ships AI Tajweed feedback — but hasn't shipped an update in 5 months, because AI-only Quran apps hit a ceiling. Sakina pairs the AI baseline with human teachers who never plateau."*

---

## 3. New HuggingFace Model — `Nuwaisir/Quran_speech_recognizer`

**Architecture:** Fine-tuned `elgeish/wav2vec2-large-xlsr-53-arabic` (2020 XLSR-53 architecture) on the Quran ASR Challenge dataset.

**Functionality:**
- Verse-positioning model: listens to user recitation → identifies which part of Quran they're reciting
- Coverage: **Juzz 30 only** (Surah 78–114, the last 37 surahs)
- Matching: edit distance between ASR output and Quran text
- Use case: "takes the user to the position of the Quran from where they had recited"

**Assessment:**
- Architecture is old (XLSR-53 is 2020, predates Whisper/wav2vec2-large-v2/modern CTC)
- No WER published
- License: unknown
- Limited scope (Juzz 30 only — the beginner-friendly short surahs)
- Lower strategic value than `yayaiu6` (full Quran, Needleman-Wunsch alignment, MIT) or `offline-tarteel` (audio embedding, no ASR needed)

**Why it's still useful:**
The Juzz 30 focus is pedagogically significant — 95%+ of Quran learners start with Juzz 30. If WER on Juzz 30 is better than full-Quran models (due to domain narrowing), this could be the on-device model for beginner recitation tracking specifically. Benchmark: run Juzz 30 clips from `Buraaq/quran-audio-text-dataset` against this model vs. muaalem-v3_2.

**Sakina action:** Check license (permissive?); benchmark on 10 Juzz 30 clips; if WER < 15% and license is clear → use as lightweight beginner verse-lock complementary to `yayaiu6`.

---

## 4. Monitor Status — All 4 Active Watches

### uzair0/quran-asr (Apache 2.0, 6.31GB)
- **Status: STILL TRAINING** — no completion signal
- Prior state: "step 2000, 3 days ago" still the last known checkpoint marker
- Trigger: commit frequency drop (daily → weekly) → benchmark within 24h
- No change from Round 40

### FaisaI/tadabur-Whisper-Large/Medium
- **Status: STILL "COMING SOON"** — Day 10+ since tadabur-Whisper-Small (June 15, 2026)
- GitHub model table still shows Large as "coming soon"
- No HuggingFace model found under FaisaI org for Large/Medium
- Prior WER context: tadabur-Whisper-Small = 8.7% WER; Large expected < 7%
- Trigger: HuggingFace model card appears → benchmark immediately
- No change from Round 40

### sherpa-onnx Flutter package
- **Status: v1.13.3 — STABLE, NO v1.14.x**
- Published ~9 days ago (June 16, 2026)
- CONFIRMED: Cohere Transcribe Dart/Flutter API already available since v1.12.35 (PR #3464)
- INT8 model `sherpa-onnx-cohere-transcribe-14-lang-int8-2026-04-01` already released (April 2026, confirmed MENA/Arabic)
- **CORRECTION TO ROUND 38:** Round 38 said "NOT yet in Flutter package v1.13.3; monitor for v1.14.x" — this was incorrect. Dart API was added in v1.12.35 and is fully present in v1.13.3. The Cohere Transcribe INT8 + Dart streaming ASR is available for Flutter today.
- Trigger: v1.14.x drops with new Arabic model support → evaluate
- No new version since Round 40

### livekit_client Flutter
- **Status: v2.8.1 stable, v2.9.0-dev.0 prerelease**
- v2.9.0-dev.0 published June 21, 2026 — not yet stable
- v2.8.1 remains the stable production version
- Recommendation: wait for v2.9.0 stable before locking in AI-agent integration pattern (as per Round 40 guidance)
- No change from Round 40

---

## 5. TajweedMate v2.0.27 Minor Update

- v2.0.27 (June 16, 2026) — minor update after v2.0.25 (June 12, 2026)
- Round 23 already covered v2.0.25 features: Full Quran text, Hausa + German language support, Kids Mode
- v2.0.27 appears to be a bug-fix release with no significant new features
- No change to competitive positioning from Round 23

---

## 6. No New arXiv Papers (June 25)

Exhaustive search across Arabic ASR, Quran recitation, tajweed detection, and mispronunciation detection returned no new arXiv submissions specifically from June 25, 2026. The most recent Quran-relevant papers remain:
- arXiv:2504.12254 (April 2026) — 15,000h Weakly Supervised Conformer (weights unconfirmed)
- arXiv:2604.18932 (April 2026) — Tadabur dataset paper
- arXiv:2603.29087v2 — IQRA 2026 finalized results

---

## 7. muaalem-annotated-compressed-v3 — Possible Recent Update

Search snippet indicated "published about 3 hours ago" for `obadx/muaalem-annotated-compressed-v3`. This likely refers to a minor dataset card update (not a new dataset upload), as the dataset was confirmed publicly released on June 23, 2026 per Round 30. No new data files expected.

**Sakina action:** Check the dataset card for any new splits, revised download instructions, or additional annotations added since June 23.

---

## Action Items Summary

| Priority | Item | Status |
|----------|------|--------|
| P0 (immediate) | Add `initial_prompt = selected_verse_text` to all Whisper API calls | ~22% relative WER reduction, one parameter |
| P0 | Build live teacher WebRTC using livekit_client v2.8.1 (stable) | $0 beta cost confirmed |
| P1 | Fine-tune CohereLabs/cohere-transcribe-03-2026 on muaalem-annotated-compressed-v3 | Apache 2.0 Conformer + MIT 848h data |
| P1 | Implement Cohere Transcribe INT8 via sherpa_onnx v1.13.3 for on-device Arabic ASR | Dart API confirmed available |
| P1 | Benchmark `Nuwaisir/Quran_speech_recognizer` on Juzz 30 clips | Check license first |
| Monitor | uzair0/quran-asr training completion | Daily frequency drop = trigger |
| Monitor | tadabur-Whisper-Large release | HuggingFace model card = trigger |
| Monitor | Tarteel v5.79+ (stall day 8) | Release break = trigger |
| Monitor | livekit_client v2.9.0 stable | Stable release = trigger |

---

## Sources

- [Tarteel Google Play](https://play.google.com/store/apps/details?id=com.mmmoussa.iqra)
- [QariAI App Store](https://apps.apple.com/us/app/qari-ai-master-your-voice/id6757345171)
- [QariAI Google Play](https://play.google.com/store/apps/details?id=app.qari.ai)
- [Nuwaisir/Quran_speech_recognizer HuggingFace](https://huggingface.co/Nuwaisir/Quran_speech_recognizer)
- [sherpa_onnx changelog pub.dev](https://pub.dev/packages/sherpa_onnx/changelog)
- [sherpa-onnx Issue #3442 Cohere Transcribe](https://github.com/k2-fsa/sherpa-onnx/issues/3442)
- [livekit_client pub.dev versions](https://pub.dev/packages/livekit_client/versions)
- [TajweedMate Android](https://play.google.com/store/apps/details?id=com.tajweedmate.androidapp)
- [obadx/muaalem-annotated-compressed-v3](https://huggingface.co/datasets/obadx/muaalem-annotated-compressed-v3)
- [IQRA 2026 arXiv:2603.29087v2](https://arxiv.org/abs/2603.29087)
