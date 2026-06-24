# Sakina Research — 2026-06-24 Daytime (08:04 UTC) — Round 35

**Focus question:** Newest Arabic/Quran ASR models on HuggingFace + monitoring open items (uzair0 training, Tarteel version, tadabur-Whisper-Large).

---

## Insight 1 — `TBOGamer22/wav2vec2-quran-phonetics` (Dec 2025, HuggingFace): First Public Phoneme-Output-Only Quran ASR Model — 35-Round Blind Spot

**URL:** `huggingface.co/TBOGamer22/wav2vec2-quran-phonetics`  
**Uploaded:** December 2025  
**Architecture:** wav2vec2-based CTC with Wav2Vec2ForCTC — fine-tuned specifically for **phonetic transcription** (phoneme-level output, NOT orthographic Arabic text)  
**Key claim:** *"To the best of the developer's knowledge, this is the first publicly released wav2vec2 model trained explicitly for Quranic phonetic transcription."*

### What makes it different from existing phoneme models
- `obadx/muaalem-model-v3_2` (the current Sakina primary scorer) outputs **QPS (Quran Phonetic Script)** — a custom 11-level encoding with Tajweed sifat attributes, specifically designed for the ICML 2026 pipeline. QPS is not standard IPA.
- `TBOGamer22/wav2vec2-quran-phonetics` outputs a **standard phonetic representation** that may be compatible with `panphon` (IPA-weighted articulatory edit distance) already in the Sakina pipeline.
- The feature extractor is **NOT frozen** — this is unusual and meaningful: it allows acoustic adaptation to Quranic recitation style (elongated Madd, Qalqalah echo, Ghunnah nasalization) that a frozen encoder cannot learn.

### Training details (confirmed via search)
- Training data: word-level Quranic recitation audio with aligned metadata (private dataset)
- Input: 16kHz mono PCM
- No PER published; no license confirmed in search results
- No associated arXiv paper found

### Strategic assessment for Sakina
**Why this matters:** The Sakina mispronunciation scorer (P1 step 5) needs a speech→phoneme frontend. The current blueprint uses `obadx/muaalem-model-v3_2` (0.16% PER, Apache-2.0, QPS output) as primary. `TBOGamer22/wav2vec2-quran-phonetics` offers a **second, independently trained phoneme frontend** with standard (non-QPS) phoneme output — potentially compatible with `panphon` IPA-feature scoring out of the box.

**Risk:** Unknown license and no published PER. Could be personal-project tier with no formal evaluation.

**Sakina action:**
1. Visit `huggingface.co/TBOGamer22/wav2vec2-quran-phonetics` directly, check license and model card for phoneme inventory.
2. Run on 5–10 Quran clips from `Buraaq/quran-audio-text-dataset` and count phoneme errors manually.
3. Check phoneme output format: if IPA-compatible, feed directly into `panphon` weighted NW alignment for mispronunciation scoring — bypasses the QPS→IPA conversion step needed with `muaalem-v3_2`.
4. If license is MIT/Apache: use as lightweight cross-check scorer alongside `muaalem-v3_2`; disagreement between the two signals edge cases worth escalating to a human teacher.

---

## Insight 2 — `IbrahimSalah/Wav2vecLarge_quran_syllables_recognition` (HuggingFace): Quran Syllable-Level ASR With 5-gram LM — Never Covered, Useful for Madd Duration

**URL:** `huggingface.co/IbrahimSalah/Wav2vecLarge_quran_syllables_recognition`  
**Architecture:** wav2vec2-large + 5-gram syllable-level language model  
**Training data:** Private dataset + part of Tarteel/EveryAyah dataset (cleaned, converted to syllables)  
**Output:** Syllable transcription, not word-level or phoneme-level  

Example output: `مِنَ الْجِنَّةِ وَالنَّاسِ` → `مِ نَلْ جِنْ نَ تِ وَنْ نَاْسْ`

### Why syllable-level output matters for Tajweed
Tajweed rules that are inherently duration/count-based operate at the syllable boundary:
- **Madd (elongation)**: Madd Tabii = 2 beats (1 syllable extension); Madd Munfasil = 4 beats; Madd Lazim = 6 beats. Counting beats requires syllable-level temporal segmentation.
- **Qalqalah echo**: Post-syllable (sukoon or waqf) — a syllable-boundary phenomenon.
- **Ghunnah (nasalization)**: 2 beats duration — syllable-timed.

Current Sakina pipeline uses letter-level timestamps from `hetchyy/quranic-universal-ayahs` as a workaround for duration measurement. A syllable-ASR model offers a **direct** route: recognized syllable boundaries → measure beat count → classify Madd type → flag if wrong.

