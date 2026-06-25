# Sakina Research — Night Run
**Date:** 2026-06-25 · **Time:** 03:33 UTC · **Round:** 40

---

## Executive Summary

Night run. Three genuine new findings across 20+ searches. Monitors checked: Tarteel stall extends to **7 days** (longest observed gap). Two previously uncovered items surface: (1) arXiv:2504.12254 — a 15,000-hour weakly supervised Conformer for Arabic ASR claiming SOTA, slipped through 40 rounds; (2) **IqraAI** (MIT, GitHub) — the first open-source multilingual Quran recitation checker built on Tarteel's public Whisper model. No new major Quran ASR models released. sherpa-onnx, flutter_webrtc, and record all stable. livekit_client v2.9.0-dev.0 prerelease appeared June 21.

---

## Monitor Status (All 3 Targets)

### Monitor 1 — Tarteel Version Track
- **Current official stable:** v5.78.2 (June 19, 2026)
- **Days since last update:** 7 (as of June 25 03:33 UTC)
- **Status:** STALL EXTENDED — now the longest release gap observed across 40 rounds of monitoring
- **v5.79+ found:** NO across all sources (Google Play, Uptodown, APKPure, Softonic)
- **Makharij/Mahraj announcement:** NO — Google Play description still reads: *"Currently, this feature does not include Tajweed or pronunciation correction, but we know there's community demand and it's on our roadmap for the future!"*
- **Blog update (June 2026):** No new blog post found. Most recent indexed = February 2026
- **X/Twitter:** No June 2026 feature announcement found; @tarteelAI last significant index from earlier months
- **Interpretation:** Either a larger feature release is being prepared (consistent with Q2 Mahraj beta hypothesis) or the hotfix sprint has simply ended. The "From Page to Screen: Rethinking Quran Rendering" blog post (Feb 2026) suggests Tarteel's engineering team is also working on Mushaf rendering — possibly a visual redesign is bundled with the Makharij beta drop.
- **Sakina racing clock:** Q3 2026 ship window for live sessions + phoneme feedback remains fully open.

### Monitor 2 — `uzair0/quran-asr` Training Completion
- **Status:** STILL TRAINING — search snippet still shows active checkpoint saves
- **Last commit activity:** "~7 hours ago" checkpoint save (consistent across rounds 28–40, training ongoing)
- **WER published:** NO
- **License:** Apache 2.0 (confirmed round 30)
- **Trigger event:** NOT MET — commit frequency has NOT dropped to weekly
- **Action:** Continue monitoring daily. Trigger = commit frequency drops from daily to ≤weekly → benchmark within 24h on `Buraaq/quran-audio-text-dataset`.

### Monitor 3 — `FaisaI/tadabur-Whisper-Large` / Medium Release
- **Status:** STILL "COMING SOON" — both Medium and Large remain unreleased
- **Small model:** `FaisaI/tadabur-Whisper-Small` (8.7% WER, CC BY-NC 4.0) remains the only shipped model
- **GitHub (fherran/tadabur):** Model table still shows Medium/Large as "Coming Soon"
- **Days since Small released:** June 15 → June 25 = **10 days** still unreleased
- **Action:** Monitor `FaisaI` HuggingFace org for new model uploads daily.

---

## Insight 1 — arXiv:2504.12254 (April 2026): 15,000-Hour Weakly Supervised Conformer for Arabic ASR, Claims SOTA — Not in Any Prior Round (40-Round Blind Spot)

**Paper:** "Advancing Arabic Speech Recognition Through Large-Scale Weakly Supervised Learning" (Mahmoud Salhab, Marwan Elghitany, Shameed Sait et al., arXiv:2504.12254, April 16/19 2026 revision, "Work in Progress").

**Architecture:** Conformer trained **from scratch** (not a Whisper fine-tune) on **15,000 hours** of weakly annotated Arabic speech — the largest Arabic ASR training run found in 40 rounds (vs. Tadabur 1,400h, muaalem-v3 848h, Tarteel ~1,250h). Data covers both **Modern Standard Arabic (MSA) and Dialectal Arabic (DA)** — no manual transcriptions required (weakly supervised via noisy pseudo-labels from a base model). Claims SOTA "surpassing both open and closed-source models on standard Arabic benchmarks."

**Why it matters for Sakina:**
- 15,000h of weakly supervised Arabic audio means the model has seen far more Arabic acoustic diversity than any Quran-specific fine-tune
- Conformer architecture (not Whisper) = streaming-compatible, ONNX-exportable (same family as `obadx/muaalem-model-v3_2`, NVIDIA pcd, Cohere Transcribe)
- If publicly released, a 15,000h Arabic Conformer would be the single best **backbone** to fine-tune on Quranic data — more acoustic headstart than whisper-large-v3's general multilingual pretraining
- Weakly supervised methodology shows Sakina could build its own large Arabic ASR by auto-labeling recorded lesson audio (teacher-student sessions as training data for the ASR layer)

