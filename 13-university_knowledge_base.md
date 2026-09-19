# 🎓 University Knowledge Base Assistant

> Building a university-specific RAG assistant that can answer questions from policies, fees, scholarships, examinations, admissions, and academic documents.

---

## 1. Introduction

University information is usually scattered across multiple PDF documents.

A student might need to search through:

* Student handbooks
* Fee structures
* Scholarship policies
* Examination rules
* Admission guidelines
* Academic calendars

The problem is not that the information doesn't exist.

The problem is **finding the right information quickly**.

This project solves that problem using **Retrieval-Augmented Generation (RAG)**.

Instead of asking an LLM to answer from its general knowledge, we first retrieve relevant information from a curated university knowledge base and then provide that context to the LLM.

The result is a chatbot that answers questions using the university's own documents.

### Tech Stack

```text
Python
Streamlit
LangChain
FAISS
Sentence Transformers
pdfplumber
Groq
openai/gpt-oss-120b
```

---

# 2. The Problem

Imagine a student asks:

> "What is the minimum GPA required to avoid academic probation?"

A normal LLM might know what academic probation generally means, but it doesn't know the specific policy of a particular university.

That's where RAG becomes useful.

Instead of:

```text
Question
   ↓
LLM
   ↓
Answer
```

we build:

```text
Question
   ↓
Search University Documents
   ↓
Retrieve Relevant Information
   ↓
Give Context to LLM
   ↓
Grounded Answer
```

The LLM becomes the **reasoning and language layer**, while the university documents become the **source of truth**.

---

# 3. What We Built

The application uses six university PDFs as its knowledge base:

```text
knowledge_base/
│
├── Student handbook.pdf
├── Scholarship policy.pdf
├── Fee policy.pdf
├── Examination rules.pdf
├── Admission guidelines.pdf
└── Academic calender.pdf
```

The documents are processed once and converted into a searchable FAISS vector index.

When a student asks a question, the application:

1. Converts the question into an embedding.
2. Searches the FAISS index.
3. Retrieves the most relevant chunks.
4. Sends those chunks to the Groq LLM.
5. Generates an answer based on the retrieved context.
6. Displays the source document, section, and page information.

---

# 4. RAG Architecture

The complete workflow looks like this:

```text
                DOCUMENT INGESTION
                       │
                       ▼
             ┌──────────────────┐
             │   University     │
             │      PDFs        │
             └────────┬─────────┘
                      │
                      ▼
             ┌──────────────────┐
             │   pdfplumber     │
             │ Text + Tables    │
             └────────┬─────────┘
                      │
                      ▼
             ┌──────────────────┐
             │ Section-Aware    │
             │    Chunking      │
             └────────┬─────────┘
                      │
                      ▼
             ┌──────────────────┐
             │ Sentence         │
             │ Transformers     │
             └────────┬─────────┘
                      │
                      ▼
             ┌──────────────────┐
             │      FAISS       │
             │  Vector Store    │
             └──────────────────┘


                  USER QUERY
                      │
                      ▼
             ┌──────────────────┐
             │ Semantic Search  │
             │   Top-K Chunks   │
             └────────┬─────────┘
                      │
                      ▼
             ┌──────────────────┐
             │ Groq             │
             │ gpt-oss-120b     │
             └────────┬─────────┘
                      │
                      ▼
             ┌──────────────────┐
             │ Grounded Answer  │
             │ + Sources        │
             └──────────────────┘
```

The important idea is:

> **Retrieve first, generate second.**

---

# 5. Why Section-Aware Chunking?

One of the important decisions in this project was how to split the PDFs.

A basic RAG implementation might simply split every document after a fixed number of characters.

For example:

```text
Every 1000 characters → new chunk
```

This can break important policy information.

Consider:

```text
4.2 Hostel and Accommodation

Students residing in university accommodation
must follow the following rules...
```

A blind splitter might separate:

```text
Chunk 1:
4.2 Hostel and Accommodation
Students residing in university...
```

from:

```text
Chunk 2:
...must follow the following rules...
```

Now retrieval may return incomplete information.

Instead, this project tries to preserve the document's own structure:

```text
4.2 Hostel and Accommodation
        │
        ├── Policy text
        ├── Conditions
        ├── Exceptions
        └── Related table
```

This makes the retrieved context more meaningful.

---

# 6. Tables Matter Too

University documents aren't just paragraphs.

Important information often lives inside tables:

