# Comparative Analysis of ChromaDB and FAISS for Multilingual Retrieval

This notebook presents a hands-on evaluation of **ChromaDB** and **FAISS** for vector search tasks using multilingual embeddings. Inspired by Abo Bakr's tutorial, the project adapts key steps, introduces data expansion, and assesses retrieval performance in CPU-bound environments.

## Dataset & Preprocessing

- **Primary dataset:** Sadeem  
  - Split into:
    - **Main DataFrame** containing question–answer entries (some rows with missing answers)
    - **Meta DataFrame** with `source` and `title` columns (included for structure; not essential)
  - **Evaluation set:** 4037 questions  

- **Expansion dataset:** SANAD  
  - Used the `article` column to expand the `text` field in Sadeem  
  - Purpose: To introduce noise and stress-test retrieval systems  
  - **Document pool for retrieval:** 35,000 records (entries with non-empty `answer` cells)

## Text Embedding

- **Model:** `sentence-transformers/distiluse-base-multilingual-cased-v2`  
  - Embedding dimension: 512  
  - Execution: Google Colab (CPU)  
    - Model loading was slower compared to GPU-powered environments shown in reference videos

## Vector Database Implementations

### ChromaDB

- Record count after expansion: 35,000
- Retrieval tested using 4037 questions

- **Retrieval speed:**
  - Before expansion (local CPU): ~11 seconds
  - After expansion (Colab CPU): ~7 ms

- **Accuracy evaluation:**
  ```json
  {'valid': 1, 'similar': 1310, 'invalid': 2726}
  ```
  - **Valid:** Retrieved document ID matches ground truth
  - **Similar:** Retrieved document from same source
  - **Invalid:** Unrelated document  
  - Evaluation logic still under review

### FAISS

- Applied embedding normalization
- Retrieval tested using same 4037 questions

- **Retrieval speed:**
  - Before expansion (local CPU): ~16 seconds
  - After expansion (Colab CPU): ~23.7 ms

- **Accuracy evaluation:**
  ```json
  {'valid': 1, 'similar': 1310, 'invalid': 2726}
  ```

## Observations & Notes

- Retrieval was performed on **4037 queries** against a document pool of **35,000 records**
- Accuracy results for both databases were identical, though further validation is ongoing
- Initial execution using **DataFrames** added latency—retrieving from **dictionary structures** proved faster
- All processing was done on **Colab CPU**, which influenced execution speed compared to GPU environments
- Metrics highlight practical semantic similarity and document alignment


## Summary of Insights
Through this comparative analysis, one key observation emerged: ChromaDB scales more efficiently than FAISS in document-heavy scenarios, particularly when running on CPU. With a pool of 35,000 documents, ChromaDB maintained low retrieval times, especially when dictionary-based structures were used for optimization. In contrast, FAISS exhibited increased latency as the document count grew, despite performing competitively in smaller-scale tests. Interestingly, on the local CPU, ChromaDB also outperformed FAISS on smaller datasets (4037 records), highlighting its overall responsiveness across different data volumes. These nuances underscore the importance of considering both data size and runtime environment when choosing a retrieval framework—especially for real-world, resource-constrained deployments.