**Critical unknowns:** Model weights NOT confirmed released. "Work in progress" caveat means arXiv preprint precedes weight release. License unknown. HuggingFace org (Salhab/Elghitany) not confirmed. No HuggingFace model card found yet.

**Sakina actions:**
1. Search HuggingFace for `mahmoudsalhab`, `elghitany`, `sait` author handles — if weights released, evaluate on `Buraaq/quran-audio-text-dataset` immediately
2. If weights unreleased, file as the reference architecture for weakly supervised Arabic ASR at scale — when Sakina accumulates 100h+ of real teacher-student session audio, apply this recipe to fine-tune a domain-specific Arabic ASR backbone
3. Cite the 15,000h weakly supervised paradigm in pitch materials: "Sakina's live sessions are also a data flywheel — every recorded lesson can train a better ASR engine without manual transcription"

---

## Insight 2 — `AbdirahmanNomad/IqraAI` (MIT, GitHub): First Open-Source Multilingual Quran Recitation Checker (Somali + Amharic + Swahili) Built on Tarteel Whisper

**Repo:** `github.com/AbdirahmanNomad/IqraAI` (MIT license, 5 commits, Python)

**What it does:** Full Quran recitation analysis pipeline using `tarteel-ai/whisper-base-ar-quran` for ASR:
- Verse matching with color-coded accuracy feedback: **green** (correct), **red** (missed/incorrect), **yellow** (extra/inserted)
- **Iqra mode:** Select specific verses → record → compare → get per-word diff
- **Hijaiyah letter practice** (isolated Arabic letter recognition mode)
- Export results as **TXT, JSON, SRT** (subtitle format — unique: enables downstream video/audio alignment)
- Multilingual translations: **Arabic, English, Somali, Amharic, Swahili** — the first Quran recitation tool with explicit East African language support

**First-run model download:** ~500MB (the whisper-base-ar-quran weights)

**Why it matters:**
1. First open-source implementation supporting **Somali, Amharic, Swahili** — 3 languages not covered by any prior Quran app in 40 rounds of research. East African Muslim communities (~30M Somali-speaking Muslims, ~30M Amharic-speaking Ethiopian Muslims) are completely underserved. Sakina's teacher marketplace could specifically recruit Somali/Ethiopian teachers.
2. SRT export is architecturally clever: recitation analysis output as a subtitle file = timestamped word-level accuracy log that can be overlaid on a recording. Sakina's teacher review mode could adopt this: teacher sees color-coded word-by-word accuracy as a subtitle track on the student recording.
3. The **verse matching architecture** (Tarteel Whisper → transcript → color diff vs. expected text) is the simplest possible reference implementation of the recitation check loop — readable Python code Sakina can study directly.

