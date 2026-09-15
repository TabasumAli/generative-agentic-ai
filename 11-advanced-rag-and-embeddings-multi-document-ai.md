# Lesson 9 — Advanced RAG & Embeddings: Multi-Document AI

> **Move from answering questions from one document to building a knowledge assistant that can understand information across many sources.**

## 1. What Is RAG?

**Retrieval-Augmented Generation (RAG)** combines information retrieval with a language model.

Instead of relying only on its training data, an LLM first receives relevant information from an external knowledge base.

```text
User Question
      ↓
Retrieve Relevant Information
      ↓
Add Context to Prompt
      ↓
LLM Generates Answer
      ↓
Grounded Response
```

RAG is useful for private, updated, and domain-specific information.

## 2. Basic RAG vs Advanced RAG

A basic RAG pipeline usually looks like this:

```text
Documents
    ↓
Chunking
    ↓
Embeddings
    ↓
Vector Database
    ↓
Similarity Search
    ↓
LLM Response
```

Advanced RAG improves this process with:

- Query rewriting
- Hybrid search
- Metadata filtering
- Reranking
- Context optimization
- Answer validation
- Source citations

```text
User Query
    ↓
Query Understanding
    ↓
Query Rewriting
    ↓
Hybrid Retrieval
    ↓
Filtering + Reranking
    ↓
Context Engineering
    ↓
Grounded Prompt
    ↓
LLM Answer
    ↓
Validation
```

The goal is to retrieve the **right information**, not simply more information.

## 3. Understanding Embeddings

An **embedding** is a numerical representation of text, images, or other data.

Texts with similar meanings usually have similar vector representations.

```text
Text
  ↓
Embedding Model
  ↓
Vector
  ↓
Similarity Search
```

For example:

```text
"How can I apply for a scholarship?"
```

and:

```text
"What are the requirements for scholarship applications?"
```

may have similar embeddings because they have related meanings.

Embeddings allow systems to search by **semantic meaning**, not only by exact keywords.

## 4. Multi-Document RAG Pipeline

A multi-document assistant can work with PDFs, web pages, reports, manuals, and other sources.

### Ingestion Pipeline

```text
Multiple Documents
        ↓
Load Documents
        ↓
Clean Text
        ↓
Split into Chunks
        ↓
Add Metadata
        ↓
Create Embeddings
        ↓
Store in Vector Database
```

Useful metadata includes:

- Document name
- Source URL
- Page number
- Section heading
- Publication date
- Topic
- Document type

### Query Pipeline

```text
User Question
      ↓
Understand Query
      ↓
Retrieve from Multiple Sources
      ↓
Rank Relevant Chunks
      ↓
Build Context
      ↓
Generate Answer
```

## 5. Context Engineering

**Context engineering** means deciding what information should be placed in the LLM's context.

Good context should be:

- Relevant
- Clear
- Well-organized
- Free from unnecessary repetition
- Within the model's context limit
- Connected to reliable sources

Example:

```text
[Source 1]
Document: Scholarship Guidelines
Page: 4
Content: ...

[Source 2]
Document: Eligibility Rules
Page: 2
Content: ...
```

Useful techniques include:

- Removing duplicate chunks
- Grouping content by source
- Preserving page numbers and titles
- Prioritizing high-quality passages
- Limiting irrelevant information
- Keeping instructions separate from retrieved text

> **More context does not always mean better answers. Better context produces better answers.**

## 6. Grounded Prompts for a Knowledge Assistant

A grounded prompt tells the LLM to answer using the retrieved context instead of guessing.

```text
You are a knowledge assistant.

Answer the question using only the provided context.

Rules:
1. Do not invent facts.
2. If the answer is missing, say:
   "I could not find this information in the
   provided documents."
3. Explain the answer clearly.
4. Mention the source when possible.
5. Be honest about uncertainty.

Retrieved Context:
{context}

User Question:
{question}
```

Prompts help reduce hallucinations, but retrieval quality and answer validation are also important.

## 7. Improving RAG Accuracy

### During Ingestion

- Clean documents
- Remove repeated headers and footers
- Use meaningful chunk sizes
- Preserve section boundaries
- Store useful metadata

### During Retrieval

- Rewrite unclear queries
- Combine keyword and vector search
- Apply metadata filters
- Retrieve multiple candidates
- Use a reranker

### During Generation

- Use grounded prompts
- Keep context focused
- Ask for source citations
- Instruct the model to admit uncertainty
- Validate the final answer

| Technique | Purpose |
|---|---|
| Semantic Search | Finds meaning-related content |
| Keyword Search | Finds exact terms |
| Hybrid Search | Combines both approaches |
| Reranking | Reorders results by relevance |
| Query Rewriting | Improves unclear questions |
| Metadata Filtering | Limits results to relevant sources |

## 8. Evaluating a RAG System

A RAG system should be tested with real questions.

Ask:

- Was the correct information retrieved?
- Was the context relevant?
- Did the answer follow the documents?
- Did the model invent facts?
- Were sources identified?
- Did the system handle missing information correctly?

```text
Test Questions
      ↓
Retrieve Context
      ↓
Generate Answers
      ↓
Compare with Trusted Answers
      ↓
Measure Quality
      ↓
Improve the Pipeline
```

Important evaluation areas include:

- Retrieval quality
- Answer correctness
- Faithfulness
- Context relevance
- Completeness
- Latency
- Cost

## 9. Building a Multi-Document Knowledge Assistant

A practical architecture can look like this:

```text
User
  ↓
Chat Interface
  ↓
Query Handler
  ↓
Query Rewrite / Analysis
  ↓
Retriever + Reranker
  ↓
Context Builder
  ↓
Grounded LLM Prompt
  ↓
AI Response + Sources
```

Possible technologies include:

- **Python** — Application logic
- **LangChain or LangGraph** — Workflow orchestration
- **Embedding model** — Vector creation
- **Vector database** — Similarity search
- **BM25 or keyword search** — Exact-term retrieval
- **Reranker** — Relevance improvement
- **LLM API** — Answer generation
- **Streamlit** — User interface

### Real Example — Input → Output

**Input:**

```text
What are the eligibility requirements for this scholarship,
and what is the application deadline?
```

**Retrieved Context:**

```text
Source 1:
Applicants must hold a bachelor's degree.

Source 2:
The application deadline is 15 December.

Source 3:
Applicants must provide proof of English proficiency.
```

**Output:**

```text
Eligibility requirements:
- A bachelor's degree
- Proof of English proficiency

Application deadline:
- 15 December

Sources:
- Scholarship Eligibility Document
- Official Application Guidelines
```

If the documents do not contain an answer, the assistant should clearly say that the information was not found.

## Key Takeaways

1. RAG connects LLMs with external knowledge.
2. Advanced RAG improves retrieval and answer quality.
3. Embeddings enable semantic search.
4. Multi-document RAG needs strong ingestion and metadata handling.
5. Context engineering organizes the most useful information.
6. Grounded prompts help reduce unsupported answers.
7. Hybrid search, reranking, and query rewriting improve retrieval.
8. RAG systems should be evaluated with real test questions.
9. A reliable assistant should provide evidence-based answers with clear sources.

> **A powerful RAG system is not the one that retrieves the most text. It is the one that retrieves the right evidence and uses it correctly.**
