# Sakina Research — 2026-06-22 Night (23:31 UTC) — Round 21

**Focus:** Fanar 2.0 Aura-STT-LF · KSAA-2026 CATT-Whisper Diacritized ASR · Tarteel version audit · Open Arabic ASR Leaderboard status

---

## Summary of New Findings

### 1 — QCRI Fanar 2.0 (arXiv:2603.16397, Mar 2026): Aura-STT-LF Is the First Arabic Long-Form ASR Model + Fanar-Sadiq Routes Islamic Queries to Quran Retrieval

**Qatar Computing Research Institute (QCRI / HBKU)** released Fanar 2.0 in March 2026 — a fully Arabic-centric AI stack operated entirely in Qatar. For Sakīna two components are directly relevant:

**Aura-STT-LF** — the first Arabic-centric bilingual (Arabic + English code-switching) **long-form ASR model**, designed for hours-long recordings. Key capabilities:
- Handles **speaker-change robustness** across continuous audio
- Includes `Aura-STT-LF-Styler`: a readability restoration layer that re-inserts punctuation and Arabic orthographic conventions (including diacritics) into the transcript
- Comes with **Aura-STT-BenchLF** — the first publicly available Arabic long-form ASR benchmark (document-level transcripts, segment boundaries, paralinguistic event labels)
- On HuggingFace under the `QCRI` org

**Fanar-Sadiq** — a multi-agent Islamic content router that dispatches to: Fiqh reasoning, Quranic retrieval, du'a lookup, zakat/inheritance calculation, Hijri calendar, prayer times. This is a reference architecture for Sakīna's "AI routing to teacher escalation" — Fanar-Sadiq shows QCRI already solved query-type classification in Arabic at government scale.

**Limitations for Sakīna direct use:** Aura-STT-LF is trained on MSA formal audio (meetings, lectures, podcasts, media) — not Quranic Classical Arabic with tajweed. It is NOT a drop-in replacement for KheemP or Moonshine Arabic on Quran text. Value: (a) use Aura-STT-BenchLF to understand where Sakīna's models fail on long sessions; (b) study Aura-STT-LF-Styler architecture for the diacritization post-processing layer idea; (c) Fanar-Sadiq architecture as a multi-agent Islamic routing template.

**HuggingFace:** `QCRI/Fanar-2-27B-Instruct`, `QCRI/Fanar-2-Oryx-IVU` (vision). Aura-STT-LF model page not yet confirmed public — check `QCRI` org on HuggingFace.

---

### 2 — KSAA-2026 Shared Task: CATT-Whisper Wins Diacritized Arabic ASR at 23.26% WER — New Architecture for Tashkeel-Preserving Speech Output

**arXiv:2605.25928** — "Thaka at KSAA-2026 Task 2: Regularized Fine-Tuning for Arabic Speech Diacritization" — **1st place** at the King Salman Global Academy (KSAA) 2026 Shared Task on Arabic Speech Dictation with Automatic Diacritization. Presented at **OSACT7 @ LREC 2026** (Palma de Mallorca, June 2026).

**Architecture: CATT-Whisper** — a character-level multimodal model combining:
- **Pretrained CATT text encoder** (character-aware Arabic text model) for diacritization context
- **Frozen Whisper speech encoder** for acoustic features
- Character-level decoder for diacritized (tashkeel) Arabic output

**Training tricks:**
- R-Drop consistency regularization
- Optuna-optimized hyperparameters (high weight decay)
- Focal Loss
- Inference: average 200 stochastic forward passes across 4 model checkpoints using Monte Carlo Dropout

**Result: 23.26% WER** on diacritized speech benchmark — 1st place.

