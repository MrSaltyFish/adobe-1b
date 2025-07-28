# 🧠 Challenge 1B - PDF Insight Extractor (SegFault - Adobe India Hackathon)

This project performs **semantic PDF analysis** using a Retrieval-Augmented Generation (RAG) pipeline built entirely offline. It takes a job-to-be-done prompt, processes PDFs, and returns relevant, structured outputs using TinyLlama.

---

## 🚀 Approach

We followed a modular, high-performance pipeline:

1. **PDF Parsing & Chunking**
   - All PDFs are parsed using PyMuPDF.
   - Each PDF is split into overlapping text chunks optimized for semantic search.

2. **Embedding Chunks**
   - We use `all-MiniLM-L12-v2` from `sentence-transformers` to embed text chunks into vector space.

3. **Semantic Search**
   - Cosine similarity is computed between task queries and chunk vectors.
   - Top 5 semantically similar chunks are selected across all PDFs.

4. **LLM-Based Refined Text**
   - We use a local TinyLlama model to generate context-aware refined text.

6. **Output Formatting**
   - Results are written to `challenge1b_output.json`, including metadata, matched chunks, and generated refined text.

---

## 🔍 Models & Libraries Used

| Component           | Model / Library                        |
|---------------------|----------------------------------------|
| PDF Parsing         | `PyMuPDF` (fitz)                       |
| Text Embedding      | `sentence-transformers` - MiniLM-L12-v2|
| Semantic Search     | Cosine Similarity                      |
| Language Model      | `TinyLlama` (custom wrapper/logic)     |
| Data Pipeline       | `numpy`                                |

> ✅ All models are downloaded during the Docker build step for fully offline execution.

---

## 🛠️ How to Build the Solution

```bash
docker build --platform linux/amd64 -t pdf_insight_extractor:abcd1234
````

This builds a container image with all dependencies and models preloaded, ensuring zero network access during execution.

---

## ▶️ How to Run (Expected Execution)

```bash
docker run --rm -v $(pwd)/input:/app/input -v $(pwd)/output:/app/output --network none pdf_insight_extractor:abcd1234
```

> Replace `$(pwd)/input` with the path to your PDF collection directory if different.

---

## 📁 Input/Output Format

* **Input Directory:** `/app/input/`

  * Each subfolder should include:

    * PDFs
    * An `input.json` file containing metadata like persona and job-to-be-done.
* **Output File:** `/app/challenge1b_output.json`

  * Structured summary per PDF with top-ranked chunks and generated answers.

---

## 📎 Example `input.json` Format

```json
{
  "persona": { "role": "UX Researcher" },
  "job_to_be_done": { "task": "Find accessibility issues in user flows" },
  "challenge_info": { "challenge_id": "1B", "team": "SegFault" }
  "
}
```
