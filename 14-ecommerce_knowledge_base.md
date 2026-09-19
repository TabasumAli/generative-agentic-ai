# 🛍️ E-Commerce Knowledge Base RAG

> **From Raw Business Data to a Grounded E-Commerce AI Assistant**

This project demonstrates how to build a **domain-specific Retrieval-Augmented Generation (RAG) application** for e-commerce operations.

Instead of relying on an LLM's general knowledge, the system retrieves relevant information from a curated knowledge base containing **product catalogs, orders, returns policies, shipping SLAs, vendor onboarding documents, warehouse SOPs, and support transcripts** before generating an answer.

The result is an AI assistant that can answer operational questions while keeping its responses grounded in the provided business data.

---

## 1. 🎯 Problem

E-commerce platforms generate large amounts of structured and unstructured information:

* Product catalogs
* Customer orders
* Inventory records
* Return and refund policies
* Shipping SLAs
* Vendor onboarding requirements
* Warehouse procedures
* Customer-support conversations

Traditional keyword search can make it difficult to find the exact information needed.

For example:

> "What is the return timeline for defective electronics?"

or:

> "Which electronics products are currently out of stock?"

or:

> "What is the SLA for same-day delivery?"

A general-purpose LLM may not have access to the organization's latest internal policies or operational data.

The goal of this project is to build a **knowledge-grounded assistant** that retrieves relevant information first and then uses an LLM to generate the final response.

---

## 2. 💡 Solution

The application uses a **Retrieval-Augmented Generation pipeline**:

```text
User Question
      ↓
Query Processing
      ↓
FAISS Semantic Search
      ↓
Retrieve Top-K Relevant Chunks
      ↓
Build Context
      ↓
Groq LLM
      ↓
Grounded Answer
      ↓
Source Citations
```

The knowledge base combines two types of information:

### Unstructured Data

* PDF policies
* SOPs
* Vendor documentation
* Support transcripts
* Operational manuals

### Structured Data

* Product inventory
* Orders
* Transactions

This allows the application to answer both **policy-oriented** and **data-oriented** questions.

---

## 3. 🧠 Key AI Concepts

This project focuses on several important Generative AI concepts.

### Retrieval-Augmented Generation

RAG separates **knowledge retrieval** from **language generation**.

Instead of asking:

```text
Question → LLM → Answer
```

the system uses:

```text
Question
   ↓
Retrieve relevant knowledge
   ↓
Provide knowledge to LLM
   ↓
Generate grounded answer
```

This reduces the dependency on the model's pretrained knowledge.

### Embeddings

Documents are converted into numerical vectors using:

```text
sentence-transformers/all-MiniLM-L6-v2
```

Questions are embedded using the same representation so that semantically similar content can be retrieved.

### Vector Search

The project uses **FAISS** for similarity search.

Conceptually:

```text
Question Vector
      ↓
Compare with Document Vectors
      ↓
Calculate Similarity
      ↓
Return Top-K Matches
```

### Grounded Generation

The LLM receives the retrieved context and generates its response based on that information.

This makes the application suitable for domain-specific knowledge assistants.

---

## 4. 🏗️ Architecture

The complete system can be viewed as two pipelines.

### Ingestion Pipeline

```text
PDFs + CSVs
     ↓
Document Loading
     ↓
Text / Table Extraction
     ↓
Section-Aware Chunking
     ↓
Metadata Creation
     ↓
HuggingFace Embeddings
     ↓
FAISS Index
```

### Query Pipeline

```text
User Question
     ↓
Embedding
     ↓
FAISS Retrieval
     ↓
Top-K Documents
     ↓
Context Construction
     ↓
Groq LLM
     ↓
Answer + Sources
```

### Technology Stack

| Component       | Technology                        |
| --------------- | --------------------------------- |
| UI              | Streamlit                         |
| RAG Framework   | LangChain                         |
| Vector Database | FAISS                             |
| Embeddings      | HuggingFace Sentence Transformers |
| LLM             | Groq `openai/gpt-oss-120b`        |
| PDF Processing  | pdfplumber                        |
| Structured Data | Pandas                            |
| Language        | Python                            |

---

## 5. 🔨 Building the Application

The application is divided into two major components.

### `ingest.py`

Responsible for creating the knowledge base.

It:

1. Finds PDFs and CSV files.
2. Extracts their contents.
3. Preserves important document structure.
4. Creates semantic chunks.
5. Adds metadata.
6. Generates embeddings.
7. Builds the FAISS index.
8. Saves the index locally.

The generated files are:

```text
faiss_index/
├── index.faiss
└── index.pkl
```

### `app.py`

Responsible for the interactive RAG application.

The basic workflow is:

```text
User Question
      ↓
Load FAISS Index
      ↓
Retrieve Relevant Documents
      ↓
Construct Prompt
      ↓
Send Context + Question to Groq
      ↓
Display Answer
      ↓
Display Sources
```

---

## 6. 🔍 Example Query & Results

Once the application is running, users can ask questions such as:

### Example 1 — Order Query

**Query:**

```text
What is the status of Order #ORD-88219?
```

**System behavior:**

```text
Question
   ↓
Search order records
   ↓
Retrieve matching order document
   ↓
Send retrieved record to LLM
   ↓
Generate grounded response
```

**Result:**

```text
Order ORD-88219 is currently marked as Shipped.
```

