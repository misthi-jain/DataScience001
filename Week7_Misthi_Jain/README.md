# Week 7 - Document Question Answering System (RAG)

## Objective
Build a Retrieval-Augmented Generation (RAG) system that answers questions from a custom document by retrieving relevant text chunks and generating grounded answers using a language model.

## Dataset
Custom document: personal resume (Resume_MisthiJain.pdf)

## Pipeline

1. **Document Ingestion** - Extracted raw text from PDF using PyPDF2
2. **Text Chunking** - Split text into overlapping word-based chunks (chunk_size=40, overlap=8)
3. **Embedding Creation** - Converted chunks into vectors using `all-MiniLM-L6-v2` (384-dim)
4. **Vector Database** - Stored embeddings in a FAISS `IndexFlatL2` index
5. **Query Processing** - Converted user questions into embeddings using the same model
6. **Context Retrieval** - Retrieved top-3 nearest chunks via FAISS similarity search
7. **Answer Generation** - Used `google/flan-t5-base` to generate answers grounded in retrieved context

## Results

| Question | Answer |
|---|---|
| What projects has Misthi built? | UI/UX design (incorrect - retrieval miss, see Observations) |
| What programming languages does Misthi know? | C++, Java, Python, JavaScript |
| What ML models were used in the recommendation system project? | KNN, Decision Tree, Random Forest, and SVM |
| What is Misthi's CGPA? | 6.7/10 |

## Experiment: Chunk Size Comparison

Compared `chunk_size=40` vs `chunk_size=60`:

- chunk_size=40 → 11 total chunks
- chunk_size=60 → 7 total chunks

For the CGPA question, chunk_size=40 correctly retrieved the education chunk containing "CGPA: 6.7/10" in the top-3 results. chunk_size=60 failed to retrieve it at all, pulling only Summary/Contact/Skills chunks instead.

## Observations

- Smaller chunk size (40) performed better than larger chunk size (60) for this short, structured document
- Larger chunks blend multiple resume sections together, reducing retrieval precision
- The "What projects has Misthi built?" question retrieved incorrect chunks (Summary, Training) instead of the Projects section, because those chunks incidentally contained the word "projects" - a real limitation of small-scale semantic retrieval on short documents with limited chunk diversity
- Retrieval quality is highly sensitive to chunk size on small documents; this would likely stabilize with a larger, more content-rich document

## Tech Stack
- PyPDF2 - PDF text extraction
- sentence-transformers (`all-MiniLM-L6-v2`) - embeddings
- FAISS - vector similarity search
- transformers (`google/flan-t5-base`) - answer generation

## System Metrics

- Document: Resume_MisthiJain.pdf
- Chunking: chunk_size=40 words, overlap=8 words
- Total chunks: 11
- Embedding model: all-MiniLM-L6-v2, dim=384
- Vector store: FAISS IndexFlatL2
- Generation model: google/flan-t5-base
