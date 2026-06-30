# Sakina Research — 2026-06-30 Daytime (08:08 UTC)

**Topic:** Tarteel v5.78.2 Stall Status + `MuazAhmad7/Surah_Ikhlas-Labeled_Dataset` (Dec 2025, CC-BY-4.0) + `sobolev210/quran-recitation-errors` — Public Labeled Tajweed Datasets Found (Round 50)
**Round:** 50
**Critical Question:** Has Tarteel shipped v5.79+? What changed? And are there any new labeled Tajweed error datasets on HuggingFace not catalogued in prior rounds?

---

## Finding 1 — Tarteel v5.78.2 Stall Confirmed: Day ~22 (Longest Gap in 50 Rounds); Only Confirmed Change: Removed Ayah Pauses — Makharij Still Roadmap-Only

**Stall duration:** Last release was v5.78.2 on **June 19, 2026**. As of June 30, 08:08 UTC, **no v5.79+** found on:
- APKMirror (shows only up to v5.77.3; v5.78.2 confirmed from other trackers)
- Uptodown (shows v5.76.4 as latest indexed)
- Aptoide (no new version)
- Google Play search snippet (still v5.78.2)
- App Store search snippet (no new version)
- Mod APK sites: "v5.85.1" continues to be a fabricated MOD version (confirmed fake in Round 45)

**Only confirmed UX change in recent updates:** Google Play "What's New" snippet confirms: *"removes the pauses between ayahs when listening to reciter audio."* This is a minor audio playback change — not Makharij, not Tajweed phonetics, not structural.

**Tajweed/Makharij status:** One third-party Indonesian tech blog (yokersane.com, not Tarteel official) speculated "Tajweed and Pronunciation Correction Beta for Q2 2026, full release Q4 2026" including "Mahraj detection, Harakat length precision, and Tajwid rules application." **This is unconfirmed speculation from a non-official source** — no Tarteel blog post, no App Store notes, no press release corroborates it. Prior rounds have confirmed Tarteel's own support docs still read: *"Tarteel does not provide detailed correction for Tajweed."* Treat the Q4 2026 timeline as rumor until Tarteel publishes it.

**Strategic interpretation:** The ~22-day stall (longest in 50 rounds) may indicate one of:
1. A large bundled feature release is in internal QA (Makharij beta)
2. The hotfix sprint ended and no new feature cycle has begun
3. Engineering resources shifted to backend (ML model iteration)

**Bottom line for Sakina:** Ship window is maximally open. Even if Tarteel ships Makharij beta in Q4 2026, Sakina's **live teacher + AI pipeline combination** remains unmatched — Tarteel is AI-only with no human session layer.

---

## Finding 2 — `MuazAhmad7/Surah_Ikhlas-Labeled_Dataset` (Dec 9, 2025, HuggingFace, CC-BY-4.0): First Public Binary-Labeled Qalqalah Error Dataset — 1,506 WAV Files — 49-Round Blind Spot

**URL:** `huggingface.co/datasets/MuazAhmad7/Surah_Ikhlas-Labeled_Dataset`
**License:** CC-BY-4.0 ✓ (commercial use with attribution)
**Upload date:** December 9, 2025 (README timestamp)
**Size:** 1,506 WAV audio files (1K–10K category on HuggingFace)

### What it is
A systematic labeled dataset of Surah Al-Ikhlas (Chapter 112) recitations with binary Tajweed correctness labels:
- File naming: `ID{participant}V{verse}{T/F}.wav`
  - `T` = True = correct Tajweed recitation
  - `F` = False = contains a Tajweed error
- Verse coverage: 4 verses of Surah Al-Ikhlas (verses 1–4)
- Explicit annotation of **Qalqalah (قلقلة) errors** — the bouncing/echoing sound required on letters ق ط ب ج د
- Structure: participant × verse × correct/incorrect → systematic, balanced sampling
- Metadata: `metadata.csv` with per-file labels

### Why Surah Al-Ikhlas for Qalqalah
Surah Al-Ikhlas verse 1 (قُلْ هُوَ اللَّهُ أَحَدٌ) ends on the daal (د) — the canonical Qalqalah Kubra position. This makes it the ideal test verse for Qalqalah detection: binary distinction between correct bouncing echo vs. missed/flattened consonant.

