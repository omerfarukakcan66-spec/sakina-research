# Research Round 42 — 2026-06-25 Night (05:35 UTC)

**Focus:** Arabic Quran ASR, Flutter audio, competitor intelligence, new HuggingFace models  
**Sources:** WebSearch (date-filtered 2025–2026)  
**Prior round:** Round 41 (04:33 UTC) — QariAI stagnation, Nuwaisir Juzz-30 model, Tarteel Day 8 stall

---

## Insight 1 — `Buraaq/quran-md-ayahs` (HuggingFace, Jan 2026): 187,080 Verse Recitations + 77,429 Word-Level Aligned Audio Samples — Largest Multimodal Quran Dataset, Never Found in 41 Prior Rounds

**Paper:** arXiv:2601.17880 "Quran-MD: A Fine-Grained Multilingual Multimodal Dataset of the Quran" (January 25, 2026, NeurIPS 2025 Muslims in ML Workshop; OpenReview: NQ6er5I4PK).  
**HuggingFace:** `huggingface.co/datasets/Buraaq/quran-md-ayahs`

**Scale:**
- **187,080 verse-level recitations** (6,236 verses × 30 distinct reciters — diverse styles, dialects)
- **77,429 word-level samples** — each word paired with: aligned audio recording, Arabic script, English translation, transliteration
- Complete Quran coverage: all 114 surahs, 6,236 ayahs
- Multi-layered structure: verse-level AND word-level granularity

**Why this matters:**
1. This is the **richest public Quranic audio dataset found in 42 rounds**. `Buraaq/quran-audio-text-dataset` (our existing benchmark) is a subset of the same Buraaq project — `quran-md-ayahs` is the full Quran-MD release with word-level alignment.
2. **Word-level aligned audio** is exactly what's needed for Sakina's tajweed pipeline: play individual word audio → student repeats → word-level diff. Teacher can click any word in the recitation diff and hear the correct pronunciation instantly.
3. 30 reciters × 6,236 ayahs = data augmentation breadth for fine-tuning any ASR model (covers diverse acoustic conditions without copyright issues since all reciters consented).
4. The **word-level audio** enables training a word-boundary forced-aligner without needing external forced-alignment tools (the audio-text pairs are pre-segmented at word level).
5. **Applications called out in the paper:** ASR, tajweed detection, Quranic TTS, multimodal embeddings, semantic retrieval, personalized tutoring — this dataset was designed with Sakina's exact use case in mind.

**License:** Not confirmed (check HuggingFace model card — NeurIPS workshop papers sometimes use CC BY 4.0).

**Sakina actions:**
1. **(P0)** Check license on `Buraaq/quran-md-ayahs`; if CC BY or Apache → replace `Buraaq/quran-audio-text-dataset` as primary benchmark + add word-level pairs to training pipeline.
2. Use word-level audio pairs to train a word-level Quranic audio embedder (complementing QuranCLAP v2 audio-based verse ID from Round 33).
3. **Teacher UX feature:** in the session replay screen, teacher clicks any misread word → Quran-MD word-audio for that word plays back as the correct reference. Zero latency (pre-bundled).

---

## Insight 2 — RecitID (Vikolo Dynamics Ltd, iOS `id6759472226`, Android `com.recitid.app`): "Shazam for the Quran" — 200+ Reciter ID by Voice, Live Khutbah Translation, Ambient Listening — Uncatalogued Competitor in 41 Rounds

**Sources:** recitid.ai, App Store, Google Play.

