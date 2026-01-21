# rag-eval-medical-manual
RAG QA system over a medical reference PDF with an evaluation framework to measure correctness, faithfulness, and retrieval quality across configurations.

# RAG Evaluation Case Study — Medical Diagnosis Manual (PDF)
This project builds a Retrieval-Augmented Generation (RAG) QA system over a single PDF (“Medical Diagnosis Manual”) and evaluates it with: a curated question set, controlled experiments, and structured error analysis.

## Problem
Given a medical diagnosis reference manual in PDF form, how do retrieval and prompting choices affect:
- Answer correctness
- Faithfulness (grounded in retrieved text)
- Retrieval success (did we fetch the right evidence?)
- Latency

## Approach
### Data
- Source: `data/raw/Medical_Diagnosis_Manual.pdf`
- Derived artifacts:
  - Extracted text chunks + metadata
  - Vector index (FAISS)
  - Curated evaluation set: `data/eval/qa_set.jsonl`

### RAG Pipeline
1. Extract text from PDF
2. Chunk text with configurable chunk sizes/overlap
3. Embed chunks and store in FAISS
4. For each query:
   - Retrieve top-k chunks
   - Generate an answer using an LLM
   - Return answer + cited chunk IDs/pages

## Evaluation Protocol
### Question Set
A curated set of questions with expected answers and (when possible) a page/section reference.

Format: `data/eval/qa_set.jsonl`
Each line is one JSON object:
- `id`: unique string
- `question`: user question
- `expected_answer`: short reference answer (1–3 sentences)
- `source_hint`: optional (page/section/topic)
- `tags`: list (e.g., `["diagnosis", "criteria"]`)

### Metrics
- **Retrieval Hit Rate**: whether retrieved context contains the correct/most relevant section (manual or heuristic)
- **Correctness**: human rating (0/1 or 1–5)
- **Faithfulness**: whether the answer is supported by retrieved text (0/1)
- **Latency**: end-to-end response time

### Experiments
We compare configurations such as:
- `chunk_size`: 200 vs 500 vs 1000
- `top_k`: 3 vs 5 vs 10
- `prompt_template`: A vs B
- (optional) embedding model variants

Outputs:
- `results/results.csv` — per-question results and aggregate metrics
- `notebooks/analysis.ipynb` — plots + error analysis


