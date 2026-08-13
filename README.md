# N-gram Language Modeling and LSTM for Protein Sequence Prediction

Comparing a classic statistical approach against a neural one on the same task: predicting the next amino acid in a protein sequence.

## Overview

Protein sequences share a property with natural language: the order of tokens isn't random, and nearby tokens carry information about what comes next. That similarity makes it reasonable to apply the same tools used for language modeling to amino acid sequences.

This project builds two families of models for next-amino-acid prediction and evaluates them on the same held-out test set:

- **N-gram models** (unigram, bigram, trigram) with add-α smoothing, evaluated using perplexity.
- **An LSTM sequence model** (TensorFlow/Keras), evaluated using cross-entropy and accuracy.

## Data

Protein sequences for human, mouse, and rat were downloaded from **UniProt**. Only the raw amino acid sequences are used (FASTA headers are discarded). Sequences are filtered to a length of 10-1000 amino acids and split 80/20 into train and test sets.

## Approach

**N-gram models** count how often each amino acid (or pair, or triplet) follows a given context in the training data, with add-α smoothing so unseen combinations don't get a probability of zero.

**LSTM model**: each sequence is converted into (prefix → next amino acid) pairs, padded to a fixed length, and fed through an embedding layer, an LSTM layer, and a softmax output over the 20 amino acids.

## Results

| Model | Metric | Score |
|---|---|---|
| Unigram | Perplexity | 17.93 |
| Bigram | Perplexity | 17.72 |
| Trigram | Perplexity | 17.17 |
| LSTM | Cross-entropy | 2.70 |
| LSTM | Accuracy | 16.9% |

A few honest notes on these numbers:

- Perplexity improves slightly as more context is added, which suggests neighboring amino acids do carry some predictive signal — but not a lot. Protein sequences are far less locally predictable than natural language.
- 16.9% LSTM accuracy sounds low, but with 20 possible amino acids, random guessing scores about 5%. The model is picking up a real (if weak) signal, consistent with how little the N-gram models could extract from local context either. Predicting the next residue from a short local window is a genuinely hard problem — most of what determines a protein's sequence isn't visible in a short window around it.
- With more training data, a larger model, or additional biological features (e.g. secondary structure), there's likely more room for the LSTM to improve than for the N-gram models, since it isn't limited to a fixed context size.

## Tech Stack

- **NumPy / Pandas** — data handling and statistics
- **TensorFlow / Keras** — LSTM model
- **Matplotlib / Seaborn** — visualizing amino acid and bigram/trigram distributions

## Project Structure

```
.
├── notebook/
│   └── ngram_lstm_protein_prediction.ipynb
├── requirements.txt
└── README.md
```

## How to Run

1. Install the dependencies:
   ```
   pip install -r requirements.txt
   ```
2. Download the human, mouse, and rat FASTA files from UniProt and place them in a `data/` folder.
3. Open `notebook/ngram_lstm_protein_prediction.ipynb` and run it top to bottom.

## Notes

This was originally a university course project. The LSTM here is deliberately small (a single layer, no hyperparameter search) since the goal was to compare it against the statistical baseline rather than to push for state-of-the-art accuracy.
