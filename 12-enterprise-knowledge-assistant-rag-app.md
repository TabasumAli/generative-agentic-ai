# Lesson 10 — Enterprise Knowledge Assistant (RAG App)

> **From RAG prototypes to secure enterprise knowledge systems.**

Enterprise AI is not just about connecting an LLM to a prompt. In real organizations, AI systems need to work with internal knowledge while keeping information accurate, private, secure, and governed.

In this lesson, we move from basic RAG toward a practical **Enterprise Knowledge Assistant**.

---

## 1. Enterprise AI

**Enterprise AI** means applying artificial intelligence to an organization's real data, workflows, and business processes.

Instead of relying only on an LLM's pretrained knowledge, an enterprise assistant can work with internal sources such as:

- Company policies
- HR documents
- Technical documentation
- Product manuals
- Legal documents
- Internal procedures
- Reports and guidelines

A simplified enterprise AI workflow:

```text
Employee Question
       ↓
Enterprise Knowledge Base
       ↓
Relevant Information
       ↓
LLM
       ↓
Grounded Answer
```

The important shift is:

**AI should not only generate answers — it should retrieve and use trusted organizational knowledge.**

---

## 2. From RAG to Multi-Document RAG

A basic RAG pipeline usually looks like:

```text
Documents
   ↓
Chunking
   ↓
Embeddings
   ↓
Vector Index
   ↓
Similarity Search
   ↓
Relevant Chunks
   ↓
LLM
   ↓
Answer
```

With **multi-document RAG**, the knowledge base can contain many documents at the same time.

```text
                 ┌── HR Policy
                 ├── Employee Handbook
                 ├── Security Policy
Documents ───────┼── Technical Docs
                 ├── Product Manual
                 └── Company Guidelines
                         ↓
                    Chunking
                         ↓
                    Embeddings
                         ↓
                    FAISS Index
                         ↓
                 Similarity Search
                         ↓
                  Relevant Chunks
                         ↓
                  Context Assembly
                         ↓
                  Grounded Prompt
                         ↓
                       LLM
                         ↓
                   Answer + Sources
```

Instead of asking:

> "What does this PDF say?"

we can ask:

> "According to the company's leave policy and employee handbook, how many annual leave days are available?"

The system can retrieve relevant information across multiple sources.

---

## 3. FAISS Index

**FAISS (Facebook AI Similarity Search)** is a library designed for efficient similarity search over vectors.

When documents are processed, their chunks can be converted into numerical embeddings.

```text
"Employees receive 20 annual leave days."
                    ↓
             Embedding Model
                    ↓
[0.12, -0.31, 0.87, 0.44, ...]
```

These vectors can then be stored inside a FAISS index.

Conceptually:

```text
Document Chunk
      ↓
   Embedding
      ↓
 Numerical Vector
      ↓
   FAISS Index
```

The FAISS index allows the application to efficiently search for vectors that are close to the vector representing a user's question.

---

## 4. FAISS Similarity Search

When a user asks a question, the question is also converted into an embedding.

```text
User Question
      ↓
Embedding Model
      ↓
Query Vector
      ↓
FAISS Similarity Search
      ↓
Top-K Similar Chunks
```

For example:

```text
Question:
"What is the company's annual leave policy?"

Retrieved results:

1. HR Policy — Annual Leave
2. Employee Handbook — Leave Benefits
3. Employee FAQ — Time Off
```

The retrieved chunks become context for the LLM.

### Why Top-K?

Instead of sending every document to the model, RAG retrieves only the most relevant chunks.

```text
1000 document chunks
        ↓
   Similarity Search
        ↓
Top 5 relevant chunks
        ↓
      LLM
```

This reduces irrelevant context and helps the model focus on information related to the question.

---

## 5. Euclidean Distance

One common mathematical method for measuring the distance between two vectors is **Euclidean distance**.

For two vectors:

```text
A = [a₁, a₂, ..., aₙ]
B = [b₁, b₂, ..., bₙ]
```

Euclidean distance is:

```text
d(A, B) = √((a₁-b₁)² + (a₂-b₂)² + ... + (aₙ-bₙ)²)
```

The basic intuition is:

```text
Smaller distance
      ↓
Closer vectors
      ↓
Potentially more similar
```

Visual intuition:

```text
Query Vector
     ●
    / \
   /   \
  ●     ●
Near   Far

Near vector → smaller distance
Far vector  → larger distance
```

In practice, FAISS can use different index types and distance/similarity metrics depending on how the index is configured.

The important concept is:

**RAG converts language into vectors and uses mathematical similarity to retrieve relevant knowledge.**

---