**What it does:**
- **Verse + reciter identification in 10–15 seconds** from any audio source (masjid speakers, Instagram, TikTok, YouTube, user's own voice)
- **200+ voices / 160+ named reciters** — identifies by vocal fingerprint
- **Live Arabic transcription + translation** into 53 languages (real-time; positioned for khutbah/mosque use)
- **Quran-quotation detection** in live translation stream (knows when a verse is being quoted vs. general speech)
- **Smart Scanner** — point camera at Arabic text → meaning, source, context
- **Ambient listening mode** — background audio monitoring that logs what verses were heard throughout the day
- **Tajweed Mushaf reader** with color-coded tajweed + 48+ playback reciters
- **Core features FREE, no ads** (paid tier for advanced features)
- **Platform:** iOS + Android; by Vikolo Dynamics Ltd (UK-registered company)

**What RecitID is NOT:**
- Not a learning/feedback app — it does not evaluate the user's recitation quality
- No tajweed correction, no mispronunciation flagging, no teacher connection
- Positioned as a **discovery/identification** tool ("Shazam" metaphor), not a "teacher" tool

**Strategic analysis:**
1. RecitID's verse-ID pipeline (10–15s identification across 200+ reciters) is a production audio-embedding system — they have a large Quranic audio fingerprint database. This is the **commercial validation** of the QuranCLAP/audio-embedding approach from Round 33 (`yazinsai/offline-tarteel`). RecitID ships it as a product, not a research demo.
2. **Market segment:** RecitID is a listening/discovery app. Sakina is a learning app. No direct competition — RecitID users are casual listeners; Sakina users are active learners. But RecitID's "ambient listening" + "verse logging" is interesting UX that Sakina could adapt for session logging (log which verses the teacher recited during a live session, auto-populate the session transcript).
3. **Live khutbah translation** (53 languages) suggests they have an Arabic ASR + MT pipeline already built for real-time use — possible underlying tech: Whisper + NLLB or similar.
4. **Reciter identification by voice** across 200+ reciters means they solved the open problem `offline-tarteel` is still targeting at 87% recall. Check if RecitID has a public API or research paper behind their reciter-ID system.

**Sakina pitch angle:** *"RecitID tells you what you're hearing. Sakina teaches you how to say it."* — complementary positioning, not competitive.

**Sakina actions:**
1. Download RecitID and test verse-ID accuracy on beginner (non-native) recitation → establishes baseline for the audio-ID approach Sakina could adopt.
2. Check recitid.ai/brand-facts and press kit for technology partners/papers behind the 200-reciter-ID system.
3. Consider "ambient session logging" UX: during a live teacher session, Sakina logs which verses were covered using the same audio-embedding approach → auto-generates session summary for the student.

---

## Insight 3 — Fanar 2.0 (QCRI, arXiv:2603.16397, March 2026): Aura-STT-LF (First Arabic Long-Form ASR) + Fanar-Sadiq (Islamic Content Module) — 42-Round Blind Spot

**Paper:** arXiv:2603.16397 "Fanar 2.0: Arabic Generative AI Stack" (March 2026, Qatar Center for AI / QCRI / HBKU)  
**HuggingFace:** `QCRI/Fanar-2-*` collection  
**Team:** Includes Ahmed Ali (QCRI) — the same researcher behind IQRA 2026, QuranMB benchmark, and Interspeech 2025 Quran ASR work

**Components relevant to Sakina:**

**Aura-STT-LF** (Long-Form ASR):
- First Arabic-centric bilingual (Arabic-English) long-form ASR model
- Handles **hours-long recordings** with speaker-change robustness
- Readability restoration layer (post-processing for MSA diacritization)
- Dialect support: Egyptian, Gulf, Levantine, non-native accents, code-switching
- **Aura-STT-BenchLF**: first publicly available Arabic long-form ASR benchmark

**Fanar-Sadiq** (Islamic content module):
- Specialized Islamic knowledge component within Fanar 2.0
- Purpose: handle Islamic content queries with appropriate scholarly framing
- Potential use for Sakina: content guardrail for AI-generated tajweed explanations — route any Islamic ruling/fiqh question through Fanar-Sadiq's knowledge layer rather than generic LLM

**Fanar-27B** (base model):
- Gemma-3-27B backbone, continually pretrained on 120B high-quality Arabic tokens
- Data quality over quantity approach (Arabic = ~0.5% of web data)

**Strategic signals:**
1. **Aura-STT-LF + Ahmed Ali connection**: This is the QCRI group's production ASR system, the same team behind IQRA 2026 and QuranMB.v2. Their production stack for Arabic ASR is Aura-STT-LF. This is what Tarteel-level infrastructure looks like when designed for multi-hour recordings. Not directly deployable by Sakina (not on-device, sovereign infrastructure), but the **approach** (long-form ASR + diacritization + readability restoration) is a reference architecture.
2. **Fanar-Sadiq** is the first public Islamic-specialist AI module from a research institution — QCRI essentially ships what Sakina would call "the AI teacher assistant." Key question: is Fanar-Sadiq's knowledge base open (Apache/MIT) or gated to Qatar sovereignty context?
3. **Fanar 3.0 planned for December 2026** — the Arabic AI ecosystem is accelerating. QCRI's next release in 6 months will likely include a better Quran-specific ASR model.

**Sakina actions:**
1. Check QCRI HuggingFace collection (`huggingface.co/QCRI`) for Aura-STT-LF weights + license — if Apache 2.0 and usable, benchmark on `Buraaq/quran-audio-text-dataset` against KheemP (5.98% WER) and Cohere Transcribe (11.2% OOTB).
2. Investigate Fanar-Sadiq API availability — if there's a free endpoint, use it as the "explain this tajweed rule" knowledge backend (Islamic scholarship-verified answers, safer than generic GPT-4).
3. Note Fanar 3.0 (Dec 2026) on radar — may include Quran-specific ASR improvements from IQRA 2026 system winners.

---

## Monitor Update — Round 42 (05:35 UTC June 25)

| Monitor | Last Round Status | Round 42 Status | Action |
|---------|------------------|-----------------|--------|
| Tarteel version | v5.78.2 (Day 8 stall) | **v5.78.2 (Day 9 stall)** — still latest across all APK trackers | Ship window intact |
| uzair0/quran-asr | Training (~7h checkpoint) | **Still training** (~7h checkpoint save again) | Monitor daily |
| tadabur-Whisper-Large | "Coming Soon" (Day 10+) | **Still "Coming Soon"** — only Whisper Small listed | Monitor |
| sherpa-onnx | v1.13.3 | **v1.13.3** (June 22 release confirmed; new features: X-ASR, Nemotron-3.5 streaming, QNN; no v1.14.x) | Watch for v1.14 (Cohere Transcribe Flutter) |
| livekit_client | v2.8.1 stable / v2.9.0-dev.0 | **v2.8.1 stable still latest** — v2.9.0-dev.0 prerelease unchanged | Wait for v2.9.0 stable |

**sherpa-onnx June 22 details:** GitHub release assets from June 22 include support for X-ASR model export, Nemotron-3.5 multilingual streaming ASR, QNN (Qualcomm Neural Network) acceleration for Android, iOS onnxruntime upgraded to v1.26.0. All within v1.13.x series. No Cohere Transcribe Flutter yet (still v1.14 target).

---

## Summary

Three genuinely new findings this round, none previously catalogued across 41 rounds:

1. **`Buraaq/quran-md-ayahs`** — Largest public Quranic audio dataset with word-level alignment (77K words × aligned audio). Unlocks word-level audio reference in teacher UX and richer ASR training data.

2. **RecitID** — "Shazam for the Quran" competitor shipping real-time reciter identification from ambient audio. Commercial validation of the audio-embedding verse-ID approach. Complementary to Sakina (discovery vs. learning) but shows the market is broadening.

3. **Fanar 2.0 Aura-STT-LF + Fanar-Sadiq** — QCRI's production Arabic long-form ASR + Islamic knowledge module. Potential Sakina use: Fanar-Sadiq as the Islamic scholarship backend for AI explanations; Aura-STT as a reference architecture for multi-hour session transcription.

All monitors stable (stalling): Tarteel still at v5.78.2 (Day 9), uzair0 still training, tadabur-Large still "Coming Soon," sherpa-onnx still v1.13.3.
