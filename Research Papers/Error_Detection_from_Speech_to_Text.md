# Error Detection from Speech-to-Text

## Grammar Checker

### Options
We have three main approaches:

1. **Traditional**
   - Grammar rules + regex + parsers (Stanford NLP or SpaCy)  
   - Best for deterministic mistakes.

2. **Machine Learning**
   - Classification models.  
   - Requires **feature engineering**: POS tags, n-grams, syntax trees.

3. **Deep Learning**
   - Seq2Seq / Transformer-based models (BERT, RoBERTa, T5, GPT, GECToR)  
   - Handles complex error patterns.

---

## Pronunciation Assessment

### Method
- Phoneme-level alignment: align learner audio with expected phoneme sequence, duration, stress, acoustic features.  
- **Goodness of Pronunciation (GOP)** score.  
- Acoustic model in ASR.

### Metrics
- **Phoneme Error Rate (PER)**  
- **Word Error Rate (WER)** – compared to reference transcription  
- **GOP score (0–1)** – scale of pronunciation quality

---

## Fluency Metrics

- **Speech Rate:** WPM (words per minute)  
- **Pause Patterns:** number and length of silent pauses (e.g., "uh", "um")  
- **Articulation Rate:** WPM excluding pauses  
- **Disfluencies:** repetitions, self-corrections, hesitations

### Computed Metrics
- **Mean Length of Run (MLR):** avg words between pauses  
- **Pause-to-Speech Ratio (PSR):** pause duration / total speech duration  
- **Phonation Time Ratio (PTR):** speech time / total recording time

---

## Models by Task

| Task           | Rule-Based / Traditional                               | Statistical / Classical ML                                | Neural / Deep Learning                                           | Integrated / Hybrid                                       |
| -------------- | ------------------------------------------------------ | -------------------------------------------------------- | ---------------------------------------------------------------- | -------------------------------------------------------- |
| **Grammar**    | LanguageTool, Regex + POS taggers, SpaCy/StanfordNLP  | Classifiers (SVM, Logistic Regression, CRF) using n-grams, POS, syntax trees | Seq2Seq / Transformers (T5, BART, MarianMT, mT5, GPT), GECToR (BERT-based edit model) | Rules for simple errors + Transformers for complex ones |
| **Pronunciation** | Forced alignment (Kaldi, Montreal Forced Aligner, Gentle), GOP scoring | –                                                        | ASR-based mispronunciation detection (Whisper, wav2vec2, HuBERT), End-to-End scorers fine-tuned on datasets (Speechocean762, wav2vec2/HuBERT/Whisper) | Hybrid: ASR transcript + phoneme scoring                 |
| **Fluency**    | Speech rate (WPM, syllables/sec), Pause & disfluency features, Prosody | Feature-based ML scoring (XGBoost, LightGBM, SVM)       | wav2vec2 / HuBERT fine-tuned for fluency/CEFR, LSTM/GRU using pause + prosody features | Combine hand-crafted features + neural predictions     |
| **Integrated** | –                                                      | –                                                        | Multi-task Transformers (grammar + pronunciation + fluency), Holistic scoring (Versant, Duolingo English Test, IELTS AI) | End-to-End scoring systems blending all modalities      |

## Grammar

### Automatic Proficiency Assessment in L2 English Learners (Grammar Review Paper)

**Objective:**  
Explore the effectiveness of automatic grammar error detection and correction systems in assessing the proficiency of non-native English speakers.

**Architectures:**
- Traditional **rule-based grammar checkers** (POS tagging, dependency parsing).
- **Statistical approaches** using n-grams and error frequency models.
- **Neural models** (Seq2Seq and Transformer-based) for grammar error correction.

**Methodology:**
1. **Rule-Based Baseline**  
   - Predefined grammar rules and error templates applied to learner text.  
   - Evaluated coverage and precision.