```text
| Program | Tuition Fee |
|---------|-------------|
| BSCS    | Rs. XXXXX   |
| BSIT    | Rs. XXXXX   |
```

or:

```text
| Grade | Marks |
|-------|-------|
| A     | 85+   |
| B     | 75-84 |
| C     | 65-74 |
```

Using `pdfplumber`, the ingestion pipeline extracts both normal text and tables.

The goal is to turn something like:

```text
PDF → text + tables
```

into searchable knowledge:

```text
University Knowledge
        ↓
Chunks
        ↓
Embeddings
        ↓
FAISS
```

This becomes particularly useful for questions involving **fees, grades, deadlines, and academic schedules**.

---

# 7. Local Embeddings + FAISS

For embeddings, the project uses:

```text
sentence-transformers/all-MiniLM-L6-v2
```

The model converts text into numerical vectors.

For example:

```text
"What is the attendance requirement?"
                 ↓
        [0.12, -0.31, 0.87, ...]
```

The same process happens for the user's question.

FAISS then searches for vectors that are semantically similar.

```text
User Question
      ↓
Embedding
      ↓
FAISS Similarity Search
      ↓
Top 4 Relevant Chunks
```

An important advantage here is that the embedding model runs locally.

The application does **not** need an embedding API key.

---

# 8. Where Generative AI Comes In

The retrieval system finds the relevant information.

But retrieved text alone isn't a good user experience.

For example, retrieval might return:

```text
Section 5.3:
Students must maintain a minimum attendance
percentage of 75%...
```

The LLM turns that into a natural response:

> Students are required to maintain at least 75% attendance according to the university's examination policy.

This is where the Groq model is used:

```text
openai/gpt-oss-120b
```

The basic generation flow is:

```text
User Question
      +
Retrieved Context
      ↓
     LLM
      ↓
Natural Language Answer
```

The prompt instructs the model to stay grounded in the retrieved university information rather than inventing facts.

---

# 9. Example Query and Result

### Query

```text
What is the minimum GPA required to avoid academic probation?
```

### Retrieval

The application searches the FAISS index and retrieves relevant sections from the academic/student policy documents.

Conceptually:

```text
Retrieved Chunk 1
→ Academic standing policy

Retrieved Chunk 2
→ GPA requirements

Retrieved Chunk 3
→ Probation conditions

Retrieved Chunk 4
→ Related academic regulations
```

### Generated Result

```text
According to the university's academic policy,
students must maintain the specified minimum GPA
to remain in good academic standing.

Students falling below the required threshold may
be placed on academic probation.

Source:
Student Handbook
Section: Academic Standing
Page: XX
```

The important part is not simply the answer.

The important part is that the answer is **connected back to the source document**.

---

# 10. Multi-Document Questions

RAG becomes more interesting when the answer requires information from different documents.

For example:

```text
I missed my midterm because I was sick.
What happens, and is there any fee involved?
```

This could require information from:

```text
Examination Rules
        +
Fee Policy
        ↓
   Combined Context
        ↓
       LLM
        ↓
   Final Answer
```

This is one of the practical advantages of a knowledge-base assistant.

The user doesn't need to know which PDF contains the answer.

They simply ask the question.

---

# 11. Handling Unknown Questions

A good RAG application should not try to answer everything.

Suppose someone asks:

```text
What is the Wi-Fi password?
```

If the knowledge base doesn't contain the answer, the system should not invent one.

Similarly:

```text
Who won the World Cup?
```

is unrelated to the university knowledge base.

A grounded assistant should respond along the lines of:

```text
I couldn't find this information in the
available university documents.
```

This is an important RAG principle:

> **Not finding information is better than hallucinating information.**

---

# 12. Common Mistakes

### ❌ Using the LLM as the database

Don't expect the LLM to know university-specific policies.

Use retrieval to provide the relevant information.

---

### ❌ Blindly splitting documents

Fixed-size chunks can separate headings, clauses, and tables.

Whenever possible, preserve document structure.

---

### ❌ Ignoring tables

Important information can exist entirely inside tables.

Fees and academic schedules are common examples.

---

### ❌ Returning huge amounts of context

More context doesn't automatically mean better answers.

Retrieve relevant chunks instead of sending the entire PDF to the LLM.

---

### ❌ Allowing unsupported answers

The model should be instructed to stay within the retrieved context.

Otherwise, the application becomes another generic chatbot.

---

# 13. Best Practices

