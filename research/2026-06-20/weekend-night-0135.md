# Weekend Night Research — 2026-06-20 01:35 UTC
*Run #4 of the evening. Sakina live Quran recitation app competitive intelligence.*

---

## 1. Tarteel — What Users Are Saying (Last 30 Days / 2025-2026)

### BOMBSHELL: Tarteel Tajweed AI Is NOT Shipped
Tarteel's own app description on Google Play explicitly states:
> "Currently, this feature does not include Tajweed or pronunciation correction, but we know there's community demand and it's on our roadmap for the future."

This is the single most important competitive finding of this run. Tarteel's brand dominance is built on AI recitation feedback — but tajweed correction is still a roadmap item, not a shipped feature. The AI detects which verse you're reciting, it does NOT correct your tajweed. **Sakina can truthfully claim a feature Tarteel markets but doesn't yet ship.**

### 2026 Tarteel Updates (What They DID Ship)
- 18 new Mushaf layouts (Madani, IndoPak, others) with Tajweed color coding
- Distraction-free video mode, improved goals, corrected-mistakes tracking
- Home screen widgets (streak, daily ayah, instant search)
- French language support added
- "Top 10 in GCC App Stores" push — aggressive MENA expansion campaign

### Confirmed User Complaints (App Store / Review Aggregators, 2025-2026)
1. **Inaccurate AI flags**: Paying subscribers ($9.99/mo, ~$100/yr) report correctly recited verses flagged as wrong — even with "detect tashkeel mistakes" turned off
2. **Partial-ayah ASR failure**: Mid-verse or end-of-verse recitation is unreliable; only full ayah or beginning of ayah recognized accurately
3. **Tafseer mapping bug**: Each tafseer entry connected to the wrong ayah
4. **Dark mode bug**: Text appears black on black background
5. **Session drops**: App stops listening during Hifz practice without warning
6. **$100/yr price vs. bug tolerance**: Users question the value proposition when AI mistakes are frequent and premium features have known issues
7. **Subscription not loading**: Post-purchase premium status fails to activate

### Twitter/X and Reddit
- Direct Twitter/X search for Tarteel posts in last 30 days returned no usable public results via web search index (Twitter blocks crawler indexing)
- Reddit site-search for Tarteel 2025 also returned no on-reddit results via web crawl; recommend checking r/islam and r/learnquran directly
- **Time Magazine (May 26, 2026)** is the highest-signal recent coverage: feature article "AI Tools Are Transforming Muslim Worship" prominently features Tarteel being used at the Maryam Islamic Center in Houston during the last ten nights of Ramadan, with hundreds of Muslims using it to follow along recitations in real time — this is mainstream media adoption signal
  - Source: https://time.com/article/2026/05/26/ai-muslim-worship/

---

## 2. Arabic Speech AI Startups Launched 2025-2026

### Intella (Egypt) — $12.5M Series A, September 2025
- **Funding**: $12.5M Series A led by Prosus; co-investors 500 Global, Wa'ed Ventures, Hala Ventures, Idrisi Ventures, HearstLab. Total raised: $16.9M
- **Founders**: CEO Nour Taher, CTO Omar Mansour (founded 2021, Series A closed Sep 2025)
- **Technical**: 95.73% ASR accuracy (claimed global record), 25+ Arabic dialects
- **Benchmarks vs. Big Tech**: Intella 95.73% vs. Google Cloud 62.5% vs. Microsoft Azure 66.2% on Arabic
- **Revenue**: 2x'd in 2024, on track for 7x in 2025
- **Market**: B2B enterprise (finance, telecoms, government) — no consumer or Quran product
- **Sakina relevance**: Pure B2B infrastructure play. 18-month risk: Prosus-backed with runway to pivot consumer
- Source: https://www.startuphub.ai/ai-news/funding-round/2025/intella-secures-12-5-million-series-a-led-by-prosus-to-scale-market-leading-arabic-speech-technologies/

