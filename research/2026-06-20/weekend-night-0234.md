# Sakina Research — Weekend Night Session
**Date:** 2026-06-20 | **Run:** 02:34 UTC

---

## 1. Twitter/X — What Users Are Saying About Tarteel (Last 30 Days)

### Official Channel Activity
- @tarteelAI posted: **"Tarteel is now top 10 in GCC App Stores!"** and is calling on users to push it to #1, outranking games and social media. Active GCC expansion campaign underway (~June 2026).
- Earlier in 2026: Free premium access offered during Sha'ban (Arabic month before Ramadan) — user acquisition push via temporary paywall drop.

### User Sentiment on X
Direct negative user posts were not surfaced in the crawl period. The app's organic mentions tend to be recommendation posts (e.g., Indonesian user recommending the "AI voice search" feature). No viral complaint threads found in the 30-day window.

**Bottom line:** Tarteel's X presence is largely official/promotional. The real complaint signal lives on app store reviews and Reddit, not X.

---

## 2. Reddit r/islam + r/learnquran — Tarteel Complaints (2025–2026)

Reddit search returned no direct threads — likely blocked by Reddit's anti-bot crawler policy. However, aggregated review data from JustUseApp (which crawls App Store reviews) confirms the community consensus:

### Confirmed Complaint Categories (2025–2026):
1. **Partial-ayah ASR failure** — Reciting from the middle or end of an ayah gives inaccurate or no recognition. Only full-ayah or beginning-of-ayah works reliably.
2. **Bismillah misidentification bug** — App snaps to Surat Al-Fatiha whenever any surah starts with Bismillah, incorrectly flagging it as a mistake.
3. **Paywall trust breakdown** — Premium subscribers ($100/yr) see their recitation incorrectly flagged as wrong. Paying users report subscription status fails to load post-purchase.
4. **No tajweed/pronunciation correction** — Tarteel explicitly states: *"Currently, this feature does not include Tajweed or pronunciation correction, but we know there's community demand and it's on our roadmap."* This gap is confirmed active as of 2026.
5. **Lagging and unexpected crashes** — Recurring performance issues cited across both iOS and Android.

### Community Tone
Despite complaints, many users call Tarteel "indispensable" for consistent practice. The trust gap is specifically about premium: users who pay expect accuracy that doesn't exist yet.

---

## 3. Arabic Speech AI Startups — 2025–2026 Launches

### A. Intella (Egypt) — $12.5M Series A, Sep 2025
- **Investors:** Prosus (led)
- **Tech:** 95.73% accuracy, 25+ Arabic dialects, enterprise transcription + AI customer engagement
- **Status:** B2B only; no Quran-specific fine-tuning; no consumer product
- **Risk to Sakina:** Funded runway to pivot to consumer in 12–18 months

### B. CNTXT AI — Munsit (Abu Dhabi), April 2026
- **Accuracy:** 95.7% across 18 dialects
- **Key feature:** Munsit Edge (May 2026) — on-device SDK, no cloud, iOS/Android/Linux/IoT, ~150ms latency
- **Status:** B2B/government; no Quran fine-tuning
- **Risk to Sakina:** On-device SDK is publicly available — a Quran app competitor could integrate it

### C. Arabic.AI (UAE/Jordan) — Agentic Platform Launch, Apr 2025 → Jan 2026 Scale
- **Backed by:** Tarjama ($15M raise to scale Arabic.AI)
- **Features:** Arabic LLM (Pronoia), 22-dialect STT, Arabic TTS, Arabic OCR, enterprise AI agents
- **Partnership (April 2026):** Arabic.AI + HeyBreez — production-grade Arabic voice AI for enterprises/governments
- **Status:** Enterprise-only; no Quran consumer vertical; no Quran data fine-tuning
- **Risk level:** Low (enterprise pivot unlikely fast); useful as potential infrastructure partner

### D. HeyBreez (partner to Arabic.AI, April 2026)
- Arabic voice AI for enterprise — integrated with Arabic.AI's stack
- Not consumer, not Quran-specific

---

## 4. ProductHunt — Quran/Arabic AI Tools (2025–2026)

