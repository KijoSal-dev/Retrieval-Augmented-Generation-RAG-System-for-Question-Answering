# Agriculture RAG System for Question Answering

A notebook-based Retrieval-Augmented Generation (RAG) application for asking questions about an agricultural report. The system extracts PDF text, splits it into overlapping chunks, retrieves relevant passages with FAISS, and generates a response grounded in the retrieved context.

Built as a learning project in Data and Artificial Intelligence track.

## How it works

1. Load and extract text from a PDF.
2. Split the text into chunks (the notebook's default is 700 characters with 100 characters of overlap).
3. Embed chunks with `sentence-transformers/all-MiniLM-L6-v2` and index them in FAISS.
4. Retrieve relevant chunks for a question.
5. Generate a context-based answer with `google/flan-t5-small`.

## Evaluation Results and Data Quality Assessment

### 1. Test Dataset Overview

The RAG evaluation was conducted using a 20-question reference test set based on the Kenya National Agriculture Production Report 2025.

| Evaluation category | Number of questions |
|---|---:|
| Total questions | 20 |
| Answerable questions | 17 |
| Unanswerable questions | 3 |
| Answerable questions with target page references | 17 |
| Answerable questions missing target references | 0 |

All 17 answerable questions contain populated target identifiers corresponding to existing PDF page numbers. The three unanswerable questions correctly have no target page reference.

### 2. Retrieval Evaluation and Index Alignment

The initial evaluation recorded a Hit Rate@3 of 0% and a Mean Reciprocal Rank (MRR) of 0.00 across the 17 answerable questions.

Further inspection identified an evaluation metadata mismatch:

- The FAISS database contains 560 indexed documents originating from a 202-page PDF.
- The indexed document metadata includes PDF page numbers (`page`) and page labels (`page_label`).
- The indexed documents do not contain an explicit `chunk_id` metadata field.
- The evaluation code attempts to compare target identifiers against `metadata["chunk_id"]`.

Consequently, the retrieval evaluation compares populated target page references against a metadata field that does not exist in the indexed documents. This produces zero retrieval matches.

Interpretation: The initial Hit Rate@3 and MRR results are invalid as measures of actual retrieval quality because of the identifier mismatch. They should not be interpreted as evidence that the retriever failed.

The evaluation requires correction by comparing target page references against the retrieved documents' page metadata, followed by a fresh evaluation. The corrected results must then be checked against the actual source passages to confirm reference alignment.

### 3. Refusal Accuracy Evaluation

The system was evaluated using three unanswerable questions designed to test whether it refuses to generate unsupported information.

| Metric | Result |
|---|---:|
| Unanswerable questions | 3 |
| Correct refusals | 3 |
| Incorrect refusals | 0 |
| Refusal accuracy | 100% |

The system correctly returned the expected refusal response for all three unanswerable questions.

Although this represents a 100% observed refusal accuracy, the small sample size limits the generalisability of the result. Further testing with a larger unanswerable question set is recommended.

### 4. Human Evaluation Status

A human evaluation template was generated to support manual assessment of the RAG system.

The template includes:

- Human correctness score.
- Faithfulness score.
- Answer relevance score.
- Hallucination detection.
- Evaluator comments.

These fields have been prepared for manual assessment but have not yet been completed. Therefore, the project does not currently claim completed human evaluation results.

Context relevance should also be assessed as a separate dimension if independent evaluation of retrieved context quality is required.

### 5. Ground-Truth Reference Quality

Inspection of the 17 answerable test questions identified that 16 use table references as their ground-truth entries, while one provides a numerical reference answer.

Examples include:

- Table 3.2: Area, Production and Value of Maize.
- Table 3.10: Beans Production and Value.
- Tables 3.4 and 3.5: Rice Production and Mwea Irrigation Scheme.
- Table 5.2.1(a): Tea Production.
- Table 7.9: Milk Production and Intake.

Although these references identify the relevant locations within the report, table citations alone do not provide sufficiently detailed expected answers for rigorous automated answer-correctness evaluation.

The ground-truth dataset should therefore be refined to include the expected numerical values, relevant years, units and descriptive findings extracted from the corresponding tables.

The three unanswerable questions are explicitly labelled as not answerable from the report.

### 6. Overall Evaluation Assessment

The project has successfully implemented the core RAG evaluation pipeline, including retrieval timing, generation timing, Hit Rate@3, MRR, refusal assessment, chunk-size experimentation and preparation of a human evaluation template.

However, the initial retrieval metrics require correction, the ground-truth references require refinement, and human evaluation remains outstanding.

The next stage is to correct the page-reference alignment, rerun the retrieval evaluation and complete the manual quality assessment before presenting definitive retrieval and answer-quality performance results.

### End-to-end evaluation

| Metric | Result |
|---|---:|
| Evaluation questions | 20 |
| Average retrieval latency | 0.081 s |
| Average generation latency | 1.456 s |
| Average total latency | 1.537 s |
| Unanswerable refusal accuracy | 100% (3/3) |
| Hit Rate @ K | 0.0 (not currently valid; see note) |
| Mean Reciprocal Rank (MRR) | 0.0 (not currently valid; see note) |

### Chunk-size experiment

All three configurations were tested on the same 20-question set. Matching accuracy is the value recorded by the notebook's chunk-size experiment.

| Chunk size (characters) | Matching accuracy | Average retrieval latency | Indexed chunks |
|---:|---:|---:|---:|
| 300 | 35% | 0.0373 s | 1,436 |
| **700 (selected)** | **60%** | **0.0340 s** | **560** |
| 1,000 | 55% | 0.0510 s | 418 |

In this experiment, 700-character chunks had the highest recorded matching accuracy and the lowest average retrieval latency of the tested settings. These results are specific to this document and evaluation set; they do not establish general performance.

### Metric limitations

- Hit Rate @ K and MRR are recorded as zero in the evaluation output because the target chunk IDs are missing (`None`) for the evaluated questions. These values do not demonstrate that retrieval failed; they are not usable retrieval-quality measurements until target chunk IDs are populated and the evaluation is rerun.
- Refusal accuracy is based on only three unanswerable questions.
- Human correctness, faithfulness, answer relevance, and context relevance scores have not been filled in. The answer examples should not be treated as a completed human quality evaluation.
- The evaluation includes answer references that are sometimes table citations rather than exact expected answers. Review and refine the ground-truth set before using it for rigorous answer-quality comparisons.

## Run the notebook

1. Open [`Agriculture_RAG_System.ipynb`](./Agriculture_RAG_System.ipynb) in Google Colab.
2. Run the setup and import cells.
3. Upload the agricultural PDF requested by the notebook.
4. Run the cells in order to build the vector index and launch the question-answering flow.
5. Use the evaluation cells to test questions and export results.

The notebook installs its Python dependencies, including LangChain, Sentence Transformers, FAISS, Transformers, PyTorch, SentencePiece, and PyPDF.

## Repository contents

| File | Description |
|---|---|
| [`Agriculture_RAG_System.ipynb`](./Agriculture_RAG_System.ipynb) | End-to-end notebook, experiments, evaluation, and export workflow |
| [`rag_evaluation_results.csv`](./rag_evaluation_results.csv) | Per-question retrieval, latency, and refusal evaluation output |
| [`rag_evaluation_summary.csv`](./rag_evaluation_summary.csv) | Aggregate evaluation metrics |
| [`rag_evaluation_template.csv`](./rag_evaluation_template.csv) | Per-question template for human evaluation |
| [`rag_human_evaluation_template.csv`](./rag_human_evaluation_template.csv) | Human evaluation worksheet |
| [`rag_human_evaluation_template.json`](./rag_human_evaluation_template.json) | JSON version of the human evaluation template |
| [`chunk_size_experiment_results.csv`](./chunk_size_experiment_results.csv) | Per-question chunk-size experiment results |
| [`chunk_size_experiment_summary.csv`](./chunk_size_experiment_summary.csv) | Aggregate chunk-size comparison |
| [`answerable_questions_results.csv`](./answerable_questions_results.csv) | Example generated answers for answerable questions |
| [`faiss_metadata_inspection.csv`](./faiss_metadata_inspection.csv) | FAISS metadata inspection export for page and chunk provenance checks |
| [`faiss_metadata_inspection.json`](./faiss_metadata_inspection.json) | JSON export of the FAISS metadata inspection results |
| [`target_id_alignment_results.csv`](./target_id_alignment_results.csv) | Alignment between evaluation questions and their target chunk or page IDs |
| [`target_id_alignment_results.json`](./target_id_alignment_results.json) | JSON export of the target ID alignment results |
| `my_pdf_database/` | Persisted FAISS index and associated metadata, when available |

## Technology

- Python and Google Colab
- LangChain text splitters and Hugging Face integrations
- Sentence Transformers (`all-MiniLM-L6-v2`)
- FAISS
- Hugging Face Transformers (`google/flan-t5-small`)
- PyPDF

