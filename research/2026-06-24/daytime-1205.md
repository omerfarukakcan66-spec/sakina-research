# Research Report — 2026-06-24 12:05 UTC (Round 37)

**Focus:** Context-Aware Whisper for Quran ASR (arXiv:2511.18774) — 37-Round Blind Spot; Monitor Status

---

## PRIMARY FINDING — arXiv:2511.18774 (Nov 24, 2025): 22.3% Relative WER Reduction on Arabic ASR via Decoder Prompting With Known Text — Direct Free Upgrade for Sakina's Whisper Pipeline

**Paper:** "Zero-Shot Context-Aware ASR for Diverse Arabic Varieties" (also titled "Context-Aware Whisper for Arabic ASR Under Linguistic Varieties")  
**Authors:** Bashar Talafha, Amin Abu Alhassan, Muhammad Abdul-Mageed  
**arXiv:** `arxiv.org/abs/2511.18774` (November 24, 2025)  
**Venue:** Appears on OpenReview (NeurIPS 2025 track); published November 2025

### What it does

The paper shows that injecting context into Whisper's decoder **without any fine-tuning** dramatically reduces WER for Arabic:

- **Prompt-1 (first-pass hypothesis):** Feed a prior transcription (or, critically, *known target text*) as Whisper's decoder prompt → **22.3% relative WER reduction on MSA Arabic, 20.54% on accented MSA Arabic**
- **Proxy-guided n-best selection:** For CTC-based models (like sherpa-onnx Nemotron), select from n-best list by minimizing text-distance to external hypothesis → free quality boost without decoder access
- Tested across **10 Arabic conditions** including MSA, accented MSA (non-native), and dialectal Arabic
- **Zero-shot**: no additional training, no fine-tuning, works on any Whisper checkpoint

### Why this is the biggest single free upgrade missed across 37 rounds

Sakina's use case is unique compared to generic Arabic ASR: **the student always selects which verse they are reciting before starting.** This means Sakina knows the exact expected Arabic text before the first audio frame arrives.

Standard Whisper receives no contextual prior → decodes cold from speech. With arXiv:2511.18774's approach:

```
student selects verse → look up mushaf text → 
feed Arabic verse text as Whisper decoder initial_prompt →
Whisper decodes with 22% fewer errors
```

Concrete implementation:
- `whisper.transcribe(audio, initial_prompt="بِسْمِ اللَّهِ الرَّحْمَٰنِ الرَّحِيمِ الْحَمْدُ لِلَّهِ رَبِّ الْعَالَمِينَ")` (or whatever verse is selected)
- Works for KheemP model (5.98% WER → ~4.6% WER estimated), tadabur-Whisper-Small (8.7% WER → ~6.8% WER estimated), and any other Whisper-based checkpoint
- The two-stage variant: first pass = on-device sherpa-onnx Nemotron (fast, slightly noisier) → output becomes `initial_prompt` for cloud Whisper second pass → even better accuracy
- For CTC models (sherpa-onnx), use proxy-guided n-best selection: generate N hypotheses → re-rank by edit distance to expected verse text → pick closest → no decoder access needed

### Baseline numbers from the paper
- MSA Arabic: **~22.3% relative WER reduction** (e.g., a 5.98% baseline → ~4.6%)
- Accented MSA: **~20.5% relative WER reduction** (directly relevant to non-native diaspora learners)
- Dialects: ~9.15% relative WER reduction (lower benefit but still positive)

### Why 37 rounds missed this

Prior rounds focused on model-level improvements (new checkpoints, fine-tuning). This paper is about inference-time conditioning — it is invisible to HuggingFace model card searches. The technique is general and the paper does not mention Quran explicitly; its applicability to Quran ASR is a Sakina-specific insight.

