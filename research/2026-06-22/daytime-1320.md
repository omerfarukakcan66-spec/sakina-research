# Research Report — 2026-06-22 Daytime (13:20 UTC) — Round 14

**Focus:** Tarteel latest app state + new June 2026 competitors + arXiv/HuggingFace updates

---

## Critical Finding 1 — Tarteel "Hifz AI" (com.awa.hifz): Second Standalone App, On-Device ASR, Word-Level Correction, Still ZERO Tajweed

**What:** Tarteel has effectively split into two separate apps: the main Tarteel (com.mmmoussa.iqra) and "Hifz AI: Tarteel Quran" (`com.awa.hifz` / iOS id6748984824). Hifz AI was launched ~April 2026 (hifzai.app metadata date April 8, 2026).

**Developer:** Listed as "Muhammad Awais" on App Store, different package from main Tarteel but branded as "Tarteel Quran." Likely an official spin-off or close affiliate product.

**Key features confirmed:**
- **On-device ASR**: "Voice recordings stay on your device — never uploaded." All AI processing happens offline. This is confirmed production-grade on-device inference.
- **Word-level error highlighting**: Red (wrong) / Green (correct) per word in real time.
- **Self-Listening feature**: Record yourself → instantly play back your own recitation → compare to expert Qari (Maher Al-Muaiqly et al.).
- **Hifz + Tarteel merged**: One learning mode for both memorization and recitation tracking.
- **NO TAJWEED CORRECTION**: Explicitly stated — "currently does not provide detailed correction for Tajweed. However, it does offer Tajweed color-coding in its Mushaf layouts ... development of advanced AI for Tajweed correction is a stated future goal on their roadmap."

**Pricing:** Free tier (AI Follow Along + Custom Goals + Streaks). Premium: £7.50/month billed annually ≈ £90/year ≈ $115/year.

**Sakīna implication:** The tajweed correction gap is STILL OPEN in both Tarteel products. Hifz AI now competes directly for the Hifz user segment with on-device ASR and word-level feedback — but leaves the phoneme/tajweed-rule level entirely vacant. QariAI (full phoneme-level tajweed) + Sakīna (real-time teacher escalation for uncertain AI cases) remain the only paths to phoneme-level tajweed in consumer apps.