2. **Neural Error Detection**  
   - Transformer-based classifiers trained on annotated corpora.  
   - Fine-tuning on learner-specific error distributions.

3. **Proficiency Estimation**  
   - Mapping detected error distribution to CEFR proficiency bands (A1–C2).  
   - Scoring based on correction quality and error frequency.

**Datasets:**
- **[CoNLL-2014 GEC Dataset](https://www.cl.cam.ac.uk/research/nl/bea2019st/)** – 1.3M tokens, learner essays with gold-standard corrections.
- **BEA-2019 Shared Task Corpora** – multilingual learner corpora with 4M+ tokens, error annotations for grammar/spelling.

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
- Transformer encoder with **Goodness of Pronunciation (GOP)** features.
- Multi-task heads for accuracy, prosody, fluency.
- Acoustic backbone pre-trained on **LibriSpeech** (1000h).

**Methodology:**
1. **Feature Extraction** – GOP, phoneme duration, pitch, energy.
2. **Multi-task Transformer** – shared encoder, task-specific heads.
3. **Scoring** – regression outputs mapped to expert-labeled ratings.

**Datasets:**
- **[Speechocean762](https://www.openslr.org/101/)** – 5,000+ utterances from non-native speakers, annotated at phoneme/word/utterance levels by experts.

**Results:**

| System           | Phoneme Corr. | Word Corr. | Utterance Corr. |
| ---------------- | ------------- | ---------- | --------------- |
| GOP-only         | 0.61          | 0.58       | 0.64            |
| CNN/RNN Baseline | 0.70          | 0.67       | 0.72            |
| **GOPT**         | **0.83**      | **0.80**   | **0.85**        |

---

### SSL + BiLSTM for Mispronunciation Detection (Recent Work)

**Objective:**  
Leverage self-supervised learning (SSL) models like wav2vec 2.0 combined with BiLSTM to improve mispronunciation detection.

**Architectures:**
- SSL encoder (wav2vec 2.0, HuBERT).  
- BiLSTM layers for temporal modeling.  
- Prediction head for binary mispronunciation classification.

**Methodology:**
1. **SSL Feature Extraction** – Pretrained wav2vec/HuBERT layers.  
2. **BiLSTM Modeling** – Temporal alignment with canonical phoneme sequences.  
3. **Classification** – Binary detection of substitution/deletion/insertion errors.

**Datasets:**
- **[L2-ARCTIC](https://psi.engr.tamu.edu/l2-arctic-corpus/)** – 115 speakers, ~20h non-native speech with phoneme-level error labels.  
- Extended lab-collected dataset (~50h Egyptian & Asian English accents).

**Results:**

| Model            | Accuracy | F1-score | Accent Generalization |
| ---------------- | -------- | -------- | ------------------- |
| GOP Baseline     | 72%      | 68%      | Low                 |
| CNN/RNN          | 80%      | 76%      | Medium              |
| **SSL + BiLSTM** | **89%**  | **86%**  | High                |

---

## Fluency

### 3M: Multi-Aspect, Multi-Granularity, and Multi-Modal Fluency Assessment (2023)

**Objective:**  
Introduce **3M**, a unified framework combining **text + audio** across multiple granularities (phoneme, word, sentence, discourse) for fluency scoring.

**Architectures:**
- Dual encoders:  
  - Text encoder (Transformer on transcripts)  
  - Audio encoder (CNN + BiLSTM for prosody, pauses)  
- Multi-modal fusion via attention.

**Methodology:**
1. **Text Processing** – tokenized transcripts, grammar/vocab features.  
2. **Audio Processing** – speech rate, pause distribution, prosody.  
3. **Fusion + Regression** – attention-based integration for fluency score.

**Datasets:**
- **[TOEFL Speech Dataset](https://www.ets.org/toefl)** – 12,000 learner responses, human-rated fluency scores (1–5 scale).  
- Augmented with **crowd-sourced L2 English** recordings (8,000 samples).

**Results:**

| Model               | Pearson Correlation w/ Human Scores | RMSE     |
| ------------------- | ----------------------------------- | -------- |
| Text-only           | 0.71                                | 0.54     |
| Audio-only          | 0.74                                | 0.51     |
| **3M (Text+Audio)** | **0.86**                            | **0.39** |

---

# Next Steps for Our Project

## 1. Data Collection
- **Grammar:** Collect English learner essays, job interview transcripts, and Egyptian English learner corpora (if available).  
  - [BEA Shared Task (2019)](https://www.cl.cam.ac.uk/research/nl/bea2019st/)
- **Pronunciation:** Use datasets with phoneme-level annotations and accented English speakers.  
  - [Speechocean762](https://www.openslr.org/101/)  
  - [L2-ARCTIC](https://psi.engr.tamu.edu/l2-arctic-corpus/)
- **Fluency:** Spoken learner corpora with timestamps, pauses, and disfluency labels.  
  - [FluencyBank](https://fluency.talkbank.org/)

## 2. Model Selection & Subscriptions
- **Grammar Models:** `T5`, `BART`, `GPT`, `mT5`, `GECToR`  
- **Pronunciation Models:** Forced alignment (Montreal Forced Aligner, Kaldi), ASR models (Whisper, wav2vec2, HuBERT)  
- **Fluency Models:** wav2vec2/HuBERT fine-tuned on CEFR-labeled datasets, classical ML (XGBoost, LightGBM) on pause/prosody features

## 3. Data Size Requirements as seen in papers 
- Grammar: ≥100K sentences  
- Pronunciation: 200–500 hours of labeled speech (50h minimum for prototype)  
- Fluency: 50–100 hours with pause/disfluency annotations  

| Target hours | Speakers (15 min each) | Total seconds | Total clips (10 s) |
| ------------:| ---------------------: | ------------: | -----------------: |
| 50 h         | 200                    | 180,000       | 18,000            |
| 120 h        | 480                    | 432,000       | 43,200            |
| 200 h        | 800                    | 720,000       | 72,000            |

---

## Research Papers Summary Table

| Domain            | Paper Title & Link                                                                                             | Year | Model / Approach                                    | Dataset(s) Used                                           | Key Results                                                              |
| ----------------- | -------------------------------------------------------------------------------------------------------------- | ---- | --------------------------------------------------- | --------------------------------------------------------- | ------------------------------------------------------------------------ |
| **Grammar**       | [Automatic Proficiency Assessment in L2 English Learners (Grammar Review)](https://aclanthology.org/W19-4406/) | 2019 | Rule-based, Statistical, Transformer GEC           | CoNLL-2014 (1.3M tokens), BEA-2019 (4M+ tokens)           | Transformer GEC: F1 = 80%, CEFR corr = 0.87                              |
| **Pronunciation** | [GOPT: Transformer-Based Multi-Aspect Pronunciation Assessment](https://arxiv.org/abs/2205.03432)             | 2022 | Transformer + GOP + Multi-task Heads               | Speechocean762 (5,000+ utterances)                        | Phoneme corr = 0.83, Word corr = 0.80, Utterance corr = 0.85             |
| **Pronunciation** | [SSL + BiLSTM for Mispronunciation Detection (wav2vec2, HuBERT)](https://arxiv.org/abs/2006.11477)            | 2020+| SSL encoder + BiLSTM                                | L2-ARCTIC, lab-collected non-native speech                | Higher detection accuracy than GOP; better generalization to new accents |
| **Fluency**       | [3M: Multi-Aspect, Multi-Granularity, and Multi-Modal Fluency Assessment](https://arxiv.org/abs/2305.13012)   | 2023 | Dual encoders (Text Transformer + CNN-BiLSTM Audio)| TOEFL Speech (12k samples), Crowd-sourced L2 (8k samples)| Text+Audio corr = 0.86 with human scores, RMSE = 0.39                    |