### Why this matters for Sakina — the TajweedAI bridge
Round 47 (NeurIPS 2025) surfaced TajweedAI: an architecture achieving **100% Qalqalah accuracy** but trained on private data. The key remaining blocker was: **no public labeled Qalqalah training data**. `MuazAhmad7/Surah_Ikhlas-Labeled_Dataset` closes that gap:

| Step | Resource |
|---|---|
| Tajweed detector architecture | `github.com/nmazid121/tajweedAI` (NeurIPS 2025, open source) |
| ASR forced alignment | `Qwen/Qwen3-ForcedAligner-0.6B` (Apache 2.0, <50ms) |
| Training data (Qalqalah) | `MuazAhmad7/Surah_Ikhlas-Labeled_Dataset` (CC-BY-4.0, 1,506 files) ← NEW |
| Broader training data (3 rules) | `QDAT` dataset (1,500+ files, Madd/Ghunnah/Ikhfaa) |
| Target accuracy | >95% (EfficientNet-B0 on QDAT hit 95.35–99.34%, Round 36) |

**The complete Qalqalah pipeline is now buildable with 100% open/licensed components:**
1. Record via `audio_io` v0.3.0 or `record` 7.1.0
2. Forced alignment via `Qwen3-ForcedAligner-0.6B` → identify sukoon positions (post-consonant frames)
3. Extract 30–80ms post-consonant audio window
4. Binary classifier (TajweedAI architecture, EfficientNet-B0-SE) trained on MuazAhmad7 data
5. Output: Qalqalah present / absent → if absent → flag to teacher or AI feedback

**Commercial path (CC-BY-4.0):** Sakina can train, deploy, and ship this classifier commercially with attribution to MuazAhmad7.

### Limitations (verify before deployment)
1. **Scale:** 1,506 files may be insufficient alone — augment with QDAT and `obadx/muaalem-annotated-compressed-v3` Qalqalah segments
2. **Scope:** Surah Al-Ikhlas only (4 verses) — generalization to other surahs needs testing
3. **No affiliated paper found** — dataset appears to be an independent HuggingFace upload, no peer-reviewed validation yet
4. **Participant demographics unknown** — age/gender/proficiency of recorders not confirmed

### Sakina actions
- **(P0):** Download `MuazAhmad7/Surah_Ikhlas-Labeled_Dataset` → combine with QDAT → train EfficientNet-B0-SE binary Qalqalah classifier. Target: >95% on held-out test from MuazAhmad7 T/F labels
- **(P0):** Wire Qalqalah classifier as first live Tajweed auto-feedback feature. Surah Al-Ikhlas is one of the first 5 surahs every beginner learns — high learner traffic, perfect Beta surface
- **(P1):** Extend to other Qalqalah-heavy surahs using Quran-MD word-level audio (`Buraaq/quran-md-ayahs`) — auto-extract segments where Qalqalah letters appear in sukoon/waqf positions
- **(P2):** File dataset attribution in Sakina app credits per CC-BY-4.0 requirement

---

## Finding 3 — `sobolev210/quran-recitation-errors` + `quran-recitation-errors-test` (HuggingFace): Train/Test Split Recitation Error Dataset With Viewer — Not in 49 Prior Rounds

**URLs:**
- `huggingface.co/datasets/sobolev210/quran-recitation-errors/viewer` (train set with HuggingFace viewer)
- `huggingface.co/datasets/sobolev210/quran-recitation-errors-test` (held-out test set)

### What is known
- Two-part dataset: train split + a separate held-out test repository
- Has HuggingFace viewer (= structured, column-based, browsable)
- `metadata.jsonl` with labels (19.5 kB for test set = ~hundreds of labeled samples)
- 11 commits in test repo history
- Contains "temp_audio_chunks" folder → indicates audio segments, not full recitations

### What is unknown (must check manually)
- License: not confirmed in search results
- Error type categories: what Tajweed errors are labeled? (could be word-level errors, could be tajweed rules)
- Dataset size (number of samples)
- Whether it's from a published paper or independent upload
- Audio format and duration distribution