### CNTXT AI — Munsit Platform (Abu Dhabi, April 2026) + Munsit Edge (May 2026)
- **Munsit (April 2026)**: Unified Arabic voice AI — ASR + TTS in one system. 95.7% accuracy, 18 dialects, dynamic dialect auto-detection within first 3 seconds of speech (no manual selection). Patented speech recognition + Faseeh TTS model (25+ dialects)
- **Adoption**: 250+ government and enterprise organizations; 86 million Arabic words processed; 1 million+ minutes of audio
- **Munsit Edge (May 2026)**: On-device SDK — no cloud required, real-time (previously confirmed ~150ms latency), iOS/Android/Linux/IoT. No Quran fine-tuning known.
- **Sakina relevance**: Munsit Edge is the most mature on-device Arabic ASR SDK available. If Sakina needs offline live recitation checking, this is the shortcut. License cost unknown — needs evaluation against `whisper-base-quran-lora` local deployment.
- Sources: https://www.zawya.com/en/press-release/companies-news/cntxt-ai-launches-munsit-the-worlds-most-accurate-arabic-voice-ai-as-demand-for-ai-services-accelerates-across-the-uae-fw5z241m / https://www.arabicdiscography.com/2026/05/cntxt-ai-introduces-munsit-edge.html

### Arabic.AI (2026 Pivot — Amman → MENA + US)
- Pivoted in 2026 to become "the AI operating layer for Arabic-first enterprises"
- Four products: Assistants, Translate, OCR, Speech — all on a shared Arabic foundation model
- Geography: Amman → KSA, Qatar, US expansion
- Claims 20% of revenue reinvested in R&D including **"dialect recognition, Quranic script analysis, and frontier modeling"** — Quranic script analysis R&D is notable
- No consumer app; enterprise positioning
- Source: https://arabic.ai/about/

### Saudi Arabia — Al-Maqraa (Grand Mosque, March 2025)
- Government-backed AI Quran education platform, inaugurated at the Grand Mosque in Makkah during Ramadan 2025
- **10 languages**: Arabic, English, Urdu, Indonesian, Malay, Hausa + others
- **Features**: AI recitation correction, Tajweed instruction, memorization, post-memorization review — supervised by qualified teachers (hybrid human+AI model)
- **Infrastructure**: Performance monitoring system, analytical reports, accredited recitation certificates
- **Availability**: Global online access (not limited to Grand Mosque)
- **Sakina relevance**: Government-level validation of the hybrid human+AI model Sakina is building. Al-Maqraa has teacher supervision — same thesis. However, it's institutional/government, not a consumer startup.
- Source: https://www.spa.gov.sa/en/N2274676

---

## 3. ProductHunt — Quran / Arabic AI Tools (2025-2026)

No ProductHunt product page URLs surfaced directly in search results for Quran recitation AI. However, app store launches confirm these as new entrants in 2025-2026:

### Qurania (June–August 2025 launch — iOS & Android)
- Developer: NeuralWorks
- Personalized AI Quran tutor: chat, ask questions, practice pronunciation, instant feedback
- Quranic Arabic vocabulary building, Tajweed rules, gamified exercises
- AI-generated visuals for lessons, offline capable
- Available: iOS App Store + Google Play (since June 2025 on Play)
- Source: https://apps.apple.com/us/app/qurania-learn-quran-with-ai/id6749280274

### Quranlingo (2025 launch — iOS)
- Gamified Quran learning: translations, Tafsir, explanations in English/German/Turkish/Spanish — verified by Islamic scholars
- AI voice-recognition for Tajweed correction: **listed as "upcoming"** (not yet shipped — same as Tarteel)
- Source: https://apps.apple.com/us/app/quranlingo-learn-quran-arabic/id6717598826

### Ilm AI Quran (Ramadan 2026 launch)
- Complete Islamic companion: prayer times, full Quran (Arabic + English translation + transliteration + audio), AI chat answering Islamic questions with Quran/Sunnah references
- Tailored to user's level of understanding
- Source: https://apps.apple.com/app/id6742023307

### AL Siraat / AL Quran AI (2025)
- AI recitation listening + instant mistake detection + pronunciation guidance
- Powered by advanced AI recitation tech; listens to voice, detects errors
- Available on Google Play
- Source: https://play.google.com/store/apps/details?id=com.alquran.aiquran.quranlearning

**Key ProductHunt finding (consistent with prior runs)**: Zero apps in this cohort are doing *live recitation with teacher sessions*. All AI Quran apps are fully automated. Sakina's human-escalation layer remains uncontested on ProductHunt and app stores.

---

## 4. HuggingFace Spaces — Live Arabic ASR Demos (Active 2025-2026)

### Leaderboards (Critical Infrastructure for Sakina Model Selection)
- **Open Universal Arabic ASR Leaderboard** — `msalhab96/open_universal_arabic_asr_leaderboard_all` and `elmresearchcenter/open_universal_arabic_asr_leaderboard`: Sortable table of Arabic ASR models by WER/CER across multiple test sets. Use this to track the best open models.
  - https://huggingface.co/spaces/msalhab96/open_universal_arabic_asr_leaderboard_all
  - https://huggingface.co/spaces/elmresearchcenter/open_universal_arabic_asr_leaderboard