**Risk:** Private training data; license unknown; no published WER at syllable level. Also appears to be a personal research project with no paper.

**Sakina action:** Check license and run a quick syllable-boundary timing test. If it cleanly segments Madd-bearing syllables with the correct duration markers, it's a useful adjunct scorer for duration-based Tajweed rules.

---

## Monitoring Updates (Open Items from Prior Rounds)

### Tarteel Version — Still Stalled at v5.78.2 (5+ Days)
- Latest confirmed: **v5.78.2 (June 19, 2026)**
- June 24 search: No v5.79+ found on any source (Uptodown, AppBrain, APKPure, Softonic)
- 5-day release stall continues (post-hotfix-sprint pause)
- No Makharij/Mahraj announcement found
- **Interpretation:** The hotfix sprint (June 7–19, 4 releases) appears fully resolved; next release cycle likely brings a new feature batch. Monitor daily — if a v5.79+ releases with any Makharij/Tajweed changelog text, that is the competitive alarm signal for Sakina.

### `uzair0/quran-asr` — Training Completion Unconfirmed, Recent Activity Continues
- Apache-2.0, 6.31GB (Whisper-large-v3 class)
- Search results still show "model saves ~7 hours ago" pattern — consistent with ongoing checkpoint saves
- "Step 2000 from 3 days ago" appeared in search snippets again — this may be a cached search snippet from Round 30
- **Conclusion:** Cannot confirm training is complete from web search alone. The monitor trigger (commit frequency dropping from daily → weekly) has NOT been met based on available evidence.
- **Sakina action:** Continue monitoring. Once confirmed complete, benchmark within 24h on `Buraaq/quran-audio-text-dataset`. Apache-2.0 license = production-deployable immediately.

### `FaisaI/tadabur-Whisper-Large` — Still "Coming Soon"
- Whisper-Small (CC BY-NC 4.0, 8.7% WER) is available
- Large model still not released as of this search
- No release date announced
- **Sakina action:** Monitor `huggingface.co/FaisaI` org for new model cards. If Large model drops and WER < 6%, it becomes a strong competitor to `KheemP/whisper-base-quran-lora` (5.98%) with far broader recitation style coverage.

### New Dataset Found: `Sadique5/arabic_quranic_asr` (HuggingFace)
- Contains Quranic recitation audio for every ayah + 10K unique Quran words
- Purpose: training ASR models for teaching beginners
- Designed for ASR training — could supplement `Buraaq/quran-audio-text-dataset` as a secondary benchmark
- License unknown
- **Priority: Low** (no model weights, only a dataset; lower quality than Buraaq/quran-md)

---

## New Model Map (All Rounds, Phoneme/Syllable Tier)

| Model | Output Type | PER/WER | License | Source | Covered |
|---|---|---|---|---|---|
| `obadx/muaalem-v3_2` | QPS phoneme | **0.16% PER** | Apache-2.0 | ICML 2026 | R20+ |
| `facebook/omniASR-CTC-1B` | Multilingual phoneme | 8.92% PER (Arabic) | Apache-2.0 | Harf-Speech | R34 |
| `TBOGamer22/wav2vec2-quran-phonetics` | Standard phoneme | unknown | unknown | Dec 2025 | **R35 NEW** |
| `IbrahimSalah/Wav2vecLarge_quran_syllables` | Syllable | unknown | unknown | Personal | **R35 NEW** |
| Hafs2Vec (ArabicNLP 2025 #2) | Phoneme (IqraEval set) | IqraEval #2 | paper only | ArabicNLP | R32 |
| `AraS2P` (arXiv:2509.23504) | IPA phoneme | IqraEval #1 | paper only | ArabicNLP | R31 |

---

## Summary of This Run

**Primary new finding:** `TBOGamer22/wav2vec2-quran-phonetics` (Dec 2025) — a phoneme-output-only Quran ASR model, never covered in 35 rounds. Claimed to be first publicly released wav2vec2 model for Quranic phonetic transcription. License and PER unknown; worth immediate spot-check.

**Secondary new finding:** `IbrahimSalah/Wav2vecLarge_quran_syllables_recognition` — syllable-level ASR with 5-gram LM, directly useful for duration-based Tajweed rule detection (Madd, Qalqalah, Ghunnah). License unknown.

**Monitoring:** Tarteel still frozen at v5.78.2 (5+ days). `uzair0/quran-asr` training unconfirmed complete. `FaisaI/tadabur-Whisper-Large` still unreleased.