### 1. Keep documents clean

Better source documents generally produce better retrieval.

### 2. Preserve metadata

Store information such as:

```text
document
section
page
```

alongside every chunk.

### 3. Tune `k`

The number of retrieved chunks affects answer quality.

For example:

```python
search_kwargs={"k": 4}
```

can be adjusted based on the size and complexity of the knowledge base.

### 4. Keep the LLM grounded

The generation prompt should clearly establish that the retrieved documents are the authoritative context.

### 5. Show sources

A user should be able to understand where an answer came from.

---

# 14. Project Structure

```text
university_knowledge_base/
│
├── knowledge_base/
│   ├── Student handbook.pdf
│   ├── Scholarship policy.pdf
│   ├── Fee policy.pdf
│   ├── Examination rules.pdf
│   ├── Admission guidelines.pdf
│   └── Academic calender.pdf
│
├── faiss_index/
│   ├── index.faiss
│   └── index.pkl
│
├── .streamlit/
│   └── config.toml
│
├── app.py
├── ingest.py
├── requirements.txt
└── README.md
```

The two most important files are:

### `ingest.py`

Responsible for:

```text
PDF
 ↓
Extraction
 ↓
Chunking
 ↓
Embedding
 ↓
FAISS
```

### `app.py`

Responsible for:

```text
Question
 ↓
Retrieval
 ↓
Context
 ↓
LLM
 ↓
Answer
```

---

# 15. GenAI Connection

This project demonstrates several important Generative AI concepts together:

| Concept             | Implementation             |
| ------------------- | -------------------------- |
| LLM                 | Groq `openai/gpt-oss-120b` |
| Embeddings          | Sentence Transformers      |
| Vector Search       | FAISS                      |
| RAG                 | Retrieval + Generation     |
| Prompt Engineering  | Grounded generation prompt |
| Document Processing | `pdfplumber`               |
| Metadata            | Document / section / page  |
| UI                  | Streamlit                  |

This is more than simply calling an LLM API.

The application combines:

```text
Data
+
Retrieval
+
Embeddings
+
Vector Search
+
Prompting
+
LLM
+
User Interface
```

into one complete AI application.

---

# 16. What This Project Teaches

The biggest lesson from this project is that building a useful GenAI application isn't only about choosing a powerful model.

The quality of the final system depends on the entire pipeline:

```text
          ┌───────────────┐
          │ Source Data   │
          └───────┬───────┘
                  ↓
          ┌───────────────┐
          │ Processing    │
          └───────┬───────┘
                  ↓
          ┌───────────────┐
          │ Retrieval     │
          └───────┬───────┘
                  ↓
          ┌───────────────┐
          │ Generation    │
          └───────┬───────┘
                  ↓
          ┌───────────────┐
          │ User Experience│
          └───────────────┘
```

A great LLM cannot compensate for poor retrieval.

Likewise, excellent retrieval is not enough if the generation step is poorly grounded.

---

# 17. Quick Revision

```text
RAG = Retrieve + Generate

PDFs
 ↓
Extract text/tables
 ↓
Create meaningful chunks
 ↓
Generate embeddings
 ↓
Store in FAISS
 ↓
User asks question
 ↓
Retrieve relevant chunks
 ↓
Send context + question to LLM
 ↓
Generate grounded response
 ↓
Show sources
```

### Key Technologies

```text
pdfplumber
    → PDF extraction

Sentence Transformers
    → Embeddings

FAISS
    → Vector search

LangChain
    → RAG orchestration

Groq
    → LLM inference

Streamlit
    → User interface
```

---

# 18. What's Next?

This project provides a solid foundation for more advanced RAG systems.

The next step could be moving from a basic vector-search RAG pipeline toward:

```text
Basic RAG
   ↓
Hybrid Search
   ↓
Reranking
   ↓
Corrective RAG
   ↓
Agentic RAG
   ↓
Multi-Agent Knowledge Assistant
```

For example, an advanced university assistant could eventually:

* Search university websites in real time.
* Detect whether retrieved information is sufficient.
* Automatically perform another search when retrieval fails.
* Compare multiple policies.
* Extract deadlines and create reminders.
* Build personalized scholarship recommendations.
* Use agents for different university departments.

That is where RAG starts moving from a simple **document chatbot** toward a real **AI knowledge system**.


Github link: https://github.com/TabasumAli/university_knowledge_base
App Link: https://university-knowledge-base.streamlit.app/