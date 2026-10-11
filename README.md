# **Polarity-Aware Probing for Quantifying Latent Alignment in Language Models**

Sabrina Sadiekh\*, Elena Ericheva\*, Chirag Agarwal (\*equal contribution) — AAAI 2026

[Paper (arXiv:2511.21737)](https://arxiv.org/abs/2511.21737) | [🤗 Datasets](https://huggingface.co/collections/SabrinaSadiekh/polarity-aware-probing-datasets-693541679832b1eedd53a928) | [Supplementary materials](docs/supplementary.pdf)

![img](teaser/ccs_teaser.png)

---

## **Abstract**

Advances in unsupervised probes like Contrast‑Consistent Search (CCS), which reveal latent beliefs without token outputs, raise the question of *whether they can reliably assess model alignment*. We investigate this by examining CCS's sensitivity to harmful vs. safe statements and introducing Polarity‑Aware CCS (PA‑CCS), which evaluates whether a model's internal representations remain consistent under polarity inversion. We propose two alignment-oriented metrics -- Polar‑Consistency and Contradiction Index -- to quantify the semantic robustness of a model's latent knowledge. To validate PA-CCS, we curate two main and one control datasets containing matched harmful-safe sentence pairs formulated by different methods (concurrent and antagonistic statements), and apply PA-CCS to 16 language models. Our results demonstrate that PA‑CCS reveals both architectural and layer-specific differences in the encoding of latent harmful knowledge. Interestingly, replacing the negation token with a meaningless marker degrades the PA‑CCS scores of models with aligned representations. In contrast, models lacking robust internal calibration do not show this degradation. Our findings highlight the potential of unsupervised probing for alignment evaluation and call on the community to incorporate structural robustness checks into interpretability benchmarks.

**WARNING:** This paper contains potentially sensitive, harmful, and offensive content.

---

## Datasets

The paper includes the release of new datasets containing contrasting pairs of "harmful/safe" statements.

### Dataset Overview

| Dataset | Link | Total Samples | Unique Statements | Harm-Safe Pairs | Purpose |
|---------|------|--------------|-------------------|-----------------|---------|
| Mixed dataset | [🤗 Huggingface](https://huggingface.co/datasets/SabrinaSadiekh/mixed_hate_dataset) | 1244 | 1219 | 622 | Tests whether CCS can distinguish harmful from safe beliefs in realistic, naturally varied formulations |
| Not dataset | [🤗 Huggingface](https://huggingface.co/datasets/SabrinaSadiekh/not_hate_dataset) | 1250 | 1241 | 625 | Direct evaluation of how the model handles polarity flips in tightly aligned sentences |

Some statements appear in more than one pair, so the number of unique statements is lower than the number of samples.

Updated 15 pairs (mixed) and 22 pairs (not) on 11.10.2026.

The control *ttt* dataset (and the other random-token controls: `eps`, `moo`, `432`, `////`, `urm`) is built on the fly from the Not dataset by replacing `not` with the placeholder token (see `notebooks/random_tokens/`).

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
<td colspan="3"><strong>"Not" Dataset</strong></td>
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

### Data format

From $0, 1, \dots, N-1$ rows, the first $\frac{N}{2}$ are harmful (label `0`) and the next $\frac{N}{2}$ are safe (label `1`); row $i$ is paired with row $i + \frac{N}{2}$. The column `is_harmfull_opposition` holds the label. Files in `data/yes_no/` are the raw statements with the CCS suffix appended (`" Yes."` / `" No."` for Mixed, `"Yes."` / `"No."` for Not).

---

## Models

16 models, grouped as small (<2B) and large (≥2B). Hidden states are taken from the first token (encoders), the last token (decoders), or both (encoder-decoder).

| Group | Model | Layers | Fine-tuned | HF ID |
|---|---|---|---|---|
| Encoder-only (small) | DeBERTa Base | 13 | × | `microsoft/deberta-base` |
| | DeBERTa Large | 25 | × | `microsoft/deberta-large` |
| | DeBERTa Large | 25 | ✓ | `Elron/deberta-v3-large-hate` |
| Decoder-only (small) | GPT-2 | 13 | × | `gpt2` |
| | GPT-2 Large | 37 | × | `gpt2-large` |
| | GPT-Neo 125M | 13 | ✓ | `ybelkada/gpt-neo-125m-detox` |
| Encoder-decoder (small) | BERT Base | 12+12 | × | `google-bert/bert-base-uncased` |
| | BERT Base | 12+12 | ✓ | `ayushdh96/HateSpeech_Bert_Base_Uncased_Fine_Tuned` |
| | BERT Base | 12+12 | ✓ | `ctoraman/hate-speech-bert` |
| Decoder-only (large) | Llama 3 8B | 33 | × | `meta-llama/Meta-Llama-3-8B` |
| | Llama 3 8B Instruct | 33 | ✓ | `meta-llama/Meta-Llama-3-8B-Instruct` |
| | Llama Guard 2 8B | 33 | ✓ | `meta-llama/Meta-Llama-Guard-2-8B` |
| | Gemma 2 2B | 27 | × | `google/gemma-2-2b` |
| | Gemma 2 2B IT | 27 | ✓ | `google/gemma-2-2b-it` |
| | Gemma 2 9B | 43 | × | `google/gemma-2-9b` |
| | Gemma 2 9B IT | 43 | ✓ | `google/gemma-2-9b-it` |

---

## **How to use this repository?**

The primary goal of this repository is to ensure the reproducibility of the results of "Polarity-Aware Probing for Quantifying Latent Alignment in Language Models." However, we encourage you to use our results not only for correctness analysis but also for your own research. You can:

1. Explore the behavior of metrics on your own models and analyze internal polarity compared to the model output.
2. Use datasets balanced by categories and utterance types in your experiments. Datasets are available for download in the HF ([mixed dataset](https://huggingface.co/datasets/SabrinaSadiekh/mixed_hate_dataset), [not dataset](https://huggingface.co/datasets/SabrinaSadiekh/not_hate_dataset)).
3. Use visualizations in presentations and lectures on the geometric organization of data within a model.
4. Explore [`docs/supplementary.pdf`](docs/supplementary.pdf) for details about related works, the list of models, performance metrics by model architecture, separation analysis, robustness of results on meaningless placeholders, and examples of pairs from the datasets.

### Where to find each figure

| Paper figure | Source |
|---|---|
| Fig. 1E (PC–CI regions) | `notebooks/analysis/trade_off_plots.ipynb`, `results/figures/teaser.pdf` |
| Figs. 2–5 (aggregated metrics, encoder vs decoder, PC–CI trade-off, fine-tuning) | per-layer metrics in `results/metrics/*.csv`; Fig. 5 in `results/figures/fine_tuning.pdf` |
| Fig. 6 (metrics by architecture) | `results/figures/metrics/` |
| Figs. 7–8 (separation analysis) | `results/figures/separation/` |
| Figs. 9–10 (random-token robustness) | `notebooks/random_tokens/` |

---

## **Files and folders**

```bash
code/
  ├── ccs.py
  ├── extract.py
  ├── extract_llama.py
  └── format_results.py

data/
  ├── raw/
  │   ├── mixed_dataset.csv
  │   └── not_hate_dataset.csv
  └── yes_no/
      ├── mixed_dataset_no.csv
      ├── mixed_dataset_yes.csv
      ├── not_dataset_no.csv
      └── not_dataset_yes.csv

notebooks/
  ├── main/            # PA-CCS on DeBERTa-large-FT, Llama 3 8B (base, instruct, guard), Gemma 2 (2B, 9B, base, it)
  ├── random_tokens/   # random-token controls: BERT-base-FT (encoder, decoder), DeBERTa-large-FT, GPT-2
  └── analysis/        # PC–CI region plot

results/
  ├── metrics/         # per-layer ESA / PC / CI for all models and datasets (mixed, not, ttt)
  └── figures/

docs/
  └── supplementary.pdf
```

### **Description**

#### `code/` — Source Code for PA-CCS Evaluation

This directory contains all the necessary scripts for reproducing the experiments described in our paper on **Polarity-Aware Probing for Quantifying Latent Alignment in Language Models** for analyzing internal representations in language models.

The code is organized to cleanly separate:

- Feature extraction (`extract.py`, `extract_llama.py`)
- Probing & analysis (`ccs.py`)
- Evaluation & reporting (`format_results.py`)

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

- All scripts assume that the input data is already formatted as sentence pairs with polarity labels (see [Data format](#data-format)).
- The model interface is built on top of HuggingFace converters.
- Notebooks add `code/` to the Python path relative to their own folder, so run them from `notebooks/<subfolder>/`.

#### `notebooks/` — PA-CCS Evaluation Notebooks

Each notebook in `notebooks/main/` runs the full pipeline for one model:

- Extract hidden representations
- PA-CCS training
- Calculate and format ESA/PC/CI metrics
- Visual diagnostics of metrics and separations

Small models were run with the same pipeline as `ccs_deberta_pretr.ipynb`; their per-layer results are in `results/metrics/all_small_models.csv`.

#### Requirements

- Python ≥ 3.9
- `transformers`, `torch`, `datasets`, `numpy`, `scikit-learn`, `matplotlib`, `seaborn`, `pandas`, `sentencepiece`, `accelerate`, `tqdm`
- For LLaMA and Gemma models: use HF-compatible checkpoints (with proper access)

---

## Citation

```bibtex
@misc{sadiekh2025polarityaware,
  title={Polarity-Aware Probing for Quantifying Latent Alignment in Language Models},
  author={Sadiekh, Sabrina and Ericheva, Elena and Agarwal, Chirag},
  year={2025},
  eprint={2511.21737},
  archivePrefix={arXiv},
  primaryClass={cs.CL},
  url={https://arxiv.org/abs/2511.21737}
}
```

## **Contact**

For questions or collaborations:

- 📧 **Sabrina Sadiekh, email** — sadsobr7@gmail.com
- 📧 **Ericheva Elena, email** — elena.ericheva01@gmail.com
- 📧 **Agarwal Chirag, email** — qze3wn@virginia.edu
- 📧 **Sabrina Sadiekh, telegram** — @sabrina_sadiekh