**Limitations:**
- Uses `whisper-base-ar-quran` (Tarteel's public research artifact, NOT their production model) — WER not benchmarked, likely ~5.75% on professional reciters but worse on beginners
- No live streaming (batch file analysis only)
- No tajweed rule identification (word-correct/incorrect only, not which rule was violated)
- 5 commits = early-stage personal project, no CI/releases

**Sakina actions:**
1. Clone the repo and read the verse-matching Python loop — this is the simplest possible reference implementation of the recitation check pipeline
2. Adopt the SRT export concept for Sakina's teacher review screen: teacher joins a session → student audio is processed → teacher sees a subtitle-style word-accuracy overlay in real time
3. Consider building a **Somali/Amharic/Swahili teacher recruitment channel** — zero competitors serve East African Muslims in their language with live teacher sessions. This is an uncontested market vertical.

---

## Insight 3 — `livekit_client` v2.9.0-dev.0 (June 21, 2026): First Prerelease of Next Major Version — Agent Deployment Targeting, AGP 9 Kotlin Support

**Package:** `pub.dev/packages/livekit_client` — v2.9.0-dev.0 published June 21, 2026 (4 days ago). Stable remains v2.8.1 (June 20).

**What v2.8.1 added (most recent stable):**
- "Add agent deployment targeting to token source options"
- Android AGP 9 built-in Kotlin support compatibility fix
- Default video degradation preference: "maintain-resolution" for local video publishing

**What v2.9.0-dev.0 signals:** A major feature branch is forming. No changelog yet for the dev prerelease, but the version bump from 2.8.x → 2.9.x (not 2.8.2) indicates architectural changes rather than hotfixes. Given the 2.8.1 changelog ("agent deployment targeting"), v2.9.0 likely deepens LiveKit AI Agents integration (programmatic AI agents in teacher sessions, session routing, SFU-level mux/demux for AI speech pipeline).

**Relevance for Sakina:**
- v2.8.1's "agent deployment targeting" = Sakina can tag teacher sessions to specific AI agent instances (e.g., route student to AI pre-session warmup agent on one server, then hand off to live teacher on another)
- v2.9.0-dev.0's emergence suggests the next stable release will bring new agent capabilities — **wait for v2.9.0 stable before locking in Sakina's AI-agent integration pattern**
- AGP 9 Kotlin fix: Sakina's Android build will work cleanly with AGP 9 (required for target SDK 36 compliance in 2026)

**Sakina actions:**
1. Stay on v2.8.1 stable for now — v2.9.0-dev.0 is not production-ready
2. Watch GitHub releases for v2.9.0 stable; when it ships, review what changed in the AI Agents API
3. Use the 2.8.1 "agent deployment targeting" feature to route Sakina's AI pre-session warmup (Groq Orpheus TTS + KheemP ASR scoring) to a specific LiveKit Agent node distinct from the human teacher WebRTC session

---

## Package Stability Snapshot (June 25, 2026)

| Package | Last Known Version | Status |
|---|---|---|
| `sherpa_onnx` | **1.13.3** (pub.dev: "9 days ago" ≈ June 16) | STABLE — no 1.14.x |
| `livekit_client` | **2.8.1** stable + **2.9.0-dev.0** prerelease | NEW dev prerelease June 21 |
| `flutter_webrtc` | **1.5.2** (June 20) | STABLE |
| `record` | **7.1.0** (June 11) | STABLE — 14 days stable |

---

## Additional Signal: Interspeech 2026 Audio Encoder Capability Challenge (arXiv:2603.22728)

22 teams evaluated audio encoders as front-ends for Large Audio Language Models (LALMs) using the unified XARES-LLM framework. Hosted by Xiaomi/University of Surrey/Tsinghua/Dataocean AI. The winning encoder(s) from this challenge = the best audio front-ends for LALMs in 2026. **Relevance:** IQRA 2026's winning approach used LALMs for Arabic MDD — the Audio Encoder Challenge directly selects the best encoder to pair with a LALM for tasks like Arabic MispronunciationDD. Sakina should monitor the challenge leaderboard for the highest-scoring Arabic-capable encoder.

**Action:** Check `dataoceanai.github.io/Interspeech2026-Audio-Encoder-Challenge/` for leaderboard results when the challenge closes at Interspeech 2026.

---

## Competitive Landscape Update

| Competitor | Last Update | ASR Model | Live Teacher | Notes |
|---|---|---|---|---|
| **Tarteel** | v5.78.2 (Jun 19) — **7-day stall** | NeMo FastConformer (proprietary) | NO | Makharij roadmap-only |
| **AL Siraat** | v1.2.1 (May 23, 2026) | Proprietary | NO | Active maintenance |
| **QariAI** | v1.1.6 (Feb 2026) | NeMo variant | NO | Real-time Makharij |
| **Tilawa.ai** | ~April 2026 | NeMo ~20MB offline | NO | AI tutor chatbot |
| **TajweedMate** | v2.0.25 (Jun 12, 2026) | Proprietary offline | ASYNC only | 40+ Qari training |
| **Qara'a** | Active | Proprietary | ASYNC (24h) | 2M users, SEA |
| **Thurayya** | v8.35.0 (May 22, 2025) | Children-specific | NO | Kids vertical only |
| **IqraAI** | New (5 commits) | whisper-base-ar-quran | NO | MIT, multilingual |
| **Sakina** | — | sherpa-onnx + Groq | **YES (LIVE)** | The moat |

---

## Open Items / Action Priority

| Priority | Action | Status |
|---|---|---|
| **P0** | Add `initial_prompt = selected_verse_text` to all Whisper calls (22% WER reduction free) | NOT YET DONE |
| **P0** | Monitor `uzair0/quran-asr` daily — benchmark when training stabilizes | ONGOING |
| **P1** | Find arXiv:2504.12254 weights on HuggingFace | NEW — check today |
| **P1** | Wait for `livekit_client` v2.9.0 stable; use 2.8.1 now | MONITOR |
| **P1** | Clone IqraAI verse-matching loop as reference implementation | NEW |
| **P1** | Benchmark nvidia/stt_ar_fastconformer_hybrid_large_pcd_v1.0 on Buraaq dataset | PENDING |
| **P2** | Check tadabur-Whisper-Medium/Large daily | ONGOING |
| **P2** | Monitor Interspeech 2026 Audio Encoder Challenge leaderboard | NEW |
| **P3** | Research Somali/Amharic/Swahili teacher recruitment (East African market) | NEW |
