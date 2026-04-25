# russian-case-interpretation-in-word2vec

## 📌 Overview

This project explores whether **grammatical case relations in Russian** can be inferred from distributional semantics using static word embeddings (word2vec). The core hypothesis is that nouns sharing the same case should form interpretable clusters in the vector space, revealing that case information is encoded in distributional semantics.

The hypothesis was **not confirmed** – but the experiment provides valuable insights into the limitations of static embeddings for morphological tasks. The repository documents a full research pipeline: data preprocessing, model training, dimensionality reduction (PCA / LDA), and critical analysis of negative results.

## 🧪 Hypothesis

> If a word's grammatical case is encoded in its distributional semantics, then word2vec vectors for words with the **same case** (e.g., all `стол_Nom` forms) should cluster together more tightly than vectors for different cases, and these clusters should be visible after linear projection (PCA / LDA).

## 📁 Repository Contents

- `word2vec_case_clustering_experiment_full_pipeline.ipynb` – Main Jupyter notebook with all code, visualizations, and commentary.
- `requirements.txt` – Python dependencies for reproducing the environment.
- `README.md` – This file.

## 🛠️ Methods

### Data
- **Corpus:** Russian SynTagRus from Universal Dependencies (`ru_syntagrus-ud-train-b.conllu`).
- **Filtering:** Only nouns (`NOUN`) and proper nouns (`PROPN`) with a non‑null `CASE` feature.
- **Rare cases removed:** Partitive (`Par`) and Vocative (`Voc`) – each accounted for less than 1% of the data.

### Preprocessing
- Each token was transformed to a `form_grammeme` string (e.g., группа_Noun_Animacy=Inan|Case=Nom|Gender=Fem|Number=Sing). This explicitly injects grammatical information into the input.

### Model
- **Word2vec (CBOW)** trained using Gensim:
  - Vector size: 300
  - Window: 5
  - Minimum count: 2

### Analysis
- **Principal Component Analysis (PCA)** (unsupervised) – projects vectors onto principal components that maximize variance.
- **Linear Discriminant Analysis (LDA)** (supervised) – uses case labels to find directions that maximize between‑class separation while minimizing within‑class variance.

## 📉 Key Findings

| Method | Observation | Interpretation |
|--------|-------------|----------------|
| **PCA** | Clusters are poorly interpretable, no clear separation by case. Different cases are interleaved. | PCA ignores case labels. It finds high‑variance directions that may reflect semantics, frequency, or other grammatical features – not case. |
| **LDA** | Clusters are tight and oriented in different directions, but remain close together. No clear boundaries. | Word2vec vectors encode **weak case‑related signal** – not strong enough for clean separation, but directional consistency shows LDA can extract some structure. |

### Conclusion
> Static word2vec embeddings do **not** robustly encode grammatical case information in a linearly separable way. The signal is present but weak, and overwhelmed by lexical semantics and frequency. For morphology‑sensitive tasks, subword‑aware models (FastText) or contextualized embeddings (BERT) may be more promising.