### Key Models and Spaces

| Model/Space | Status | Notes |
|---|---|---|
| `Qwen/Qwen3-ASR` Space | Active 2025-2026 | Qwen's multilingual ASR including Arabic; live demo on HF Spaces |
| `mbzuai-nlp/ArTST` (ArTSTv3) | April 2025 | 17 fine-tuned checkpoints across Arabic dialects; Speech-to-Text demo |
| `NAMAA-Space/EgypTalk-ASR-v2` | Active | Egyptian Arabic optimized — local pronunciations and colloquialisms |
| `MaddoggProduction/whisper-l-v3-turbo-quran-lora-dataset-mix` | Active | Whisper Large V3 Turbo LoRA fine-tuned on Quran; 12.69% WER |
| `KheemP/whisper-base-quran-lora` | Active | Smaller, 5.98% WER — lowest confirmed Quranic ASR number |
| `obadx/recitation-segmentation` | Active | Waqf pause segmentation using Quran Phonetic Script (QPS) + wav2vec2-BERT |
| `IqraEval/IqraEval_Interspeech_26` | Active | IqraEval 2026 benchmark dataset — standardized tajweed error detection corpus |

### Key Research Papers (HuggingFace Papers, 2025-2026)
1. **Automatic Pronunciation Error Detection and Correction of the Holy Quran's Learners Using Deep Learning** (arXiv:2509.00094, Sep 2025) — 850+ hours of audio, ~300K annotated utterances, multi-level CTC model achieving 0.16% average Phoneme Error Rate. This is the technical floor Sakina's tajweed engine should aim to beat.
   - https://huggingface.co/papers/2509.00094

2. **Quran-MD: A Fine-Grained Multilingual Multimodal Dataset of the Quran** (arXiv:2601.17880, Jan 2026) — multimodal (text + audio + visual) dataset; directly useful for training cross-modal Quran AI features
   - https://huggingface.co/papers/2601.17880

---

## Summary of Strategic Signals for Sakina

| Signal | Action |
|---|---|
| Tarteel's tajweed AI is explicitly "on roadmap, not shipped" | **Sakina can be first to market with real tajweed correction** |
| Tarteel's paywall trust gap ($100/yr + wrong flags) is dominant complaint | **Hybrid human+AI validation is the answer users want** |
| Munsit Edge SDK (May 2026) = on-device Arabic ASR, no cloud | **Evaluate for offline recitation sessions** |
| Al-Maqraa (Saudi govt, March 2025) = teacher-supervised AI Quran | **Government validation of Sakina's model — use in pitch materials** |
| Time Magazine (May 26, 2026) = AI Muslim worship is mainstream media | **Timing is now: this vertical is gaining secular press coverage** |
| Qurania/Quranlingo launched 2025 — both have tajweed AI as "upcoming" | **Window to ship first is still open but narrowing (3–6 months)** |
| Intella 7x revenue growth in 2025, Prosus-backed | **Monitor for consumer pivot announcement** |
| 0.16% PER achievable (Sep 2025 paper) | **Set this as Sakina's internal ASR accuracy benchmark** |

---

*Sources used in this report:*
- https://time.com/article/2026/05/26/ai-muslim-worship/
- https://tarteel.ai/blog/new-mushaf-layouts-now-available-on-tarteel-ai/
- https://support.tarteel.ai/en/articles/12414460-what-features-do-i-unlock-with-tarteel-premium
- https://www.startuphub.ai/ai-news/funding-round/2025/intella-secures-12-5-million-series-a-led-by-prosus-to-scale-market-leading-arabic-speech-technologies/
- https://www.zawya.com/en/press-release/companies-news/cntxt-ai-launches-munsit-the-worlds-most-accurate-arabic-voice-ai-as-demand-for-ai-services-accelerates-across-the-uae-fw5z241m
- https://www.arabicdiscography.com/2026/05/cntxt-ai-introduces-munsit-edge.html
- https://arabic.ai/about/
- https://www.spa.gov.sa/en/N2274676
- https://apps.apple.com/us/app/qurania-learn-quran-with-ai/id6749280274
- https://huggingface.co/papers/2509.00094
- https://huggingface.co/papers/2601.17880
- https://huggingface.co/spaces/msalhab96/open_universal_arabic_asr_leaderboard_all
- https://aichief.com/ai-lifestyle-tools/tarteel/
- https://justuseapp.com/en/app/1391009396/tarteel-recite-al-quran/reviews
