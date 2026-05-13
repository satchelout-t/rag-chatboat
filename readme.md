# Academic RAG Assistant

A Retrieval-Augmented Generation (RAG) based academic assistant designed for exam preparation and course-specific learning.

This system allows students to upload lecture notes, assignments, study material, and previous year question papers as PDFs, then interact with the content through a conversational interface powered by Large Language Models (LLMs).

Instead of generating generic answers from internet-scale pretraining alone, the assistant retrieves relevant context directly from the uploaded academic material before generating responses. This ensures that answers remain grounded in the actual course content provided by the user.

Built using LangChain, FAISS, HuggingFace embeddings, Groq-hosted Llama 3, and Streamlit.

---

# Project Motivation

Students preparing for exams often deal with:
- Large volumes of scattered lecture notes
- Multiple PDFs across subjects
- Difficulty locating specific concepts quickly
- Lack of personalized doubt-solving systems

Traditional chatbots fail in academic settings because:
- They hallucinate facts
- They are unaware of course-specific material
- They cannot reference uploaded documents

This project solves that problem using Retrieval-Augmented Generation (RAG).

Instead of relying only on the LLM’s internal knowledge, the system first retrieves the most semantically relevant sections from uploaded documents and uses them as grounding context during answer generation.

The goal was to build a lightweight, locally deployable academic assistant that behaves more like a personalized open-book exam helper rather than a generic chatbot.

---

# What is RAG?

## Retrieval-Augmented Generation

RAG combines two components:

### 1. Retriever
Searches a knowledge base and retrieves the most relevant information related to the user query.

### 2. Generator (LLM)
Uses the retrieved context to generate accurate, grounded answers.

---

# Why RAG Instead of a Normal Chatbot?

A normal LLM:
- Relies only on pretrained knowledge
- Cannot access your notes
- May hallucinate incorrect information

A RAG system:
- Searches uploaded academic material
- Grounds responses in retrieved content
- Produces context-aware answers
- Reduces hallucination significantly

This makes RAG highly effective for:
- Exam preparation
- Doubt solving
- PYQ analysis
- Course-specific revision

---

# System Architecture

```text
                    PDF Documents
        (Notes, PYQs, Assignments, Slides)
                              │
                              ▼
                    Text Extraction Layer
                              │
                              ▼
                     Text Chunking
                 (500 token segments)
                              │
                              ▼
                    Embedding Generation
              (all-MiniLM-L6-v2 Encoder)
                              │
                              ▼
                  FAISS Vector Database
                              │
──────────────────────────────────────────────────

                    User Question
                              │
                              ▼
                   Query Embedding
                              │
                              ▼
               Similarity Search in FAISS
                              │
                              ▼
                Top-K Relevant Chunks
                              │
                              ▼
              Prompt + Retrieved Context
                              │
                              ▼
                  Llama 3 via Groq API
                              │
                              ▼
                    Final Grounded Answer
```

---

# Core Features

## Multi-PDF Upload Support

Users can upload:
- Lecture notes
- Previous year papers
- Assignments
- Tutorials
- Reference material

The system processes all documents into a unified searchable knowledge base.

---

## Context-Grounded Question Answering

The assistant answers questions strictly using retrieved content from uploaded documents.

Example:
```text
"What are the assumptions of Fourier heat conduction?"
```

The system retrieves relevant chunks from thermal engineering notes before generating the answer.

---

## Semantic Search

Unlike keyword matching, semantic retrieval captures meaning.

This allows queries like:
```text
"Explain entropy generation"
```

to retrieve chunks containing:
```text
"Irreversibility in thermodynamic systems..."
```

even if the exact words do not match.

---

## Persistent Vector Database

Embeddings are stored locally using FAISS.

Benefits:
- Faster future queries
- No need to reprocess documents
- Persistent knowledge base across sessions

---

## Exam-Oriented Question Generation

The assistant can generate:
- Practice questions
- Short answer questions
- Conceptual questions
- Viva-style prompts

directly from uploaded course material.

---

# Tech Stack

| Component | Technology |
|---|---|
| LLM Framework | LangChain |
| Vector Database | FAISS |
| Embedding Model | all-MiniLM-L6-v2 |
| LLM Provider | Groq |
| Language Model | Llama 3 |
| Frontend | Streamlit |
| Backend | Python |

---

# Why These Technologies?

## LangChain

Used for orchestrating the complete RAG pipeline:
- Document loading
- Chunking
- Retrieval
- Prompt chaining
- LLM interaction

It simplifies building modular LLM workflows.

---

## FAISS

Facebook AI Similarity Search (FAISS) enables fast vector similarity search over embedded document chunks.

Chosen because:
- Lightweight
- Extremely fast locally
- Efficient for medium-sized academic datasets
- No external database dependency

---

## all-MiniLM-L6-v2 Embeddings

A lightweight sentence-transformer model used to convert text into dense semantic vectors.

