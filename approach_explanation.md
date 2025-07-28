# 🚀 Approach: Modular, High-Performance RAG Pipeline

To ensure scalability, speed, and semantic relevance, we adopted a modular Retrieval-Augmented Generation (RAG) pipeline optimized for document-heavy workloads. Each stage is independently testable and tuned for throughput and precision. The pipeline consists of five major components:

---

### 📄 1. PDF Parsing & Chunking

We begin by processing all provided PDFs using **PyMuPDF (`fitz`)**, a lightweight and performant PDF parser known for its accuracy in preserving layout and handling embedded fonts. Once parsed, the textual content is chunked using a **recursive splitting strategy**. This ensures that the semantic boundaries of the content are respected while maintaining a consistent chunk size optimized for downstream embeddings.

We employ overlapping chunks (e.g., 1000 tokens with 200-token overlap) to retain contextual continuity, which is crucial for downstream models to infer meaning, especially when content spans across pages or sections. Metadata, such as chunk IDs and document source, are retained for traceability and auditability.

---

### 🧠 2. Embedding Chunks

Each chunk of text is embedded into vector space using **`all-MiniLM-L12-v2`** from the `sentence-transformers` library. This model strikes an optimal balance between speed, memory footprint (\~80MB), and embedding quality, making it suitable for both local development and scaled deployments.

The embeddings are computed in batches to reduce compute overhead, and stored for fast retrieval during semantic querying. These vectors represent the high-dimensional semantic meaning of each chunk, enabling intelligent matching beyond simple keyword overlap.

---

### 🔍 3. Semantic Search

At query time, user-specified tasks or prompts are converted into query embeddings using the same embedding model. We compute **cosine similarity** between these query vectors and the precomputed chunk embeddings to identify the top-k (typically top 5) most relevant chunks from across all PDFs.

This ensures that only semantically aligned content is passed downstream, reducing LLM load and improving output accuracy.

---

### 🧬 4. LLM-Based Refined Text

The selected top-k chunks are concatenated and passed into a locally hosted **TinyLlama** model, fine-tuned or prompted for context-aware generation. TinyLlama offers a lean yet powerful alternative to heavier LLMs, making local inference feasible on consumer-grade GPUs or edge devices like NVIDIA Jetson.

The model generates coherent, contextually enriched summaries or refinements tailored to the input task.

---

### 📦 5. Output Formatting

All results — including metadata, selected chunks, and LLM outputs — are structured and written to `challenge1b_output.json`. The output schema ensures downstream readability, integrates with evaluation scripts, and provides traceable mappings from input source to generated text.
