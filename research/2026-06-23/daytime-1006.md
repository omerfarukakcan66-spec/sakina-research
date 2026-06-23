# Sakīna Research — Round 27
**Date:** 2026-06-23 | **UTC:** ~10:06 | **Focus:** Two critical prior-round corrections + obadx iOS deployment + Tarteel competitive timeline re-assessment

---

## Executive Summary

This round surfaces **two significant corrections** from prior research that change the competitive picture, plus confirms a production-ready iOS deployment of the best open-source Quran pronunciation model. No new Arabic ASR models appeared on HuggingFace in the June 16–23 window — the frontier is stable.

---

## Finding 1 — CORRECTION: AL Siraat Is ACTIVELY Maintained (v1.2.1, May 23 2026) — Prior "6-Month Stagnant" Claim Was Wrong

**Prior research (Round 24) stated:** AL Siraat (2.7M downloads) last updated December 30, 2025 — "6 months without an update as of June 2026, signaling engineering slowdown."

**This round's finding: INCORRECT.** AL Siraat has been continuously updated throughout 2026:
- **February 18, 2026:** AI recitation accuracy improvements + faster verse detection (source: Google Play listing)
- **April 3, 2026:** (update confirmed, changelog not retrieved)
- **May 23, 2026:** v1.2.1 — latest confirmed version (source: AppBrain `com.alquran.aiquran.quranlearning`)

AL Siraat has shipped **at least 3 updates in 2026**, the most recent just 31 days before this report. It is not stagnant.

### Why This Changes the Competitive Picture

Round 24's "6-month stagnant" assessment was the basis for arguing that AL Siraat's 2.7M users represented "accumulating churn" ripe for Sakīna harvesting. That argument is now weaker:
- An actively maintained app with animated Makhraj guides + 2.7M users at v1.2.1 is a healthy competitor, not a decaying one
- Sakīna's positioning against AL Siraat must rely on **live teacher sessions** (genuine moat), not on AL Siraat's engineering slowdown (not confirmed)

### What Remains True
- AL Siraat has **no live teacher sessions** — Sakīna's core moat is intact
- AL Siraat's animated Makhraj visuals are still the only such feature in any competitor app
- The gap Sakīna targets ("AL Siraat shows animated diagrams — Sakīna gives you a certified teacher who corrects you live") is still valid and differentiating

### Sakīna Action
Update all competitive materials to reflect AL Siraat v1.2.1 (May 23, 2026) as an actively maintained app with 2.7M+ users. Do not rely on competitor stagnation as a pitch argument — rely on genuine structural moat (live teacher).

---

## Finding 2 — CORRECTION: Tarteel Makharij Q2/Q4 Timeline Was Third-Party Speculation — No Official Tarteel Commitment Found

**Prior research (Round 19, confirmed in Rounds 20–26) stated:** "Tarteel Mahraj/Makharij detection: beta Q2 2026, full release Q4 2026" — described as "Tarteel's own update page confirms."

**This round's finding:** The Q2/Q4 timeline **cannot be verified from any official Tarteel source.**

Direct verification attempts:
- `site:tarteel.ai` search for "makharij OR mahraj OR articulation point OR phoneme" returned only general educational articles — no product roadmap page, no timeline announcement
- tarteel.ai/blog returned HTTP 403 (blocked)
- Tarteel's official Google Play app description (retrievable) still reads: *"Currently, this feature does not include Tajweed or pronunciation correction, but we know there's community demand and **it's on our roadmap for the future!**"* — **no date, no quarter, no specifics**
- The "Q2 beta / Q4 full release" language appears exclusively on **third-party blogs** (yokersane.com, aichief.com) using language like "predictions suggest" and "estimated timeline"

### Revised Assessment

| Prior claim | Revised status |
|---|---|
| "Tarteel Mahraj beta Q2 2026" | UNVERIFIED — third-party speculation |
| "Tarteel Mahraj full release Q4 2026" | UNVERIFIED — third-party speculation |
| "Tarteel does not have Tajweed correction shipped" | CONFIRMED — official app description still says roadmap only |
| "Tarteel is building toward phoneme detection" | PLAUSIBLE — 4-release sprint + prior feature velocity suggests it |