Advantages:
- Fast inference
- Good semantic retrieval performance
- Low memory usage
- Suitable for local deployment

---

## Groq + Llama 3

Groq provides extremely fast inference for open-source LLMs.

Llama 3 was selected because:
- Strong reasoning capability
- Good instruction following
- Fast generation latency
- Open-source accessibility

---

# Document Processing Pipeline

## Step 1 — PDF Loading

PDFs are parsed and text is extracted.

Supported sources:
- Lecture notes
- Handwritten scanned PDFs (limited)
- Assignments
- Text-heavy documents

---

## Step 2 — Text Chunking

Documents are split into manageable chunks.

### Configuration

| Parameter | Value |
|---|---|
| Chunk Size | 500 tokens |
| Chunk Overlap | 50 tokens |

### Why Overlap?

Overlap preserves context continuity between neighboring chunks and improves retrieval quality.

---

## Step 3 — Embedding Generation

Each chunk is converted into a high-dimensional vector representation using:
```text
all-MiniLM-L6-v2
```

Semantically similar chunks produce nearby vectors in embedding space.

---

## Step 4 — Vector Storage

Embeddings are stored inside FAISS for efficient nearest-neighbor search.

This enables:
- Fast semantic retrieval
- Persistent memory
- Scalable querying

---

# Query Pipeline

When a user asks a question:

## 1. Query Embedding
The question is embedded into vector space.

---

## 2. Similarity Search
FAISS retrieves the most relevant chunks using cosine similarity.

Typically:
```text
Top 3 relevant chunks
```

are retrieved.

---

## 3. Context Injection
Retrieved chunks are injected into the LLM prompt.

---

## 4. Grounded Generation
Llama 3 generates a response using:
- User query
- Retrieved academic context

This minimizes hallucination and keeps answers course-specific.

---

# Example Workflow

## Uploaded PDFs
- Thermodynamics notes
- Heat transfer slides
- PYQs

---

## User Query
```text
"What is entropy generation?"
```

---

## Retrieved Chunks
Relevant sections discussing:
- Irreversibility
- Second law
- Entropy balance

---

## Final Response
The LLM generates an explanation grounded in the retrieved notes rather than generic internet knowledge.

---

# Challenges Faced

## Hallucination Control

Initially, the model generated answers beyond the uploaded material.

This was reduced by:
- Restricting prompt instructions
- Using retrieved-context grounding
- Limiting generation scope

---

## Chunk Size Optimization

Large chunks:
- Reduced retrieval precision

Small chunks:
- Lost contextual continuity

A balanced configuration of:
```text
500 tokens + 50 overlap
```

provided better retrieval quality.

---

## Retrieval Quality

Pure keyword matching failed for conceptual academic questions.

Semantic embeddings significantly improved retrieval performance for theory-heavy subjects.

---

# Future Improvements

## Hybrid Search

Combine:
- Semantic search
- Keyword search (BM25)

for stronger retrieval accuracy.

---

## OCR Integration

Improve support for:
- Scanned handwritten notes
- Low-quality PDFs

using OCR pipelines like Tesseract.

---

## Conversation Memory

Add long-term conversational memory across sessions for personalized learning.

---

## Source Citations

Display:
- Retrieved chunks
- PDF names
- Page references

alongside generated answers.

---

## Multi-Modal RAG

Extend support to:
- Diagrams
- Figures
- Tables
- Mathematical equations

using vision-language models.

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

# Setup Instructions

## Clone Repository

```bash
git clone https://github.com/your-username/rag-chatbot.git
cd rag-chatbot
```

---

# Create Virtual Environment

## Windows

```bash
python -m venv venv
venv\Scripts\activate
```

## Linux / Mac

```bash
python3 -m venv venv
source venv/bin/activate
```

---

# Install Dependencies

```bash
pip install -r requirements.txt
```

---

# Add Groq API Key

Create a `.env` file:

```bash
GROQ_API_KEY=your_api_key_here
```

---

# Run the Application

```bash
streamlit run app.py
```

---

# Example Use Cases

- Exam preparation assistant
- Course-specific doubt solving
- PYQ analysis
- Research paper querying
- Technical document search
- Personalized study companion

---

# Limitations

- Retrieval quality depends heavily on PDF quality
- Weak OCR support for handwritten notes
- Small embedding models may miss highly nuanced context
- Responses are limited to uploaded material

---

# Learning Outcomes

Through this project, the following concepts were explored deeply:

- Retrieval-Augmented Generation (RAG)
- Vector embeddings
- Semantic search
- Prompt engineering
- LLM orchestration
- Vector databases
- Context grounding
- LangChain pipelines
- Streamlit deployment

---

# References

## Papers & Concepts

- Lewis et al. — Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks
- Sentence Transformers
- FAISS Similarity Search
- LangChain Documentation
- Meta Llama 3

---

# Author

Harpreet Singh

Built as part of an exploration into Generative AI systems, LLM orchestration, semantic retrieval pipelines, and practical educational AI applications.