### Why it matters (conditionally)
If `sobolev210/quran-recitation-errors` contains per-rule Tajweed error labels (beyond word-level correct/incorrect), it could be the second labeled tajweed classification dataset alongside MuazAhmad7. The test/train split structure suggests it was designed for ML evaluation, not just exploratory use.

### Sakina actions
- **(P1):** Visit `huggingface.co/datasets/sobolev210/quran-recitation-errors/viewer` — check column names/labels to understand error taxonomy
- **(P1):** If license is permissive (MIT/Apache/CC-BY) AND error labels include specific Tajweed rules → combine with MuazAhmad7 for multi-rule classifier training
- **(P2):** Check commit history for any associated GitHub repo or paper link

---

## Monitor Status (Round 50)

| Monitor | Last Known | Current Status | Change |
|---|---|---|---|
| **Tarteel** | v5.78.2 (June 19) | **No v5.79+** — Day ~22 stall | No change; longest gap in 50 rounds |
| **`uzair0/quran-asr`** | Training (~step 2000, June 26) | Still training, no WER published | No change |
| **`tadabur-Whisper-Large/Medium`** | "Coming Soon" (15+ days) | Still "Coming Soon" | No change |
| **`sherpa-onnx`** | v1.13.3 (June 15) | Still v1.13.3 — no v1.14.x | No change |
| **`livekit_client`** | v2.8.1 stable, v2.9.0-dev.0 pre | Still v2.8.1 stable | No change |
| **QariAI** | v1.1.6 (Feb 1, 2026) | ~149 days since last update (unverified this round) | No change expected |

---

## Round 50 Priority Additions

### Immediate (this sprint)
1. **(P0) Download and train Qalqalah classifier** — `MuazAhmad7/Surah_Ikhlas-Labeled_Dataset` (CC-BY-4.0) + QDAT → EfficientNet-B0-SE binary classifier → integrate as Sakina's first shippable Tajweed auto-feedback feature
2. **(P0) Check `sobolev210/quran-recitation-errors` viewer** — determine license and error taxonomy before next session
3. Carry forward all P0 items from Round 49: clone `iTarek/Quran-Muaalem-iOS` Modal deployment pattern; benchmark `microsoft/VibeVoice-ASR` on Buraaq/quran-audio-text-dataset

### Monitor
- Tarteel: if v5.79+ appears on APKMirror → immediately retrieve full changelog for Makharij/Tajweed content
- `uzair0/quran-asr`: trigger = commits stop daily cadence (training complete); retrieve WER from model card
- `tadabur-Whisper-Large`: trigger = GitHub README updates "Coming Soon" → "Available"

---

## Sources
- [APKMirror — Tarteel versions](https://www.apkmirror.com/apk/tarteel-inc/tarteel-ai-quran-memorization/) — v5.78.2 stall confirmed
- [Google Play — Tarteel](https://play.google.com/store/apps/details?id=com.mmmoussa.iqra) — "removes pauses between ayahs" changelog note
- [QariAI — Quran App Comparison 2026](https://qariai.app/quran-app-comparison-2026)
- [MuazAhmad7/Surah_Ikhlas-Labeled_Dataset](https://huggingface.co/datasets/MuazAhmad7/Surah_Ikhlas-Labeled_Dataset) — CC-BY-4.0, 1,506 WAV, Dec 9 2025
- [sobolev210/quran-recitation-errors viewer](https://huggingface.co/datasets/sobolev210/quran-recitation-errors/viewer)
- [sobolev210/quran-recitation-errors-test](https://huggingface.co/datasets/sobolev210/quran-recitation-errors-test/tree/aa4bbb0db3d75f3fdedbd893c5ff53d1110e1ae6)
- [MDPI — Enhanced Neural Speech Recognition of Quranic Recitations via a Large Audio Model (Aug 2025)](https://www.mdpi.com/2076-3417/15/17/9521)
- [livekit_client versions — pub.dev](https://pub.dev/packages/livekit_client/versions) — v2.8.1 stable confirmed
- [sherpa-onnx releases — GitHub](https://github.com/k2-fsa/sherpa-onnx/releases) — v1.13.3 confirmed latest