### Sakina Action (P0 priority)
1. Add `initial_prompt` parameter to all Whisper API calls in the pipeline: feed the verse being recited (from QUL mushaf text, MIT license) as the prompt
2. For two-pass pipeline: on-device sherpa-onnx first pass → output as `initial_prompt` for cloud Groq/Whisper second pass
3. For CTC sherpa-onnx: implement n-best proxy selection with expected verse as reference (re-rank sherpa's top-5 by edit distance to expected text)
4. Benchmark improvement: run KheemP + context vs. KheemP baseline on `Buraaq/quran-audio-text-dataset` — if confirmed ~20%+ relative reduction, this becomes the standard default in all paths with no added cost or model download

---

## SECONDARY FINDING — RetaSy/quranic_audio_dataset (LREC 2024): 7,000 Non-Native Speaker Quran Recordings With Expert Annotations — Sakina's Demographic in a Dataset

**HuggingFace:** `huggingface.co/datasets/RetaSy/quranic_audio_dataset`  
**Paper:** LREC 2024 (crowdsourcing platform "Quran Voice")  
**License:** check before use

7,000 Quranic recitations from **1,287 non-native Arabic speakers across 11+ countries** (the exact Sakina diaspora demographic — Turkish, Indonesian, Urdu-speaking learners). Annotated in six categories; crowd accuracy 0.77, inter-rater agreement 0.63, expert-algorithm agreement 0.89. This is the only public dataset of *learner* Quran recitations from non-native speakers with quality annotations.

**Why it matters for Sakina:**
- The obadx `muaalem-v3_2` (0.16% PER) and KheemP (5.98% WER) are both trained on *expert reciters* (EveryAyah, professional Qaris). RetaSy is a non-native speaker evaluation corpus.
- **Sakina's scorer must handle learner-grade audio, not just expert audio.** RetaSy is the right evaluation set to benchmark against.
- `Sadique5/arabic_quranic_asr` (HuggingFace dataset): a dataset of all Quran verses + 10K unique words, built to train beginner-focused ASR. Complementary to RetaSy for training rather than evaluation.

**Sakina Action:** Download and benchmark the context-aware Whisper + muaalem-v3_2 pipeline on RetaSy (non-native learners) to confirm accuracy holds on real beginner audio, not just professional reciters.

---

## MONITOR STATUS — Round 37

| Target | Last Known | Status | Action |
|---|---|---|---|
| **Tarteel Android** | v5.78.2 (June 19) | **~6-day stall** — no v5.79+ found anywhere. Hotfix sprint confirmed ended. No Makharij/Tajweed announcement. | Monitor daily; Q3 Sakina window intact |
| **`uzair0/quran-asr`** | Apache-2.0, ~6.3GB, training active | Still training (unconfirmed completion). Apache-2.0 confirmed. | Trigger: commit frequency drop daily→weekly |
| **`FaisaI/tadabur-Whisper-Large`** | "Coming soon" | Still "coming soon" per June 2026 search. Whisper-Small (CC BY-NC, 8.7% WER) still the only released tadabur model. | Monitor FaisaI HF org weekly |

---

## OTHER NOTABLE ITEMS

- **TajweedAI (NeurIPS 2025 Muslims-in-ML, `nmazid121/tajweedAI`):** ASR → CTC Forced Aligner → binary Qalqalah classifier pipeline. Results: 100% on internal set, **57.14% on external set** — not production-grade. No HuggingFace model released. The approach (word-segment → dedicated rule classifier) is architecturally sound but the specific classifier needs more data. Confirmed the EfficientNet-B0-SE approach (Round 36) is a better-validated path for per-rule detection.

- **arXiv:2511.18774 Python implementation note:** The paper uses standard Whisper API. The `initial_prompt` parameter is available in both `openai-whisper` and `faster-whisper` (which powers sherpa's Whisper integration). Also available directly in Groq's Whisper endpoint — check Groq API docs for `prompt` parameter support.

---

## SOURCES

- arXiv:2511.18774: `arxiv.org/abs/2511.18774`
- RetaSy dataset: `huggingface.co/datasets/RetaSy/quranic_audio_dataset`
- Sadique5 dataset: `huggingface.co/datasets/Sadique5/arabic_quranic_asr`
- TajweedAI GitHub: `github.com/nmazid121/tajweedAI`
- TajweedAI NeurIPS: `neurips.cc/virtual/2025/133028`
- Tarteel versions (AppBrain): `appbrain.com/app/tarteel-ai-quran-memorization/com.mmmoussa.iqra`
- Tarteel Uptodown: `tarteel.en.uptodown.com/android/versions`
- IQRA 2026 leaderboard: `huggingface.co/spaces/IqraEval/IqraEval_Interspeech_26`