**Sources:** [Google Play](https://play.google.com/store/apps/details?id=com.awa.hifz), [App Store](https://apps.apple.com/us/app/hifz-ai-tarteel-quran/id6748984824), [hifzai.app](https://hifzai.app/)

---

## Critical Finding 2 — Tarteel Main App v5.78.0 (June 14, 2026): Fixed Android Inter-Ayah Pause; iOS Still Broken

**What:** Tarteel main app (com.mmmoussa.iqra) released v5.78.0 on June 14, 2026 for Android. The changelog confirms: removed the pauses between ayahs when listening to reciter audio.

**Version history summary (2026):**
| Version | Date | Platform | Change |
|---------|------|----------|--------|
| 5.78.0 | June 14, 2026 | Android | Removed inter-ayah audio pauses |
| 5.77.3 | May 30, 2026 | Android | Bug fixes, "Eid Mubarak!" messaging |
| 5.75.4 | Feb 28, 2026 | iOS | LAST iOS UPDATE — iOS still at this version |

**Sakīna implication:** The "between-ayah pause" competitive advantage identified in Round 5 has been ELIMINATED for Android users as of June 14, 2026. However, **iOS users (likely 40–50% of the market) still have the broken audio gap experience** — iOS is stuck on v5.75.4 from February. Sakīna's audio architecture (continuous HLS or WebRTC streaming) provides gapless playback on both platforms from Day 1 — this remains a differentiator for iOS users. The gap between iOS and Android Tarteel quality is itself a talking point ("Tarteel on Android was just fixed 8 days ago; iOS still broken").

**Sources:** [Uptodown version history](https://tarteel.en.uptodown.com/android/versions), [APKPure](https://apkpure.com/tarteel-quran-memorization/com.mmmoussa.iqra), [iqra.soft112.com](https://iqra.soft112.com/)

---

## Critical Finding 3 — Quranly.app: Live Teacher + AI Hybrid Already Live — Direct Sakīna Positioning Conflict

**What:** `quranly.app` ("The world's first habit-building Qur'an app") combines in-app AI practice with access to live Quran teachers, structured curriculum, and progress reports shared with teachers. Currently #3 competitor to Tarteel by web traffic (Similarweb, May 2026). Also: `quranica.com` (#1) and `qurantutor.ai` (#2) are Tarteel's top three web competitors.

**Quranly model:**
- Teacher-assisted AI tajweed feedback (not real-time AI autonomous correction)
- Teacher-scheduled spaced repetition (scheduled lesson sessions, not on-demand)
- Progress reports sent to teachers
- No offline mode (requires connectivity)
- More expensive than Tarteel due to teacher component

**Sakīna differentiation from Quranly:** Quranly does pre-scheduled teacher sessions — the teacher is your tutor for a booked lesson, not a real-time escalation target. Sakīna's model is: AI detects tajweed uncertainty mid-recitation → instant live teacher joins the session → confirms/corrects the specific moment. This is fundamentally different from "schedule a lesson with a teacher." The Sakīna model is asynchronous-to-synchronous escalation driven by AI confidence, not by user booking.

**QuranTutor.ai model:** Asynchronous review — Ustadz/Ustadzah provides feedback within 1×24 hours after reviewing recorded recitation. This is a quality-review queue, not live.

**Sakīna's unique position confirmed:** No app in the market currently does: AI detects uncertainty → escalate to live teacher IN THE SAME SESSION in real time. This is the open slot.

**Sources:** [Quranly.app](https://www.quranly.app/), [QariAI comparison page](https://qariai.app/apps-like-tarteel), [Alphazed 2026 comparison](https://www.thealphazed.com/blog/best-quran-memorization-apps-2026)

---

## Finding 4 — Riwaya-ID (NeurIPS 2025, Nov 2025): 82% Accuracy Warsh vs Hafs Detection from Audio

**Paper:** "Riwaya-ID: Towards ML-powered Identification of Qur'anic Recitation Style from Audio" — NeurIPS 2025, Muslims in ML Track. [OpenReview](https://openreview.net/forum?id=EkkiGLptOD)

**Method:** 700+ hours of Quranic recitations segmented into 12-second windows. Pretrained speech encoders (wav2vec2.0 + Whisper) used to extract frame-level embeddings → lightweight classifier predicts riwaya (Warsh vs Hafs).

**Result:** 82% prediction accuracy for Warsh vs Hafs identification from audio.

**Sakīna implication:** Most Quran ASR models are trained exclusively on Hafs (the dominant Egyptian riwaya). The 10–15% of the global Muslim population that uses Warsh (North Africa, West Africa) gets worse recognition accuracy — this is an underserved market. If Sakīna integrates Riwaya-ID as a preprocessing step, it can auto-detect the user's riwaya and route to the correct ASR model, offering superior accuracy for Warsh users that Tarteel/Hifz AI miss entirely. The Tadabur dataset (1,400h, 600+ reciters, CC BY-NC) likely includes multiple riwayas and can be used to train riwaya-specific fine-tunes.

---

## Finding 5 — IbrahimSalah/Wav2vecLarge_quran_syllables_recognition: First Public Quranic SYLLABLE Model on HuggingFace + Azure AI Foundry

**Model:** `IbrahimSalah/Wav2vecLarge_quran_syllables_recognition` — Wav2Vec2 Large fine-tuned for **syllable-level** (not word-level, not phoneme-level) Quranic speech recognition. Also available as a general Arabic version: `IbrahimSalah/Arabic_speech_Syllables_recognition_Using_Wav2vec2`.

**Availability:** HuggingFace + **Microsoft Azure AI Foundry** (REST API via AzureML endpoint, base64 or raw bytes payload). Google Colab demo available.

**Training:** Trained on private dataset + part of Tarteel EveryAyah dataset.

**Why this matters for Sakīna:** Syllable-level analysis is the missing layer between word-level ASR and phoneme-level tajweed. Specifically for **Madd detection** (prolonged vowels — one of the most common tajweed errors): a syllable model can measure the duration of the long vowel syllable and flag if it's too short. This is the approach taken by `MrConnect/Wartilho` for madd duration analysis. Pair with `TBOGamer22/wav2vec2-quran-phonetics` (phoneme output) for a three-level stack: word → syllable → phoneme.

**Source:** [HuggingFace](https://huggingface.co/IbrahimSalah/Wav2vecLarge_quran_syllables_recognition), [Azure AI Foundry](https://ai.azure.com/catalog/models/ibrahimsalah-arabic-speech-syllables-recognition-using-wav2vec2)

---

## Finding 6 — IqraEval Baseline Checkpoints Released: HuBERT/WavLM/Wav2vec2 Downloadable via Trikaldarshi/sws_pretrained_models

**Paper:** arXiv:2506.07722 (June 2025, updated v2 2026) — Interspeech 2025. [GitHub: Iqra-Eval/interspeech_IqraEval](https://github.com/Iqra-Eval/interspeech_IqraEval)

**What's downloadable:** Pre-trained checkpoints for Quranic pronunciation assessment:
- HuBERT Base
- WavLM Base
- Wav2vec2 Base
- mHuBERT

All at `Trikaldarshi/sws_pretrained_models` on HuggingFace. Training: CV-Ar trainset + TTS augmentation. Evaluation scripts: `align_data.py` + `metric.py` for phoneme alignment and error analysis (insertion/deletion/substitution rates).

**Sakīna implication:** These are PLUG-AND-PLAY baseline checkpoints for the exact task (Quranic pronunciation error detection). Download from `Trikaldarshi/sws_pretrained_models`, run `s3prl_inference.py` from the IqraEval repo, and get phoneme-level MDD scores immediately. These are the baselines the IQRA 2026 competition winner (whu-iasp, F1=0.7201) improved upon — they can serve as Sakīna's initial pronunciation quality layer before investing in the full IQRA 2026 champion architecture.

---

## Competitive Landscape Update (June 2026)

| App | On-Device ASR | Tajweed Correction | Live Teacher | Pricing |
|-----|--------------|-------------------|-------------|---------|
| Tarteel (main, v5.78.0 Android) | No | No (roadmap) | No | $100/yr |
| Hifz AI: Tarteel | **Yes** | No (roadmap) | No | £90/yr |
| QariAI | Unknown | **Yes (phoneme-level)** | No | Unknown |
| Quranly | No | Teacher-assisted | **Yes (scheduled)** | Premium |
| QuranTutor.ai | No | Teacher review | Async (24h) | Unknown |
| RecitID | Unknown | No (verse ID only) | No | Unknown |
| **Sakīna (target)** | **Yes** | **Yes (AI+teacher)** | **Yes (real-time escalation)** | TBD |

**The gap:** No app combines on-device ASR + real-time tajweed + instant live teacher escalation in one product. This remains Sakīna's unclaimed position.

---

## Action Items from This Round

1. **iOS Tarteel gap**: Position Sakīna's seamless gapless audio explicitly as "works on iOS on Day 1" — Tarteel's iOS hasn't been updated since Feb 28, 2026 and still has the ayah-pause bug. Target iOS users specifically in early marketing.

2. **Riwaya-ID integration**: Implement a Riwaya auto-detection preprocessing step using the NeurIPS 2025 model (82% Warsh/Hafs accuracy). This gives Sakīna superior performance for North African and West African users that Tarteel misses entirely. A clear differentiation for the 10-15% of Muslims using Warsh.

3. **IqraEval baselines**: Download `Trikaldarshi/sws_pretrained_models` (HuBERT/WavLM/Wav2vec2) and run the IqraEval evaluation pipeline. This is a free starting point for Sakīna's pronunciation quality layer — zero custom training needed to have a working tajweed assessment baseline.

4. **Syllable model for Madd**: Integrate `IbrahimSalah/Wav2vecLarge_quran_syllables_recognition` for Madd duration measurement (too-short long vowels). Azure AI Foundry endpoint provides an instant REST API before you need to self-host.

5. **Competitor monitoring**: Track Quranly's pricing page and user reviews — if they're already doing scheduled teacher + AI, their user feedback reveals what works and what doesn't for the hybrid model.
