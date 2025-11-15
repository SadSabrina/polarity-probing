# **"Polarity-Aware Probing for Quantifying Latent Alignment in Language Models."**

Source code for the paper ["Polarity-Aware Probing for Quantifying Latent Alignment in Language Models"]().

![img](https://raw.githubusercontent.com/SadSabrina/ccs_2/refs/heads/main/teaser/ccs_teaser.png)

---

## **Abstract**

Advances in unsupervised probes like Contrast‑Consistent Search (CCS), which reveal latent beliefs without token outputs, raise the question of **whether they can reliably assess model alignment*. We investigate this by examining CCS's sensitivity to harmful vs. safe statements and introducing Polarity‑Aware CCS (PA‑CCS), which evaluates whether a model's internal representations remain consistent under polarity inversion. We propose two alignment-oriented metrics -- Polar‑Consistency and Contradiction Index -- to quantify the semantic robustness of a model's latent knowledge. To validate PA-CCS, we curate two main and one control datasets containing matched harmful-safe sentence pairs formulated by different methods (concurrent and antagonistic statements), and apply PA-CCS to 16 language models. Our results demonstrate that PA‑CCS reveals both architectural and layer-specific differences in the encoding of latent harmful knowledge. Interestingly, replacing the negation token with a meaningless marker degrades the PA‑CCS scores of models with aligned representations. In contrast, models lacking robust internal calibration do not show this degradation. Our findings highlight the potential of unsupervised probing for alignment evaluation and call on the community to incorporate structural robustness checks into interpretability benchmarks. 
**WARNING:** This paper contains potentially sensitive, harmful, and offensive content.

---

## Datasets

The paper includes the release of new datasets containing contrasting pairs of "harmful/safe" statements.

### Dataset Overview

| Dataset | Link | Total Samples | Unique Observations | Harm-Safe Pairs | Purpose |
|---------|------|--------------|---------------------|-----------------|---------|
| Mixed dataset | [🤗 Huggingface](https://huggingface.co/datasets/SabrinaSadiekh/mixed_hate_dataset) | 1244 | 1244 | 622 | Tests whether CCS can distinguish harmful from safe beliefs in realistic, naturally varied formulations |
| Not dataset | [🤗 Huggingface](https://huggingface.co/datasets/SabrinaSadiekh/not_hate_dataset) | 1250 | 1250 | 625 | Direct evaluation of how the model handles polarity flips in tightly aligned sentences |

### Dataset Construction Details

<table>
<thead>
<tr>
<th>Method/Category</th>
<th>Description</th>
<th>Proportion</th>
</tr>
</thead>
<tbody>
<tr>
<td colspan="3"><strong>"Mixed" Dataset</strong></td>
</tr>
<tr>
<td>Concurrent-based</td>
<td>Harmful and safe statements differ by rephrasing, while preserving semantic opposition</td>
<td>72.3%</td>
</tr>
<tr>
<td>Negation-based</td>
<td>One statement is the syntactic negation of the other</td>
<td>27.7%</td>
</tr>
<tr>
<td colspan="4"><strong>"Not" Dataset</strong></td>
</tr>
<tr>
<td>Harmful sentences (625)</td>
<td>Contains "not" in 52.8% of statements where $\texttt{not}(x^{\text{harm}}) = x^{\text{safe}}$</td>
<td>52.8%</td>
</tr>
<tr>
<td>Safe sentences (625)</td>
<td>Contains "not" in 47.52% of statements where $x^{\text{harm}} = \texttt{not}(x^{\text{safe}}$)</td>
<td>47.52%</td>
</tr>
</tbody>
</table>

---

## **How to use this repository?**

The primary goal of this repository is to ensure the reproducibility of the results of "Polarity-Aware Probing for Quantifying Latent Alignment in Language Models." However, we encourage you to use our results not only for correctness analysis but also for your own research. You can:

1. Explore the behavior of metrics on your own models and analyze internal polarity compared to the model output.
2. Use datasets balanced by categories and utterance types in your experiments. Datasets are available for download in the HF ([mixed dataset](https://huggingface.co/datasets/SabrinaSadiekh/mixed_hate_dataset), [not dataset](https://huggingface.co/datasets/SabrinaSadiekh/not_hate_dataset)).
3. Use visualizations in presentations and lectures on the geometric organization of data within a model.
4. Explore the file `Supplementaty materials for the paper` to find details about Related works, List of Models Used in Experiments, Performance Metrics by Model Architecture, Separation Analysis, Robustness of Results on Meaningless
Placeholder and Examples of pairs from data sets.
---
---

## **Files and folders**

**Repo structure:**

```bash

'Suplementaty materials for the paper.pdf'

code/
  ├── ccs.py
  ├── extract.py
  ├── extract_llama.py
  ├── format_results.py

data/
  ├── raw/
  │   ├── mixed_dataset.csv
  │   └── not_dataset.csv
  ├── yes_no/
  │   ├── mixed_dataset_no.csv
  │   ├── mixed_dataset_yes.csv
  │   ├── not_dataset_no.csv
  │   └── not_dataset_yes.csv

notebooks/
  ├── ccs_deberta_pretr.ipynb
  ├── ccs_Meta-Llama-3-8B-Instruct.ipynb
  └── ...
  

```

### **Description**

#### `code/` — Source Code for PA-CCS Evaluation

This directory contains all the necessary scripts for reproducing the experiments described in our paper on **Polarity-Aware Probing for Quantifying Latent Alignment in Language Models** for analyzing internal representations in language models.

The code is organized to cleanly separate:

- Feature extraction (`extract.py`, `extract_llama.py`)
- Probing & analysis (`ccs.py`)
- Evaluation & reporting (`format_results.py`)

##### Files

##### `ccs.py`

Base implementation of the **Contrast Consistent Search (CCS)** method for linear probing of model representations.

- Implements the linear probing training and evaluation procedure.
- Used to compute CCS directions and evaluate the empirical separation accuracy (ESA) and introduced metrics: polar consistency (PC), and contradiction index (CI).

##### `extract.py`

Script for extracting hidden states from encoder, decoder and encoder-decoder based models.

##### `extract_llama.py`

Script for extracting representations from LLaMA — just `extract.py` adapted for MetaLlama8b models.

##### `format_results.py`

Post-processing utility that:

- Aggregates layerwise metric results (ESA, PC, CI).
- Computes group statistics (means, std, confidence intervals).
- Formats results for plotting or tabular reporting.

##### Usage Notes

- All scripts assume that the input data is already formatted as sentence pairs with polarity labels.
- The dataset with pairs should be organized as follows. From $0, 1, 2 .... N$ sentences, the first $\frac{N}{2}$ harm (or safe) and the next $\frac{N}{2}$ safe (or harm). For 0, the pair index is $\frac{N}{2}$, for 1, the pair index is $\frac{N}{2} + 1$ and so on.
- The model interface is built on top of HuggingFace converters.

#### **`notebooks/` — PA-CCS Evaluation Notebooks**

This folder contains Jupyter notebooks for running and plotting **Polarity-Aware CCS (PA-CCS)** on various language models. The notebooks are intended to reproduce key experiments and visualize model alignment behavior.

##### Files

- **`ccs_deberta_pretr.ipynb`**
  Runs CCS and PA-CCS on **DeBERTa-large-FT** (`Elron/deberta-v3-large-hate`). Includes:

- Extract hidden representations
- PA-CCC training
- Calculate and format ESA/PC/CI metrics
- Visual diagnostics of metrics and separations

- **`ccs_Meta-Llama-3-8B-Instruct.ipynb`**
  Applies PA-CCS to **Meta-LLaMA-3 8B Instruct** using reformulated harmful-safe statement pairs. Includes:

- Extract hidden representations
- PA-CCC training
- Calculate and format ESA/PC/CI metrics
- Visual diagnostics of metrics and separations

#### **`data/` — datasets introduced in paper**

#### Requirements

- Python ≥ 3.9
- `transformers`, `torch`, `datasets`, `numpy`, `scikit-learn`, `matplotlib`, `seaborn`, `pandas`, `sentencepiece
`, `accelerate`, `tqdm`
- For LLaMA models: use HF-compatible checkpoints (with proper access)