### Strategic Implication: The Racing Clock Is Uncertain in Both Directions

The Q3 2026 Sakīna ship urgency was partly premised on "beat Tarteel's Q4 Makharij release." If that Q4 date is unverified speculation, the competitive window could be:
- **Wider:** Tarteel may not ship Makharij until 2027
- **Narrower:** Tarteel's sprint pace (4 releases in 12 days) could mean a surprise announcement any week

**However: QariAI (qariai.app, v1.1.6, Feb 2026) already ships real-time Makharij/phoneme correction.** The competitive clock is not only Tarteel — it's QariAI, which has already solved this problem and is actively growing. QariAI is the near-term threat Sakīna should benchmark against, not Tarteel's unconfirmed roadmap.

### Sakīna Action
1. Remove the specific "Q2 2026 beta / Q4 2026 full release" Tarteel milestone from all pitch decks and competitive materials — it was third-party speculation
2. Replace with confirmed fact: "Tarteel's own app description confirms tajweed correction is roadmap-only as of June 2026"
3. Reframe competitive urgency around QariAI (ships Makharij today, iOS + Android) rather than a hypothetical Tarteel future feature
4. Tarteel is confirmed at v5.78.2 (June 19, 2026) — no new release found June 20–23

---

## Finding 3 — `obadx/muaalem-model-v3_2` Has a Live iOS Deployment (FastAPI + Modal) — Most Complete Open-Source Quran Pronunciation Pipeline Is Production-Ready

**Model:** `obadx/muaalem-model-v3_2` (Apache 2.0, uploaded August 27, 2025, part of arXiv:2509.00094 / ICML 2026)  
**Companion:** `obadx/recitation-segmenter-v2` (waqf pause segmentation, wav2vec2-BERT, same release)  
**Metric:** 0.16% average PER on QPS phoneme test set (best published result for tajweed-aware phoneme recognition)

### New Finding: iOS Deployment Reference Implementation Exists

An independent developer has already deployed the `obadx/muaalem-model-v3_2` pipeline in a native iOS app:
- **GitHub:** `github.com/iTarek/Quran-Muaalem-iOS`
- **Stack:** FastAPI backend on Modal (serverless GPU) + native iOS frontend
- **What it proves:** The full pipeline — `recitation-segmenter-v2` (waqf split) → `muaalem-model-v3_2` (pronunciation scoring) → per-segment feedback — works end-to-end on a real iOS device

Prior rounds (Rounds 7, 11, 22) covered the `obadx` models at the HuggingFace level but did not surface this iOS reference implementation. This closes the gap between "model exists" and "model works in production on iOS."

### Complete Production Stack (Confirmed Open, Apache 2.0)

```
iOS mic → record package (PCM16)
  → obadx/recitation-segmenter-v2 (waqf split, wav2vec2-BERT)
    → obadx/muaalem-model-v3_2 (QPS CTC, 0.16% PER)
      → Hetchy/quranic-phonemizer (expected phoneme labels)
        → diff → tajweed rule classification → user feedback
```

All five layers confirmed open-source (Apache 2.0 or MIT). The iOS reference at `iTarek/Quran-Muaalem-iOS` shows each layer is individually callable.

### Sakīna Action
1. **Clone `github.com/iTarek/Quran-Muaalem-iOS` immediately** — use it as the iOS pronunciation feedback reference implementation
2. The FastAPI/Modal backend pattern is directly portable to Flutter (call same API from Dart HTTP client)
3. The `obadx` models are the highest-quality open-source pronunciation pipeline available — use them for the live-session AI feedback layer
4. The 0.16% PER is at QPS phoneme level (not standard WER) — do not conflate with WER figures; measure on a held-out Quran word set for comparability

---

## Finding 4 — tadabur-Whisper-Medium/Large: NOT Released as of June 23, 2026