## 6. Metadata

Embeddings tell us about semantic similarity, but enterprise systems also need information **about the document**.

This is where **metadata** becomes important.

Example:

```json
{
  "source": "HR Policy.pdf",
  "department": "HR",
  "document_type": "policy",
  "access_level": "employees",
  "version": "2026",
  "page": 12
}
```

Metadata can help with:

- Filtering results
- Identifying sources
- Tracking document versions
- Applying access rules
- Showing citations
- Organizing knowledge
- Auditing retrieval

A stronger retrieval pipeline:

```text
User Query
    ↓
Query Embedding
    ↓
FAISS Search
    ↓
Metadata Filtering
    ↓
Relevant Chunks
    ↓
Grounded Answer
```

This becomes especially useful when an organization has thousands of documents.

---

## 7. Privacy, Security & Governance

Enterprise RAG introduces an important question:

> **Should the AI be allowed to access every document?**

Not necessarily.

A secure enterprise assistant should respect the organization's access policies.

### Privacy

Enterprise knowledge bases may contain sensitive information such as:

- Personal information
- Employee records
- Financial information
- Customer data
- Confidential business documents

### Security

The system should protect information throughout the pipeline:

```text
Documents
   ↓
Access Control
   ↓
Retrieval Layer
   ↓
LLM
   ↓
Authorized Answer
```

A user should not receive information simply because it exists in the knowledge base.

### Governance

Organizations also need to know:

- Where did the answer come from?
- Which document version was used?
- Who can access the document?
- When was the document updated?
- Can the retrieval process be audited?
- What happens when a document is removed?

A useful principle is:

> **Retrieve only what the user is authorized to access.**

This makes access control part of the RAG architecture rather than an afterthought.

---

## 8. Enterprise Knowledge Assistant — Complete Pipeline

Putting everything together:

```text
                 ENTERPRISE DOCUMENTS
                          ↓
                Document Processing
                          ↓
                     Chunking
                          ↓
                    Embeddings
                          ↓
              ┌─────────────────────┐
              │      FAISS Index    │
              │                     │
              │ Vector + Metadata   │
              └─────────────────────┘
                          ↑
                          │
                    User Question
                          ↓
                    Query Embedding
                          ↓
                  Similarity Search
                          ↓
                  Metadata / Access
                       Filtering
                          ↓
                 Top-K Relevant Chunks
                          ↓
                  Context Engineering
                          ↓
                   Grounded Prompt
                          ↓
                         LLM
                          ↓
                  Answer + Sources
```

The complete enterprise system combines:

**RAG + embeddings + vector search + metadata + access control + grounded generation.**

That is the foundation of an enterprise knowledge assistant.

---

## 9. Real Example — Input → Output

Imagine a company has uploaded:

```text
HR_Policy.pdf
Employee_Handbook.pdf
Security_Policy.pdf
Remote_Work_Guidelines.pdf
```

### Input

> "Can I work remotely for three days per week, and what approval do I need?"

### Retrieval

The system converts the question into an embedding and searches the FAISS index.

It retrieves relevant chunks from:

```text
Remote_Work_Guidelines.pdf
Employee_Handbook.pdf
HR_Policy.pdf
```

Metadata and access rules are checked before the context is passed to the model.

### Grounded Context

```text
Remote Work Guidelines:
Eligible employees may work remotely up to
three days per week with manager approval.

Employee Handbook:
Remote work requests must follow the
department's approval process.
```

### Output

> According to the Remote Work Guidelines, eligible employees may work remotely up to three days per week, subject to manager approval. The Employee Handbook also requires the department's approval process to be followed.
>
> **Sources:** Remote Work Guidelines, Employee Handbook

The key difference from a generic chatbot is that the answer is grounded in the organization's retrieved knowledge.

---

# Key Takeaways

- **Enterprise AI** applies AI to organizational data and workflows.
- **RAG** connects LLMs with external knowledge.
- **Multi-document RAG** retrieves information across multiple documents.
- **FAISS** provides efficient vector similarity search.
- **Embeddings** represent semantic meaning as numerical vectors.
- **Euclidean distance** is one mathematical method for measuring vector distance.
- **Metadata** adds document-level information and enables filtering and traceability.
- **Privacy and security** help control what information can be retrieved.
- **Governance** helps organizations track, audit, and manage AI knowledge systems.
- A strong enterprise assistant combines **retrieval, access control, context engineering, and grounded generation**.

> **RAG gives an AI system access to knowledge. Enterprise RAG adds the controls needed to use that knowledge responsibly.**

Github Link: https://github.com/TabasumAli/hospital_knowledge_base