The answer is grounded in the corresponding order record rather than generated from general knowledge.

---

### Example 2 — Product Query

**Query:**

```text
Find wireless earbuds under Rs. 5,000.
```

The system searches the product inventory and retrieves matching product records.

The LLM then formats the retrieved information into a natural-language response.

---

### Example 3 — Policy Query

**Query:**

```text
What are the return timelines for defective electronics?
```

The system retrieves the relevant section from the return-policy documentation and uses it as context for generation.

---

### Example 4 — Vendor Query

**Query:**

```text
What documents are required to join DarazMall?
```

The application retrieves the relevant vendor onboarding section and generates an answer based on the retrieved documentation.

---

### Example 5 — Out-of-Scope Question

**Query:**

```text
Who won the World Cup?
```

If the information does not exist in the knowledge base, the assistant should avoid inventing an answer and respond that the information is outside its available knowledge.

This is an important RAG principle:

> **If the knowledge base does not contain the answer, don't fabricate one.**

---

## 7. 🧩 Important Implementation Decisions

### Why FAISS?

FAISS provides efficient local vector similarity search and works well for a project of this size.

It also allows the entire vector store to remain local.

### Why Local Embeddings?

Using:

```text
all-MiniLM-L6-v2
```

means embeddings can be generated locally without requiring a separate embedding API.

Benefits include:

* No embedding API costs
* Simple development setup
* Local processing
* Easy reproducibility

### Why Groq?

Groq provides fast LLM inference, which makes the interactive Streamlit experience responsive.

The project uses:

```text
openai/gpt-oss-120b
```

for answer generation.

### Why Metadata?

Metadata makes retrieved information easier to trace.

For example:

```python
{
    "source": "orders_transactions.csv",
    "order_id": "ORD-88219",
    "status": "Shipped"
}
```

For PDF content, metadata can include:

```python
{
    "source": "returns_shipping_policy.pdf",
    "section": "3.2 RETURN POLICY",
    "page": 7
}
```

This allows the UI to show meaningful source information alongside the answer.

---

## 8. 🚀 What This Teaches

This project moves beyond a basic chatbot and introduces several practical RAG engineering skills.

### You learn how to:

* Build a knowledge base from multiple data formats
* Process both structured and unstructured information
* Extract tables from PDFs
* Create metadata-rich documents
* Perform semantic chunking
* Generate local embeddings
* Build a FAISS vector database
* Retrieve Top-K relevant documents
* Construct grounded LLM prompts
* Use Groq for fast inference
* Display source citations
* Handle out-of-scope questions
* Build a production-style Streamlit interface

### The bigger lesson

A useful enterprise AI application is often not:

```text
LLM + Prompt
```

Instead, it looks more like:

```text
Data
  ↓
Processing
  ↓
Retrieval
  ↓
Context
  ↓
LLM
  ↓
Application
  ↓
User
```

The LLM is only one component of the complete system.

---

## 9. 🌍 Real-World Applications & Next Steps

The same architecture can be adapted to many business domains.

### 🏢 Enterprise Knowledge Assistant

Replace e-commerce documents with:

```text
Company Policies
Employee Handbook
SOPs
Internal Documentation
```

### 🏥 Healthcare Knowledge Assistant

Use:

```text
Hospital SOPs
Patient Guidelines
Clinical Documentation
Department Policies
```

### 🎓 University Knowledge Assistant

Use:

```text
University Policies
Admission Guidelines
Fee Structures
Scholarship Documents
Academic Calendar
```

### ⚖️ Legal Document Assistant

Use:

```text
Contracts
Regulations
Policies
Legal Documents
```

### 🔮 Possible Improvements

The current system can be extended with:

* Hybrid keyword + vector retrieval
* Reranking
* Query rewriting
* Conversational memory
* Metadata filtering
* Streaming responses
* Multi-query retrieval
* Parent-child document retrieval
* Automated knowledge-base updates
* Live web search
* Agentic RAG
* Evaluation datasets
* Retrieval and answer-quality metrics

A natural evolution would be:

```text
Basic RAG
   ↓
Advanced RAG
   ↓
Hybrid RAG
   ↓
Agentic RAG
   ↓
Multi-Agent Knowledge System
```

---

## 🧠 Final Takeaway

This project demonstrates the transition from **"using an LLM"** to **"building an AI application around an LLM."**

The important architecture is:

```text
                 ┌─────────────────┐
                 │  Knowledge Base │
                 └────────┬────────┘
                          ↓
                 ┌─────────────────┐
                 │   Retrieval     │
                 │     FAISS       │
                 └────────┬────────┘
                          ↓
                 ┌─────────────────┐
                 │ Retrieved       │
                 │ Context         │
                 └────────┬────────┘
                          ↓
                 ┌─────────────────┐
                 │    Groq LLM     │
                 └────────┬────────┘
                          ↓
                 ┌─────────────────┐
                 │ Grounded Answer │
                 │   + Sources     │
                 └─────────────────┘
```

**The core idea:**

> **Retrieve the right knowledge first. Generate the answer second.**

That principle is at the heart of practical RAG systems and provides the foundation for building more advanced **enterprise AI and agentic AI applications**.


Github Project Link: https://github.com/TabasumAli/ecommerce_knowledge_base

App Link: https://ecommerceknowledgebase.streamlit.app/