**Why this matters for Sakīna:** This is the second public architecture (alongside MaddoggProduction's LoRA at 12.69% WER) proven to output **fully diacritized (tashkeel-preserved) Arabic from speech**. The CATT text encoder branch provides morphological diacritization context that pure acoustic models (Whisper, Moonshine) lack. For the tajweed diff pipeline:

`Speech → CATT-Whisper → diacritized transcript → char-diff vs expected Quran text → flag missing/wrong harakāt → map to tajweed rule`

This is more principled than MaddoggProduction (which uses LoRA on Whisper alone with no dedicated diacritization architecture). **Sakīna action:** Locate CATT model on HuggingFace; test CATT-Whisper on 20 Quran clips from `Buraaq/quran-audio-text-dataset`; compare diacritized WER vs. MaddoggProduction 12.69%.

**arXiv link:** https://arxiv.org/abs/2605.25928

---

### 3 — Tarteel Version Audit: v5.85.1 Source Downgraded to Unverified Mod-APK — Official Public Track Is v5.77.3 (Android) + iOS Stalled at v5.76.4

Previous round (Round 20) presented Tarteel v5.85.1 and "8 minor versions in 2 weeks" as confirmed. **This round's verification shows:**

- **Android (Google Play / official sources):** v5.77.3 — June 7, 2026. This is still the latest confirmed official Android release.
- **Android (Uptodown):** v5.76.4 is the latest mirrored there (Uptodown lags official Play Store by 1–2 versions typically).
- **v5.85.1 appears only on mod APK aggregator sites** (getmodpc.net, apktodo.io). These sites sometimes pull beta/internal track APKs or fabricate version numbers — this source is **unverified**.
- **iOS:** Still at or around v5.75.4 (last confirmed Feb 28, 2026). No new iOS release found in this round.

**Updated competitive picture:** Tarteel's Mahraj/Makharij detection beta is still described as "Q2 2026 beta testing, Q4 2026 full release" — the v5.85.1 claim may have been premature. The core finding stands: **Tarteel iOS is genuinely stalled (~4 months behind Android)** and tajweed correction still unshipped on both platforms. The Q4 2026 deadline for Sakīna to ship first remains valid.

**Corrected Round 20 Insight 3:** Replace "v5.85.1 confirmed" with "v5.85.1 from mod APK aggregator only — treat as unverified beta signal, not confirmed release."

---

## Additional Supporting Finds

### Open Universal Arabic ASR Leaderboard (as of Aug 2025)

Full ranking context from arXiv:2412.13788:
- **#1: NVIDIA Conformer-CTC-large-Arabic + 4-gram LM** → 25.71% Avg WER, 10.02% Avg CER
- **#3: Whisper large v3** → 36.86% Avg WER
- Nemotron 3.5 streaming and FastConformer hybrid not yet on this leaderboard (added after Aug 2025 cutoff)

This leaderboard covers **dialectal + MSA Arabic** — it does NOT include Quranic Classical Arabic as a test domain. Models that lead on this leaderboard (Conformer-CTC) are explicitly NOT the same models that lead on Quranic WER (KheemP: 5.98%). The distinction is critical for any Sakīna model selection argument.

### IQRA 2026 Full Challenge Stats (arXiv:2603.29087)

- 19 teams participated
- Best system (whu-iasp): F1=0.7201 on QuranMB.v2
- Organizer baseline: F1=0.4414
- 13 of 19 systems surpassed baseline
- whu-iasp used wav2vec2-xls-r-300m + TCN + CTC (confirmed again)
- Kalimat (6th place, F1=0.6702): used Qwen3-ASR 1.7B generative approach
- Challenge dataset: `Iqra_Extra_IS26` — first real human mispronounced MSA speech corpus

### Tadabur Paper Now Formally on arXiv (arXiv:2604.18932, Apr 2026)

The Tadabur dataset paper is now formally published on arXiv. Confirmed: 1,400+ hours, 600+ reciters, CC BY-NC 4.0, fine-tuned Whisper-Small model. **Tadabur-Whisper-Small is now on HuggingFace** (`FaisaI/tadabur` org) — no WER published yet. This is the next benchmarking priority after Nemotron 3.5 Arabic testing (from Round 18).

---

## Sakina Action Items (New This Round)

1. **Find CATT-Whisper / Thaka model** on HuggingFace (search `CATT-Whisper` or `KSAA-2026`). Test on 20 Quran clips. Compare diacritized WER vs. MaddoggProduction 12.69%. If better, adopt CATT-Whisper as the tashkeel-output ASR branch.

2. **Check QCRI HuggingFace org** for Aura-STT-LF model card and license. Evaluate whether Aura-STT-LF-Styler's diacritization restoration approach is extractable independently of the full long-form model.

3. **Update competitive tracker:** Remove v5.85.1 as a confirmed milestone. Tarteel's latest confirmed public version is v5.77.3 (Android June 7, 2026). Mahraj beta is still Q2 target — confirm whether it shipped in v5.77.3 changelog.

4. **Tadabur-Whisper-Small benchmark:** Now available on HuggingFace. Run on standard Quran test set (10-20 clips, multiple reciters) vs. KheemP (5.98% WER) to determine if Tadabur's 3–4× larger training set delivers a WER improvement.

---

## Sources

- [Fanar 2.0: Arabic Generative AI Stack (arXiv:2603.16397)](https://arxiv.org/abs/2603.16397)
- [QCRI HuggingFace org](https://huggingface.co/QCRI)
- [Thaka KSAA-2026 (arXiv:2605.25928)](https://arxiv.org/abs/2605.25928)
- [KSAA-2026 Shared Task (Codabench)](https://www.codabench.org/competitions/11859/)
- [Tarteel on Uptodown (version history)](https://tarteel.en.uptodown.com/android/versions)
- [Tarteel v5.77.3 on soft112](https://iqra.soft112.com/)
- [Open Universal Arabic ASR Leaderboard](https://arxiv.org/abs/2412.13788)
- [IQRA 2026 Challenge Paper (arXiv:2603.29087)](https://arxiv.org/abs/2603.29087)
- [Tadabur paper (arXiv:2604.18932)](https://arxiv.org/abs/2604.18932)
- [Tadabur GitHub](https://github.com/fherran/tadabur)
