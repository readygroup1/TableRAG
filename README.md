# TableRAG: A More Effective Method for LLMs to Reason and Act Over Large Datasets

This project **re-implements and extends TableRAG**, a retrieval-augmented generation (RAG) system designed to let large language models reason over **very large tables (up to millions of cells)** that traditionally do not fit within an LLM’s context window.

This project focuses on **system design, retrieval strategies, and empirical evaluation**, not just prompt engineering. The reason for this expirement is derived from how LLMs are traditionally fed tables which:
- Fails for large tables due to token limits
- Is slow, expensive, and error-prone

**TableRAG decouples table size from prompt size** by retrieving only the relevant schema and values. This project demonstrates when this approach works well using real benchmarks.

See [`./report.pdf`](./report.pdf) for the written research report of our project.

---

## Construction

We re-implemented an **end-to-end TableRAG pipeline**:

- **Table ingestion & indexing**
  - Schema indexing (column names, types, example values)
  - Cell-value indexing with configurable encoding budgets
- **Retrieval layer**
  - Schema + cell retrieval using embeddings
  - Query expansion to improve recall
- **Reasoning layer**
  - PyReAct (Reason + Act) agent
  - LLM generates executable Pandas code for filtering, aggregation, and comparison
- **Execution & answer generation**
  - Deterministic code execution over real table data
  - Final natural-language answer grounded in execution results

The system is **modular and extensible**, allowing retrieval strategies, models, prompts, and agents to be swapped independently.

---

## Benchmarks & Results

We reconstructed and evaluated TableRAG on three major datasets:

- **TabFact** (fact verification over tables)
- **BIRD-QA** (large-scale database QA, reconstructed from Text-to-SQL)
- **Arcade-QA** (natural language → value questions, reconstructed from code outputs)

Key outcomes:
- Successfully reproduced **TabFact accuracy (~85%)**, matching published results
- Confirmed that **retrieval-based expansion improves accuracy on large tables**
- Showed performance is **highly sensitive to preprocessing and retrieval coverage**, an under-emphasized limitation in the original paper

---

## Findings

Beyond replication, we conducted a novel systematic study of the **cell encoding budget** (the parameter controlling how many table values are embedded). We found that:

- Accuracy follows a **U-shaped curve**:
  - Very small budgets (≈5 cells) and large budgets (10K–20K) outperform mid-range values
- **Best performance occurs when ~10–20% of a table’s cells are indexed**
  - Absolute budget size matters less than *budget-to-table-size ratio*
- Encoding more than **50K cells provides no benefit**, even for million-cell tables
- Schema retrieval provides most of the signal; cell retrieval mainly grounds ambiguity

These results refine the original TableRAG recommendations and provide **practical guidance for cost-efficient deployment**.

---

## Tech Stack

- **Python**
- **FAISS** for vector search
- **OpenAI embeddings & LLMs** (GPT-3.5, GPT-4o-mini)
- **Pandas** for execution

---

## Reproducibility

Due to dataset size, full databases are not included in this repo. Processed datasets, experiment outputs, and step-by-step reproduction instructions are linked in the project report.

---

## Disclaimers

This repository contains code released by
[Google Research](https://research.google).

*This is not an official Google product.*

All datasets in this repository are released under the CC BY 4.0 International
license, which can be found here:
https://creativecommons.org/licenses/by/4.0/legalcode.  All source files in this
repository are released under the Apache 2.0 license, the text of which can be
found in the LICENSE file.

---

Because the repo is large, we recommend you download only the subdirectory of
interest:

* Use GitHub editor to open the project. To open the editor change the url from
github.com to github.dev in the address bar.
* In the left navigation panel, right-click on the folder of interest and select
download.

If you'd like to submit a pull request, you'll need to clone the repository;
we recommend making a shallow clone (without history).

```
git clone git@github.com:google-research/google-research.git --depth=1
```