### What Was Found
- **AlQuran Classes** — listed on ProductHunt; live human teaching sessions, no AI
- **Quran.com product updates** — ongoing (QuranReflect rebuilt Nov 2025 into social reflection platform; "Quran in a Year" enhanced Jan 2026)
- **Quran AI (quran-ai.app)** — AI-powered tafsir, reading plans, prayer times; offline; *text only, no recitation audio*
- **RecitID** — identified in past sessions (Google Play March 2026); 18s recitation ID + live khutbah translation; *not on ProductHunt yet*

### Confirmed Gap (Carries Forward from Prior Sessions)
**Zero live-audio recitation AI products have launched on ProductHunt in 2025–2026.** Text/tafsir/Q&A AI products dominate the Quran AI category on ProductHunt. Sakina would be first-mover in live recitation audio on the platform.

---

## 5. HuggingFace — Active Arabic ASR Spaces & Resources (2025–2026)

### Live Demo Spaces
| Space | Description | Status |
|---|---|---|
| `IqraEval/IqraEval_Interspeech_26` | IQRA 2026 Challenge — MSA mispronunciation detection (Quran-focused); 19 teams competed | **Active** |
| `IqraEval/Leaderboard` | Live leaderboard for Interspeech 2026 pronunciation challenge | **Active** |
| `IqraEval/SharedTask_ArabicNLP2025` | 2025 ArabicNLP shared task on Quranic pronunciation | **Active** |
| `elmresearchcenter/open_universal_arabic_asr_leaderboard` | Sortable Arabic ASR benchmark table (WER/CER across test sets) | **Active** |
| `Qwen/Qwen3-ASR` | Qwen3-ASR live demo (supports Arabic, 52 languages) | **Active** |
| `hf-audio/open_asr_leaderboard` | General open ASR leaderboard — includes Arabic track | **Active** |

### Key Datasets (2025–2026)
- **`Buraaq/quran-audio-text-dataset`** (Quran-MD) — multilingual multimodal Quran dataset; accepted NeurIPS 2025 Muslims in ML Workshop; enables ASR, tajweed detection, Quranic TTS tasks
- **`MohamedRashad/Quran-Recitations`** — curated recitation audio dataset
- **`MuazAhmad7/Surah_Ikhlas-Labeled_Dataset`** — labeled tajweed dataset for Surah Ikhlas
- **`obadx/recitation-segmenter-v2`** — waqf segmentation model, critical for live verse tracking (used in prior Sakina research)

### Key Models (2025–2026)
- **`KheemP/whisper-base-quran-lora`** — 5.98% WER (best confirmed Quranic ASR benchmark)
- **Qwen3-ASR 1.7B** — SOTA open ASR, 52 languages; used by IQRA 2026 teams for direct speech-to-phoneme generation (Kalimat team reframed MSA diacritization as speech-to-phoneme with Qwen3-ASR)
- **ArTSTv3 (MBZUAI)** — Arabic Text-Speech Transformer v3; multilingual pre-training (Arabic + EN/FR/ES); 17 fine-tuned dialect checkpoints released April 2025

### Critical New Paper
**arXiv:2603.29087** — *"IQRA 2026: Interspeech Challenge on Automatic Pronunciation Assessment for Modern Standard Arabic (MSA)"*
- Introduces **QuranMB.v2** — expanded Quranic mispronunciation benchmark (builds on QuranMB.v1 from 2025)
- Best system: +0.2787 F1 over baseline using generative large audio-language models (LALMs)
- Sakina should treat IQRA 2026 leaderboard as the official benchmark for tajweed error detection pipeline

---

## Summary of New Findings This Session

| # | Finding | Sakina Implication |
|---|---|---|
| 1 | **Tarteel's top 30-day X activity = GCC expansion push** — not product improvements | Sakina's time window is compressing in GCC market specifically |
| 2 | **Partial-ayah ASR and Bismillah bug still unresolved** (confirmed 2026) | These are product-level wedges: Sakina must get partial-verse tracking right from v1 |
| 3 | **Arabic.AI raised $15M + HeyBreez partnership (Apr 2026)** = well-funded Arabic voice infra | Evaluate Arabic.AI's speech SDK as potential Sakina backend vs. Munsit Edge vs. whisper-lora |
| 4 | **Qwen3-ASR 1.7B used in IQRA 2026 for Quran phoneme tasks** | Benchmark Qwen3-ASR against whisper-base-quran-lora; may surpass 5.98% WER |
| 5 | **QuranMB.v2 (arXiv:2603.29087) is the active benchmark** | Sakina's tajweed evaluation must use QuranMB.v2 to be comparable to SOTA |
| 6 | **HuggingFace Arabic ASR leaderboard is live and maintained** | Subscribe to `elmresearchcenter/open_universal_arabic_asr_leaderboard` for weekly model updates |
| 7 | **ProductHunt live-audio gap still unoccupied** | Sakina ProductHunt launch remains a first-mover opportunity in this category |

