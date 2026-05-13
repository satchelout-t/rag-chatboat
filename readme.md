# Academic RAG Assistant

A Retrieval-Augmented Generation (RAG) based chatbot designed for exam preparation and course-specific learning. Upload lecture notes, study material, or previous year question papers as PDFs and get context-grounded answers powered by an LLM.

Instead of relying only on pretrained knowledge, the system retrieves relevant content from uploaded academic documents before generating responses, reducing hallucinations and improving accuracy for subject-specific queries.

---

# Features

- Upload multiple PDFs (notes, PYQs, assignments, study material)
- Context-grounded question answering using uploaded documents
- Semantic search using vector embeddings
- Persistent FAISS vector database for faster future queries
- Exam-style practice question generation
- Session-based chat history
- Streamlit-based interactive UI

---

# Architecture

```text
PDF Upload
    │
    ▼
Text Extraction
    │
    ▼
Chunking (500 tokens + overlap)
    │
    ▼
Embedding Generation
(all-MiniLM-L6-v2)
    │
    ▼
FAISS Vector Store
──────────────────────────────

User Query
    │
    ▼
Query Embedding
    │
    ▼
Similarity Search
    │
    ▼
Top Relevant Chunks
    │
    ▼
Llama 3 via Groq API
    │
    ▼
Grounded Answer
```

---

# Tech Stack

| Component | Technology |
|---|---|
| Framework | LangChain |
| Vector Database | FAISS |
| Embeddings | all-MiniLM-L6-v2 |
| LLM | Llama 3 |
| Inference Provider | Groq |
| Frontend | Streamlit |
| Backend | Python |

---

# How It Works

1. PDFs are loaded and split into smaller text chunks
2. Each chunk is converted into embeddings using `all-MiniLM-L6-v2`
3. Embeddings are stored inside FAISS for semantic retrieval
4. User queries are embedded and matched against stored vectors
5. Top relevant chunks are passed to Llama 3 through Groq API
6. The LLM generates answers grounded in retrieved academic context

---

# Why RAG?

Traditional chatbots:
- Cannot access your course material
- Often hallucinate information
- Generate generic answers

RAG-based systems:
- Retrieve relevant notes before answering
- Produce course-specific responses
- Improve factual grounding and reliability

This makes RAG highly effective for:
- Exam preparation
- Doubt solving
- PYQ analysis
- Personalized study assistance

---

# Challenges & Improvements

## Challenges Faced
- Hallucination control
- Chunk size optimization
- Maintaining retrieval accuracy across large PDFs

## Future Improvements
- Hybrid search (semantic + keyword)
- OCR support for scanned notes
- Source citations with page references
- Transformer-based reranking
- Multi-modal document support

---

# Repository Structure

```text
.
├── app.py
├── requirements.txt
├── vector_store/
├── uploaded_pdfs/
├── utils/
├── README.md
└── .env
```

---

# Setup

## Clone Repository

```bash
git clone https://github.com/your-username/rag-chatbot.git
cd rag-chatbot
```

---

## Create Virtual Environment

### Windows
```bash
python -m venv venv
venv\Scripts\activate
```

### Linux / Mac
```bash
python3 -m venv venv
source venv/bin/activate
```

---

## Install Dependencies

```bash
pip install -r requirements.txt
```

---

## Add API Key

Create a `.env` file:

```bash
GROQ_API_KEY=your_api_key_here
```

---

## Run Application

```bash
streamlit run app.py
```

---

# Example Use Cases

- Exam preparation assistant
- Course-specific doubt solving
- Technical document querying
- Study material summarization
- Practice question generation

---

# Learning Outcomes

This project explores:
- Retrieval-Augmented Generation (RAG)
- Semantic search
- Vector databases
- Prompt engineering
- LLM orchestration
- LangChain pipelines
- Streamlit deployment

---

# Author

Harpreet Singh

Built as part of an exploration into Generative AI systems, semantic retrieval pipelines, and practical educational AI applications.