**Confirmed via three independent signals:**
- `github.com/fherran/tadabur` model table: only `FaisaI/tadabur-Whisper-Small` listed as "✅ Available" — Medium and Large are absent (not even listed as "coming soon")
- `site:huggingface.co "tadabur-Whisper-Medium"` → zero model card results
- arXiv:2604.18932 paper contains multi-size benchmark results but links no HuggingFace IDs for Medium or Large

The "coming soon" characterization in Round 25 was an inference from the paper's size-comparison table, not an explicit release announcement.

**Current status table:**

| Model | Released | WER | License |
|---|---|---|---|
| `FaisaI/tadabur-Whisper-Small` | ✅ ~June 15, 2026 | 8.7% / 6.5% CER | CC BY-NC 4.0 |
| `FaisaI/tadabur-Whisper-Medium` | ❌ Not released | Paper only | TBD |
| `FaisaI/tadabur-Whisper-Large` | ❌ Not released | Paper only | TBD |

### Commercial License Blocker on tadabur-Whisper-Small

CC BY-NC 4.0 prohibits commercial deployment without negotiating a license with Faisal Alherran. The `MaddoggProduction/whisper-l-v3-turbo-quran-lora-dataset-mix` (Apache 2.0, 12.69% WER, March 2026) is weaker but commercially usable without restriction today.

**Priority matrix for Sakīna ASR choice:**
1. **On-device real-time (primary):** `KheemP/whisper-base-quran-lora` (5.98% WER, hafs-murattal) — already in ONNX pipeline
2. **Server-side accuracy:** Contact Faisal Alherran for CC BY-NC commercial license on tadabur-Whisper-Small; evaluate Medium/Large when released
3. **Commercial fallback if CC BY-NC blocked:** `MaddoggProduction` Apache 2.0 model (12.69% WER, faster Turbo base)

---

## Finding 5 — Competitor Activity Snapshot: June 20–23, 2026

| App | Latest Version | Date | Key Change |
|---|---|---|---|
| Tarteel (Android) | v5.78.2 | June 19, 2026 | Hotfix sprint paused — no new release June 20–23 |
| Tarteel (iOS) | ~v5.76.x | ~Feb 2026 | Still 4+ months behind Android |
| AL Siraat | v1.2.1 | May 23, 2026 | Active — prior "stagnant" assessment corrected |
| Qara'a | v5.1.41 | June 13, 2026 | Performance + bug fixes |
| Tilawa.ai | — | ~April 2026 | No update June 20–23 |
| QariAI | v1.1.6 | February 1, 2026 | Last confirmed; no June update found |
| TajweedMate | — | May 10, 2026 | Bug fix update |

**No new competitor app launches confirmed June 20–23, 2026.**

---

## Key Corrections for Pitch Materials

| Remove from pitch materials | Replace with |
|---|---|
| "AL Siraat stagnant — 6 months without update" | "AL Siraat v1.2.1 (May 2026) — actively maintained, 2.7M users, no live teacher" |
| "Tarteel Makharij beta Q2 2026, full release Q4 2026" | "Tarteel's own app description confirms tajweed correction is roadmap-only — no official timeline given" |
| "Sakīna must ship before Tarteel's Q4 Makharij" | "QariAI already ships phoneme-level Makharij — Sakīna needs live-teacher differentiation against QariAI today, not just Tarteel tomorrow" |

---

## Sources

- AppBrain: `com.alquran.aiquran.quranlearning` — AL Siraat version history
- Google Play: AL Siraat Feb 18, 2026 update — AI recitation accuracy
- Tarteel Google Play official listing — "roadmap for the future" (no date)
- GitHub: `fherran/tadabur` — model availability table
- HuggingFace: `obadx/muaalem-model-v3_2`, `obadx/recitation-segmenter-v2`
- GitHub: `iTarek/Quran-Muaalem-iOS` — iOS deployment reference
- arXiv:2604.18932 — Tadabur paper (Whisper Small confirmed, Medium/Large unconfirmed)
- arXiv:2509.00094 / ICML 2026 page 80144 — QPS model (obadx)
- QariAI Google Play: `app.qari.ai` v1.1.6, February 1, 2026
- Qara'a UpdateStar: v5.1.41, June 13, 2026
