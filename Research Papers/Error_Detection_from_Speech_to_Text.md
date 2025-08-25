## Grammar

### Automatic Proficiency Assessment in L2 English Learners (Grammar Review Paper)

**Objective:**
Explore the effectiveness of automatic grammar error detection and correction systems in assessing the proficiency of non-native English speakers.

**Architectures:**

* Traditional **rule-based grammar checkers** (POS tagging, dependency parsing).
* **Statistical approaches** using n-grams and error frequency models.
* **Neural models** (Seq2Seq and Transformer-based) for grammar error correction.

**Methodology:**

1. **Rule-Based Baseline**

   * Predefined grammar rules and error templates applied to learner text.
   * Evaluated coverage and precision.

2. **Neural Error Detection**

   * Transformer-based classifiers trained on annotated corpora.
   * Fine-tuning on learner-specific error distributions.

3. **Proficiency Estimation**

   * Mapping detected error distribution to CEFR proficiency bands (A1–C2).
   * Scoring based on correction quality and error frequency.

**Datasets:**

* **CoNLL-2014 GEC Dataset** – 1.3M tokens, learner essays with gold-standard corrections.
* **BEA-2019 Shared Task Corpora** – multilingual learner corpora with 4M+ tokens, error annotations for grammar/spelling.

**Results:**

| Model Type           | Precision | Recall | F1-score | CEFR Correlation |
| -------------------- | --------- | ------ | -------- | ---------------- |
| Rule-Based           | 62%       | 55%    | 58%      | 0.65             |
| Statistical (n-gram) | 70%       | 66%    | 68%      | 0.71             |
| Transformer GEC      | 82%       | 79%    | 80%      | 0.87             |

---

## Pronunciation

### GOPT: Transformer-Based Multi-Aspect Pronunciation Assessment (Gong et al., 2022)

**Objective:**
Propose **GOPT**, a Transformer-based model for assessing pronunciation across multiple aspects (accuracy, fluency, prosody) and granularities (phoneme, word, utterance).

**Architectures:**

* Transformer encoder with **Goodness of Pronunciation (GOP)** features.
* Multi-task heads for accuracy, prosody, fluency.
* Acoustic backbone pre-trained on **LibriSpeech** (1000h).

**Methodology:**

1. **Feature Extraction** – GOP, phoneme duration, pitch, energy.
2. **Multi-task Transformer** – shared encoder, task-specific heads.
3. **Scoring** – regression outputs mapped to expert-labeled ratings.

**Datasets:**

* **Speechocean762** – 5,000+ utterances from non-native speakers, annotated at phoneme/word/utterance levels by experts.

**Results:**

| System           | Phoneme Corr. | Word Corr. | Utterance Corr. |
| ---------------- | ------------- | ---------- | --------------- |
| GOP-only         | 0.61          | 0.58       | 0.64            |
| CNN/RNN Baseline | 0.70          | 0.67       | 0.72            |
| **GOPT**         | **0.83**      | **0.80**   | **0.85**        |

---

## Fluency

### 3M: Multi-Aspect, Multi-Granularity, and Multi-Modal Fluency Assessment (2023)

**Objective:**
Introduce **3M**, a unified framework combining **text + audio** across multiple granularities (phoneme, word, sentence, discourse) for fluency scoring.

**Architectures:**

* Dual encoders:

  * Text encoder (Transformer on transcripts).
  * Audio encoder (CNN + BiLSTM for prosody, pauses).
* Multi-modal fusion via attention.

**Methodology:**

1. **Text Processing** – tokenized transcripts, grammar/vocab features.
2. **Audio Processing** – speech rate, pause distribution, prosody.
3. **Fusion + Regression** – attention-based integration for fluency score.

**Datasets:**

* **TOEFL Speech Dataset** – 12,000 learner responses, human-rated fluency scores (1–5 scale).
* Augmented with **crowd-sourced L2 English** recordings (8,000 samples).

**Results:**