---

## Sources

- [Tarteel X/Twitter (@tarteelAI)](https://x.com/tarteelAI)
- [Tarteel GCC Push Tweet](https://x.com/tarteelAI/status/1966619981660991975)
- [Tarteel Problems 2026 — JustUseApp](https://justuseapp.com/en/app/1391009396/tarteel-recite-al-quran/problems)
- [Tarteel Reviews 2026 — JustUseApp](https://justuseapp.com/en/app/1391009396/tarteel-recite-al-quran/reviews)
- [Tarteel Review 2026: AIChief](https://aichief.com/ai-lifestyle-tools/tarteel/)
- [Tarteel Help: App Not Working Well](https://support.tarteel.ai/en/collections/15105581-the-app-is-not-working-well)
- [Intella $12.5M Series A — StartupHub](https://www.startuphub.ai/ai-news/funding-round/2025/intella-secures-12-5-million-series-a-led-by-prosus-to-scale-market-leading-arabic-speech-technologies/)
- [CNTXT AI Munsit Launch — ZAWYA](https://www.zawya.com/en/press-release/companies-news/cntxt-ai-launches-munsit-the-worlds-most-accurate-arabic-voice-ai-as-demand-for-ai-services-accelerates-across-the-uae-t8szmgjm)
- [CNTXT AI acquires Actualize — Middle East AI News](https://www.middleeastainews.com/p/cntxt-ai-acquires-actualize-to-expand)
- [Arabic.AI — Tarjama $15M raise](https://arabic.ai/tarjama-raises-15-million-to-scale-arabic-ai-for-enterprise-use-in-mena/)
- [Arabic.AI + HeyBreez Partnership — Wamda](https://www.wamda.com/2026/04/arabicai-partners-heybreez-scale-arabic-voice-ai-enterprises)
- [Arabic.AI Agentic Platform — ArabFounders](https://arabfounders.net/en/arabic-ai-launches-agentic-arabic-platform-mena/)
- [Quran.com Product Updates](https://quran.com/product-updates)
- [ProductHunt: AlQuran Classes](https://www.producthunt.com/products/online-quran-classes)
- [RecitID — Best Quran App 2026 Guide](https://recitid.ai/guides/best-quran-app-2026)
- [IqraEval Interspeech 2026 HF Space](https://huggingface.co/spaces/IqraEval/IqraEval_Interspeech_26)
- [IqraEval Leaderboard HF Space](https://huggingface.co/spaces/IqraEval/Leaderboard)
- [Open Universal Arabic ASR Leaderboard](https://huggingface.co/spaces/elmresearchcenter/open_universal_arabic_asr_leaderboard)
- [Qwen3-ASR HF Space](https://huggingface.co/spaces/Qwen/Qwen3-ASR)
- [Qwen3-ASR Technical Report — arXiv:2601.21337](https://arxiv.org/html/2601.21337v1)
- [IQRA 2026 Challenge Paper — arXiv:2603.29087](https://arxiv.org/html/2603.29087)
- [Quran-MD Dataset — arXiv:2601.17880](https://arxiv.org/html/2601.17880v1)
- [Buraaq/quran-audio-text-dataset — HuggingFace](https://huggingface.co/datasets/Buraaq/quran-audio-text-dataset)
- [obadx/recitation-segmenter-v2 — HuggingFace](https://huggingface.co/obadx/recitation-segmenter-v2)
- [ArTST MBZUAI Collection — HuggingFace](https://huggingface.co/collections/MBZUAI/artst-arabic-text-speech-transformer)
- [Open ASR Leaderboard — HuggingFace](https://huggingface.co/spaces/hf-audio/open_asr_leaderboard)
- [Best Quran Memorization Apps 2026 — QuranicMa](https://quranicma.com/best-quran-memorization-apps-2026/)
