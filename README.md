# RAG-OEQ — Chunking Strategy Evaluation for Retrieval-Augmented Generation

Supplementary material for my Master's thesis (University of Bologna): an evaluation of how **chunking strategy** affects retrieval performance in a RAG pipeline over scientific documents, using **open-ended questions**.

## Contents

| File | Description |
|---|---|
| `MaxSimilarityAccuracy_Final.pbix` | Power BI report with the full evaluation: similarity distributions, TP/FP/FN counts, precision/recall per condition, per-model comparisons, and chunk-level inspection pages. |

## Workflow

1. **Document conversion** — Scientific PDFs are converted to structured Markdown using [Nougat](https://github.com/facebookresearch/nougat), preserving section headings.
2. **Chunking** — Each document is segmented with two strategies:
   - **Fixed-length**: uniform character windows, ignoring document structure;
   - **Structure-aware**: boundaries aligned with Markdown section headings, keeping each section a coherent unit.
3. **Embedding** — Chunks and questions are embedded with sentence-transformer models (`all-mpnet-base-v2`, `all-distilroberta-v1`, `all-MiniLM-L6-v2`, `all-MiniLM-L12-v2`).
4. **Storage** — Chunks, questions, embeddings, and ground-truth document–question pairs are stored in PostgreSQL.
5. **Retrieval & scoring** — For every question, cosine similarity is computed against all chunks; a chunk is retrieved when max similarity exceeds the **0.5 threshold**. Retrievals are evaluated under three conditions: *Relevant*, *Adjacent Relevant* (retrieved chunks must be consecutive in the document), and *Structured-Adjacent*.
6. **Evaluation** — Retrievals are matched against ground truth to compute TP/FP/FN, precision, and recall per chunking strategy, model, and condition. Results are analyzed and visualized in the Power BI report.

## Key finding

Structure-aware chunking achieves perfect recall (1.00) on the ground-truth relevant pairs, while fixed-length chunking reaches 0.786: fragmenting semantically coherent sections across chunk boundaries dilutes each chunk's similarity signal below the threshold, producing false negatives.

## Opening the report

Open `MaxSimilarityAccuracy_Final.pbix` with [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (free). Data is imported into the file — no database connection is required to explore the report.

## Author

Mohammad Jamshidi — University of Bologna
