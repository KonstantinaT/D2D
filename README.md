# D2D predictor
A constrained protein large language model for predicting protein stability, function, and epistasis.

This repository provides the codebase and data resources associated with our upcoming paper (citation to be added).

<img width="1920" height="1080" alt="D2D_manuscript (23)" src="https://github.com/user-attachments/assets/224b6f2e-13f2-4e20-8b7d-4c1d0decd500" />

Part of the analysis builds on the supervised variant of D2D, **D2Deep**. For details on its training procedure and datasets, please refer to the D2Deep GitHub repository: https://github.com/KonstantinaT/D2Deep/.

Table of contents
=================

<!--ts-->
   * [Input pre-processing](https://github.com/KonstantinaT/D2D/blob/main/README.md#input-pre-processing)
   * [Feature Extraction & Pathogenicity Prediction]()
   * [Software and Hardware requirements]()
   * [Datasets]()
<!--te-->


Input pre-processing
============

Generates all possible single-point amino-acid substitutions for a given protein, producing the input CSV files required by the D2D/D2Deep pipeline.
Code: input_preprocessing.ipynb

**Output:** `<uniprot>_all.csv` — one row per single-point mutant (19 × sequence length rows for a full scan).


Feature Extraction & Pathogenicity Prediction
============
Core pipeline that computes D2D  features and runs D2Deep cancer driver classification predictors.

**What it does:**
1. **MSA processing** — Loads a multiple sequence alignment (`.a3m`), filters sequences by identity to the query (>50%) and gap content (≤30%), with automatic fallback to relaxed thresholds for shallow alignments.
2. **ProtT5 embedding** — Encodes the filtered MSA and each mutant sequence using [ProtT5-XL-UniRef50](https://huggingface.co/Rostlab/prot_t5_xl_uniref50), then applies max-pooling to reduce per-residue embeddings from 1024 to 20 dimensions.
3. **D2D feature calculation** — Fits a single-component GMM at each sequence position on the WT MSA embeddings. For every mutant, computes the difference in log-likelihood between the wild-type and mutant at each position, producing a per-mutation feature vector.
4. **D2Deep prediction** — Feeds the feature vectors (zero-padded to 2200) into a pre-trained two-layer neural network classifier and outputs a sigmoid probability score per variant.
5. **Confidence scoring** — Combines the D2Deep prediction with a min-max normalized MSA log-probability to assign a final confidence score.


Software and Hardware requirements
============
**Requirements:** Python 3.10+, PyTorch, Transformers, EVcouplings, scikit-learn, Biopython. A GPU is strongly recommended for ProtT5 inference.
For a representative protein of 344 amino acids (approximately the median length of a human protein) with an MSA of 647 sequences, the total computation time for all possible single mutations is summarized below:
<img width="353" height="132" alt="image" src="https://github.com/user-attachments/assets/1cbd8cdd-195e-4f7a-9196-5b991ed43895" />


Datasets
============

Datasets containing predictions and test sets can be downloaded through Zenodo: TBD
