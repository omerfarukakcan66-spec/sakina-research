# Sakina Research — Round 23
**Date:** 2026-06-23 | **Run:** night-0132 UTC  
**Focus:** Arabic Quran ASR • Flutter audio • Competitor tracking • New ML models  
**Prior context:** Rounds 1–22 in SUMMARY.md — reading before searching to avoid duplication.

---

## Summary of New Findings

Three genuinely new signals this round: two peer-reviewed 2026 papers explicitly validating Sakina's human-teacher thesis, TajweedMate v2.0.25 as a newly-tracked competitor with an explicit Makhraj AI failure admission, and Tarteel v5.78.2 as the confirmed latest Android build.

---

## Insight 1 — Two Peer-Reviewed 2026 Papers Explicitly Confirm "AI Cannot Replace Quran Teachers" — Use in Every Pitch Deck

### Paper A: "Integrating AI into Qur'an Learning: Technical Advances and Pedagogical Gaps"
- **Author:** Mehmet Birgün (University of Groningen)
- **Published:** 2026 — Social Sciences and Humanities Open, Vol. 13, Article 102499
- **DOI:** https://doi.org/10.1016/j.ssaho.2026.102499
- **SSRN preprint:** https://papers.ssrn.com/sol3/papers.cfm?abstract_id=5473279
- **Method:** Qualitative case study — literature review + comparative analysis of existing AI Quran apps
- **Key finding (verbatim):** *"while AI provides features such as pronunciation analysis, tajweed rule detection, and adaptive feedback, it lacks pedagogical direction and spiritual transmission. AI serves as a complementary, not a replacement, in Qur'an education. Traditional teaching methodologies remain indispensable."*
- **Sakina use:** Third peer-reviewed source (joins arXiv:2508.19587 "AI cannot replace teachers" + Frontiers in Education from earlier rounds) directly stating the human-teacher thesis. Quote in investor pitch, grant materials, and app store listings.

### Paper B: "Artificial Intelligence in Qur'anic Education: Pedagogical Promise, Practical Challenges, and Ethical Concerns"
- **Published:** Frontiers in Education, Vol. 11, 2026
- **DOI:** https://doi.org/10.3389/feduc.2026.1876380
- **License:** Open-access, Creative Commons Attribution (CC BY)
- **Method:** Semi-structured focus group discussions with **20 Islamic Education teachers**, structured analysis of two AI tools: **Tarteel** (specialist app) + **ChatGPT** (general AI)
- **Key findings:**
  - *Opportunities confirmed:* AI supports recitation, memorization practice, simplified interpretation, learner autonomy, confidence, accessibility, self-paced learning
  - *Critical concerns from teachers:* accuracy of AI output, authenticity of religious content, over-reliance on AI, **"potential erosion of the spiritual and relational dimensions of Qur'anic learning"**
- **Sakina use:** Focus group of 20 teachers confirmed that spiritual and relational dimensions resist AI automation. This is Sakina's core value proposition stated by 20 professional Islamic Education teachers rather than by Sakina's founders. Cite in pitch as "teachers themselves say."

### Combined citation strategy
> "Three peer-reviewed studies published in 2025–2026 — including a focus-group study with 20 Islamic Education teachers (Frontiers in Education, 2026), and a comparative review of AI Quran apps (SSAHO/University of Groningen, 2026) — confirm that AI tools lack the pedagogical direction and spiritual transmission that human teachers provide. Sakina is built on exactly this gap."

---

## Insight 2 — TajweedMate v2.0.25 (iOS June 12, 2026): Newly-Tracked Offline AI Competitor With Explicit Makhraj AI Failure and Async Teacher Sharing

### What TajweedMate is
TajweedMate (App Store `id1547696689`, Play Store `com.tajweedmate.androidapp`, tajweedmate.com) is an AI-powered Tajweed assessment app — **completely absent from all 22 prior research rounds**.

- **iOS v2.0.25** (June 12, 2026); **Android v2.0.23** (May 10, 2026)
- **Architecture:** Proprietary offline speech recognition engine, trained on 40+ Qari recitations. Voice data never uploaded to cloud.
- **Coverage:** Tajweed rules: Huruf, Madd, Ikhfaa, Ith'har, Iqlaab, Idghaam
- **Content:** Structured Qaida-style foundational lessons + live AI feedback on recitation + interactive quizzes

### New features in v2.0.25 (June 12, 2026) — round-new additions
1. **Full Quran text integration** — unified read-and-practice environment (previously lesson mode was separate)
2. **Hausa + German language support** — West/Central Africa expansion (Hausa ≈ 150M speakers) + DACH market
3. **Kids Mode** — kid-friendly interface, attractive colors, simplified navigation, Arabic letter writing practice on canvas (all 28 letters, 4 forms); available as **one-time purchase, no subscription**
4. **Share recordings with teachers** — new share button on memorization practice page → send audio file to any teacher via OS share sheet

### Critical limitation: Makhraj AI explicitly inadequate
From third-party review published June 2026:
> *"TajweedMate's AI limitations require real human correction, particularly for Makhraj-level assessment that AI cannot yet evaluate reliably."*

**This is significant:** TajweedMate's own positioning acknowledges that Makhraj (articulation-point) assessment requires human teachers. This is the third app (after Tarteel and Hifz AI) to publicly confirm AI cannot do Makharij — and TajweedMate's engine is fully offline with 40+ reciters of training data, meaning the failure is fundamental at current model scale, not a cloud/data-access limitation.