| Model               | Pearson Correlation w/ Human Scores | RMSE     |
| ------------------- | ----------------------------------- | -------- |
| Text-only           | 0.71                                | 0.54     |
| Audio-only          | 0.74                                | 0.51     |
| **3M (Text+Audio)** | **0.86**                            | **0.39** |

---
# Next Steps for Our Project

## 1. Data Collection
- **Grammar:** Collect English learner essays, job interview transcripts, and Egyptian English learner corpora (if available). Public datasets include:
  - [BEA Shared Task (2019)](https://www.cl.cam.ac.uk/research/nl/bea2019st/)
- **Pronunciation:** Use datasets with phoneme-level annotations and accented English speakers.
  - [Speechocean762](https://www.openslr.org/101/)
  - [L2-ARCTIC](https://psi.engr.tamu.edu/l2-arctic-corpus/)
- **Fluency:** Spoken learner corpora with timestamps, pauses, and disfluency labels.
  - [FluencyBank](https://fluency.talkbank.org/)

## 2. Model Selection & Subscriptions
- **Grammar Models:**
  - Pretrained: `T5`, `BART`, `GPT`, `mT5`, `GECToR`
  - Need Hugging Face API credits or we can use colab pro.
- **Pronunciation Models:**
  - Forced alignment (Montreal Forced Aligner, Kaldi).
  - ASR models: Whisper, wav2vec2, HuBERT.
- **Fluency Models:**
  - wav2vec2/HuBERT fine-tuned on CEFR-labeled datasets.
  - Classical ML (XGBoost, LightGBM) with extracted pause/prosody features.

## 3. Data Size Requirements as seen in papers 
- **Grammar:** At least 100K+ sentences for medium fine-tuning.
- **Pronunciation:** 200–500 hours of labeled learner speech for good phoneme scoring. Minimum ~50 hours for prototype.
- **Fluency:** 50–100 hours of learner speech with pause & disfluency annotations.
-  **Estimated data set collection:😔** 
  
|  Target hours| Speakers (15 min each) | Total seconds | Total clips (10 s) |
| -----------: | ---------------------: | ------------: | -----------------: |
|         50 h |                **200** |       180,000 |         **18,000** |
|        120 h |                **480** |       432,000 |         **43,200** |
|        200 h |                **800** |       720,000 |         **72,000** |

---
## 5.Research Papers Summary Table
| Domain            | Paper Title                                                                                                                     | Year  | Model / Approach                                    | Dataset(s) Used                                           | Key Results                                                              |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------- | ----- | --------------------------------------------------- | --------------------------------------------------------- | ------------------------------------------------------------------------ |
| **Grammar**       | [Automatic Proficiency Assessment in L2 English Learners (Grammar Review)](#grammar-review-2019)                                | 2019  | Rule-based, Statistical, Transformer GEC            | CoNLL-2014 (1.3M tokens), BEA-2019 (4M+ tokens)           | Transformer GEC: F1 = 80%, CEFR corr = 0.87                              |
| **Pronunciation** | [GOPT: Transformer-Based Multi-Aspect Pronunciation Assessment (Gong et al.)](#gopt-2022)                                       | 2022  | Transformer + GOP + Multi-task Heads                | Speechocean762 (5,000+ utterances)                        | Phoneme corr = 0.83, Word corr = 0.80, Utterance corr = 0.85             |
| **Pronunciation** | [SSL + BiLSTM for Mispronunciation Detection (wav2vec2, HuBERT based)](#ssl--bilstm-for-mispronunciation-detection-recent-work) | 2020+ | SSL encoder + BiLSTM                                | L2-ARCTIC, lab-collected non-native speech                | Higher detection accuracy than GOP; better generalization to new accents |
| **Fluency**       | [3M: Multi-Aspect, Multi-Granularity, and Multi-Modal Fluency Assessment](#3m-2023)                                             | 2023  | Dual encoders (Text Transformer + CNN-BiLSTM Audio) | TOEFL Speech (12k samples), Crowd-sourced L2 (8k samples) | Text+Audio corr = 0.86 with human scores, RMSE = 0.39                    |