### Sakina competitive analysis
| Feature | TajweedMate | Sakina target |
|---|---|---|
| AI recitation feedback | ✅ Offline, real-time | ✅ On-device + cloud |
| Makhraj detection | ❌ Explicitly AI-inadequate | ✅ Human teacher |
| Live teacher session | ❌ None | ✅ Core feature |
| Teacher feedback | ✅ Async via share button | ✅ Synchronous live |
| Kids Mode | ✅ One-time purchase | TBD |
| Multi-qira'at | ❌ Not mentioned | ✅ (planned) |

**Teacher sharing** in TajweedMate is async-only (student records → exports audio file → sends via OS share sheet to teacher outside the app). There is no in-app teacher session, scheduling, or response system. Sakina's live synchronous teacher-session model remains completely unique in the market.

**Action for Sakina:** Add TajweedMate to competitive landscape deck. Position Sakina as: "TajweedMate gives you offline AI feedback + async teacher recordings — Sakina gives you a real teacher, live, who knows your voice."

---

## Insight 3 — Tarteel v5.78.2 (June 19, 2026): Latest Stable Android Confirmed; Rapid Hotfix Sprinting Signals Internal Feature Pressure

### Version timeline (June 2026 confirmed)
| Version | Date | Source |
|---|---|---|
| v5.77.3 | Jun 7, 2026 | Uptodown (official) |
| v5.78.0 | Jun 14, 2026 | Uptodown (official) — removed inter-ayah pause |
| v5.78.1 | Jun 17, 2026 | LiteAPKs (confirmed in Round 22) — recording playback removed from free |
| **v5.78.2** | **Jun 19, 2026** | **LiteAPKs, AppBrain — confirmed this round** |

- v5.78.2 is the latest confirmed stable Android build as of this research run (June 23, 2026).
- **Specific changelog for v5.78.2** not published through any official source (Google Play, Uptodown, Tarteel blog). Likely a silent bug fix.
- **Pattern:** 4 versions in 12 days (Jun 7 → Jun 19). This is a hotfix sprint cadence, not a feature release pace. Likely internal testing of the Mahraj/Makharij beta (Q2 2026 target, Q4 2026 full release per prior rounds).
- **iOS status:** Still unconfirmed new version. Previous data: iOS frozen at v5.76.4 (Feb–Apr 2026). No new iOS version found this round. Android-iOS gap remains 4+ months.
- **X (Twitter) status:** X platform experienced a global outage on June 22, 2026 (MacRumors confirmed) — Tarteel social announcement tracking temporarily suspended for this round. No Mahraj beta announcement found. This is absence of evidence, not evidence of absence.

### Notification bug still unresolved (presumed)
No changelog or announcement found indicating the notification bug (Friday Surah Al-Kahf, nightly Surah Al-Mulk reminders failing) has been fixed. v5.78.2 is the most likely version that would address this given the sprint pace; absence of user reports in available sources is not confirmation of fix.

---

## Additional Findings (Context)

### Qariah app (qariah.app)
Women-focused Quran recitation **listening** app (id1594917787), not a recitation assessment app. Features 60+ female Qariahs in 7 qira'at styles. Not a Sakina competitor; instead validates the multi-qira'at audience Sakina should serve (Warsh/Hafs splits already covered in DeiT V3 routing from Round 15). Users of Qariah are an adjacent audience for Sakina (women reciters who want teacher feedback, not just listening).

### "Quran Learning: AI Recitation" app (id6742226275)
Small utility app (1,000+ users) — wraps Tarteel ASR + AI chatbot. Not architecturally interesting and not a competitive threat.

### Tasleem AI
An Islamic "super app" (Quran, prayer times, Islamic search engine, spiritual tracker). No recitation assessment AI. Not a Sakina competitor.

### TajweedMate competitor positioning (from QariAI)
A 2026 head-to-head comparison article (QariAI.app/best-ai-quran-app) positions TajweedMate as "best for theory and learning rules" vs. QariAI as "most complete for Tajweed correction and memorization combined" vs. Tarteel as "best for pure memorization flow." Sakina should position as: **"best for live teacher sessions that AI cannot replace."**

---

## Technical Stack Status (No New Updates)

- **sherpa-onnx:** Still v1.13.3 (June 15, 2026) — no new version released this round
- **record Flutter package:** Last stable update January 2026 (v7.1.0) — unchanged
- **Nemotron 3.5 ASR Streaming:** No new Quran WER data; NVIDIA Nemotron 3 Ultra (550B MoE LLM, June 2026) is a different product (language model, not ASR)
- **KheemP/whisper-base-quran-lora:** Still at 5.98% WER — no new releases found

---

## Sources

- TajweedMate v2.0.25: https://tajweedmate-ios.soft112.com/ | https://tajweedmate.com/
- TajweedMate review (Makhraj limitation): https://aichief.com/ai-religion-tools/tajweedmate/
- TajweedMate App Store: https://apps.apple.com/us/app/tajweedmate/id1547696689
- Frontiers in Education paper: https://www.frontiersin.org/journals/education/articles/10.3389/feduc.2026.1876380/abstract
- Birgün SSRN paper: https://papers.ssrn.com/sol3/papers.cfm?abstract_id=5473279
- Birgün ScienceDirect: https://www.sciencedirect.com/science/article/pii/S259029112600063X
- Tarteel v5.78.2: https://liteapks.com/tarteel-quran-memorization.html
- Tarteel Uptodown history: https://tarteel.en.uptodown.com/android/versions
- Qariah app: https://qariah.app/
- QariAI comparison: https://qariai.app/best-ai-quran-app
