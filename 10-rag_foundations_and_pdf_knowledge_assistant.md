# RAG Foundations & PDF Knowledge Assistant

> A beginner-friendly guide to Retrieval-Augmented Generation — what it is, why it exists, how it works, its architectures, and how we'll use it to build a PDF Knowledge Assistant.

---

## 1. Introduction — What is RAG and Why Does It Matter?

**RAG (Retrieval-Augmented Generation)** is a technique where, instead of relying only on what the LLM memorized during training, we **fetch relevant information from an external knowledge source (your PDFs, a database, the web) and hand it to the model at question time** — so it answers based on *your* data.

**Why it matters — the problems RAG solves:**

| LLM Problem | How RAG Solves It |
|---|---|
| **Hallucination** (makes up facts) | Answers are grounded in retrieved, real text |
| **Stale knowledge** (frozen at training time) | Fresh data is retrieved at query time |
| **No access to private data** (your docs, your PDFs) | Any document can be indexed and retrieved |
| **Can't cite sources** | Retrieved chunks can be referenced |
| **Expensive to retrain** | No retraining — just add new documents to the index |

> 💡 **Analogy:** An LLM is like a smart student who memorized the internet. RAG is letting that student *open the textbook during the exam* — same reasoning ability, but now with access to the right pages.

---

## 2. The Core Idea — RAG vs. Alternatives + Simple Workflow

RAG is one of three ways to make an LLM "know" your data. The other two: **Fine-Tuning** and **Long Context**.

| Criteria | RAG | Fine-Tuning | Long Context |
|---|---|---|---|
| **How knowledge is added** | Retrieved at query time | Baked into model weights | Pasted into the prompt |
| **Fresh data** | ✅ Instant — just add docs | ❌ Needs retraining | ✅ If it fits |
| **Cost** | Low | High (GPU training) | High (long prompts = expensive tokens) |
| **Hallucination control** | ✅ Grounded in sources | ❌ Can still hallucinate | ⚠️ Model may ignore middle content |
| **Citations** | ✅ Natural | ❌ Hard | ❌ Hard |
| **Best for** | Facts that change, private docs, Q&A | Style, tone, task behavior | Small docs, quick prototypes |

> 🎯 **Rule of thumb:** Want to give the model new **facts**? → RAG. Want to change its **style/behavior**? → Fine-Tuning. (They often work *together*: fine-tune for behavior, RAG for facts.)

**Simple RAG workflow (two phases):**

```
PHASE A — Ingestion (offline, run when docs change):
  Documents → Load → Chunk → Embed → Store in Vector DB

PHASE B — Query Time (every user question):
  Question → Embed → Similarity Search → Top-K Chunks
        → Augmented Prompt → LLM → Answer (with citations)
```

---

## 3. Anatomy of a RAG System — Key Components

A RAG pipeline is built from these building blocks. Each one is a decision point that affects answer quality.

| Component | What It Does | Example |
|---|---|---|
| **Document / Corpus** | Your source data collection | 500 PDFs of company reports |
| **Chunking** | Splitting documents into smaller pieces (usually with overlap so context isn't lost at boundaries) | 100-page PDF → 500 chunks of ~500 words, 50-word overlap |
| **Embedding** | Converting text into a numeric vector that captures *meaning* | "cat" and "kitten" get similar vectors |
| **Embedding Model** | The model that creates vectors | `text-embedding-3-small`, `all-MiniLM-L6-v2` |
| **Vector Database** | Stores embeddings + enables fast similarity search | FAISS, ChromaDB, Pinecone, Qdrant |
| **Retrieval** | Finding the most relevant chunks for a query | Top-5 chunks for a question |
| **Similarity Search** | Comparing vectors via distance (cosine, dot product) | "refund policy" matches "returns & refunds" |
| **Prompt Augmentation** | Inserting retrieved chunks into the LLM prompt | `Context: [chunks] Question: [query]` |
| **Generation** | LLM produces the final answer from the context | GPT/Mistral writes the answer |
| **Reranking** (optional) | Re-scoring retrieved chunks for better precision | Boost best 5 of top 20 |
| **Hallucination** | When the model invents facts | What RAG aims to reduce |

---

## 4. Before → After — One Strong Example

**🚫 BEFORE (no RAG):**

> User: *"What is our company's refund policy?"*
> LLM: *"Most companies offer a 30-day refund policy..."* ❌ — a generic, hallucinated guess.

**✅ AFTER (with RAG):**

The same question now triggers retrieval from the company's actual policy PDF:

> User: *"What is our company's refund policy?"*
> System retrieves: *"Refunds are available within 14 days of purchase for unused items..."* (from `refund_policy.pdf`, page 2)
> LLM: *"According to the refund policy, refunds are available within 14 days of purchase for unused items. Digital products are non-refundable."* ✅ — grounded, correct, and citable.

**What changed?** The model stopped guessing from stale training memory and started answering from *your* documents. No retraining — the policy PDF was simply added to the vector database.

---

## 5. The RAG Process — Build → Ask → Retrieve → Generate → Evaluate

The development loop in practice:

1. **Build (Ingest)** — Load documents, chunk them, embed, store in the vector DB.
2. **Ask** — Embed the user's question into a vector.
3. **Retrieve** — Run similarity search, get top-K chunks.
4. **Generate** — Stuff chunks into a prompt, call the LLM, get the answer.
5. **Evaluate** — Inspect answers and ask:
   - Wrong answer? → chunking too big/small, or retrieval missed
   - Right answer but too verbose? → refine the generation prompt
   - Hallucination? → strengthen the "only use context" instruction or add fallback
6. **Improve** — Tune chunk size, K, embeddings, or prompt. One change at a time.

> ⚠️ **Golden rule:** Diagnose before you tune. If retrieval grabs irrelevant chunks, no prompt fix will save the answer — fix retrieval first.

**Diagnosis cheat-sheet:**

| Symptom | Likely cause |
|---|---|
| Answers are generic/off-topic | Retrieval missed — try smaller chunks, more K, better embeddings |
| Answers miss details that exist in the docs | Chunking too large — split finer |
| Hallucinated facts | Prompt lacks "only use context" rule + fallback |
| Right facts, wrong tone/format | Refine the generation prompt |

---

## 6. Real-World Application — Where RAG Appears in Actual GenAI Applications

RAG is the backbone of most production GenAI products you see today:

- **Chatbots & support assistants** — answer from company knowledge bases, wikis, and ticket history instead of hallucinating.
- **Document Q&A tools** — "chat with your PDF" products (this is exactly our project).
- **Enterprise search** — employees query internal docs, policies, and reports conversationally.
- **Code assistants** — retrieve relevant code/docs/examples before answering.
- **Legal & medical research tools** — ground answers in case law, regulations, or research papers with citations.

> 🔁 **Industry pattern:** production RAG systems are continuously refined using real query logs — every bad answer is a diagnosis opportunity (Section 5's loop).

---

## 7. Types of RAG — Standard, Corrective, Speculative & Agentic

These four architectures form an evolution — each fixes a weakness of the previous.

### 7.1 Standard (Naive) RAG

The baseline pipeline: `Query → Embed → Retrieve Top-K → Generate`.

- ✅ Fast, simple, cheap — perfect for MVPs (this is what our PDF project starts with).
- ❌ **Silent failure:** if retrieval grabs irrelevant chunks, the LLM hallucinates confidently with no detection.

### 7.2 Corrective RAG (CRAG)

Adds a **retrieval evaluator** between retrieval and generation. Each chunk is graded:

| Grade | Action taken |
|---|---|
| **Relevant** | Proceed to generation (with optional "knowledge refinement") |
| **Ambiguous** | Decompose the query into sub-queries and re-retrieve |
| **Irrelevant** | Fallback — web search or rewrite query and re-retrieve |

- ✅ Fixes standard RAG's silent failure — bad retrieval is *detected and corrected*.
- ❌ Extra grading step adds latency and complexity.

### 7.3 Speculative RAG

Introduced by Google DeepMind (2024). Uses **two models with different jobs**: a **small fast "drafter"** generates a speculative answer, and a **large "verifier"** validates/corrects it against retrieved documents — the big model only verifies, never generates from scratch.

- ✅ ~2x faster responses; big-model usage drops sharply.
- ❌ Needs two models; verifier quality depends on draft quality.

### 7.4 Agentic RAG — The Connection to Agentic AI

In Agentic RAG, the LLM becomes an **agent in control** — there is no fixed pipeline. The agent *plans, acts, observes, and reflects*:

```
User Query → Agent decides: retrieve? which tool? what query?
          → Retrieve / Search / Compute → Inspect results
          → "Enough, or iterate?" → Repeat until confident → Answer
```

**Why this matters for Agentic AI:** as LLMs move from *answering* to *acting*, retrieval stops being a fixed step and becomes a *decision the agent makes*. Poor retrieval decisions in an agent cause wrong tool choices, infinite loops, or hallucinated actions. RAG quality = agent reliability.

- ✅ Most flexible; handles complex, multi-hop questions.
- ❌ Slowest, most expensive, hardest to debug.

### Comparison Table

| Criteria | Standard RAG | Corrective RAG | Speculative RAG | Agentic RAG |
|---|---|---|---|---|
| **Pipeline** | Fixed, one-shot | Fixed + grading | Fixed, two-model | Dynamic, agent-driven |
| **Failure handling** | ❌ Silent hallucination | ✅ Detect & correct | ✅ Verifier catches errors | ✅ Reflection loops |
| **Speed** | ⚡ Fast | 🐢 Slower | ⚡⚡ Fastest (~2x) | 🐢 Slowest |
| **Cost** | Low | Medium | Low (big model) | High |
| **Complexity** | ⭐ | ⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| **Best for** | Simple Q&A, MVPs | Noisy corpora | Latency-sensitive apps | Multi-hop reasoning |

> 🎯 **Practical advice:** Start with **Standard RAG**. Move to **Corrective** when bad retrievals poison answers. Consider **Speculative** when latency matters at scale. Adopt **Agentic** only when queries genuinely need multi-step reasoning.

---

## 8. Common Mistakes & Best Practices

### ❌ Common Mistakes

1. **Chunks too big** — the model drowns in irrelevant text; retrieval precision drops.
2. **Chunks too small / no overlap** — sentences lose context, facts get split at boundaries.
3. **Weak prompt guardrails** — no "answer only from context" rule, no fallback → hallucination.
4. **One-shot testing** — the pipeline "works" on your demo question, fails on real user queries.
5. **Tuning generation before checking retrieval** — fixing the wrong stage.
6. **No citations** — users can't verify answers, trust collapses.

### ✅ Best Practices

1. **Chunk deliberately** — ~300–500 tokens with 10–15% overlap is a solid starting point.
2. **Always include a fallback instruction** — *"If the answer isn't in the context, say 'I don't know.'"*
3. **Diagnose retrieval before generation** — check which chunks were retrieved for failed queries.
4. **Test on edge cases** — ambiguous questions, out-of-scope questions, empty results.
5. **Return citations** — point to source document + page/chunk.
6. **Track and iterate** — log queries and failures; refine chunking, K, and prompts based on evidence.

---

## 9. Real Example: Input → Output + Key Takeaways

**Goal:** Ask a question against a PDF knowledge base and get a grounded, citable answer.

**Setup:** A vector DB containing chunks from `employee_handbook.pdf` (loaded, chunked at 400 tokens with 50 overlap, embedded with `all-MiniLM-L6-v2`, stored in ChromaDB).

**Input (user query):**

```
"How many sick days do employees get per year?"
```

**Step 1 — Retrieval:** Query embedded → similarity search returns top-3 chunks:

```
[chunk_021] employee_handbook.pdf p.4: "...full-time employees are entitled to
            12 paid sick days per calendar year, accruing monthly..."
[chunk_089] employee_handbook.pdf p.12: "...sick leave policy does not apply to
            contractors..."
[chunk_034] employee_handbook.pdf p.6: "...leave requests must be submitted
            through the HR portal..."
```

**Step 2 — Augmented prompt sent to the LLM:**

```
You are a helpful assistant. Answer ONLY from the context below.
If the answer is not in the context, say "I don't know."

Context:
[chunk_021] ...full-time employees are entitled to 12 paid sick days...
[chunk_089] ...sick leave policy does not apply to contractors...
[chunk_034] ...leave requests must be submitted through the HR portal...

Question: How many sick days do employees get per year?
```

**Step 3 — Output:**

```
Full-time employees are entitled to 12 paid sick days per calendar year,
accruing monthly. (Source: employee_handbook.pdf, page 4)
Note: this policy does not apply to contractors.
```

### 🔑 Key Takeaways

- **RAG grounds the model in YOUR data** — no hallucinated "30 days," no stale guesses; the answer comes from page 4 of your actual handbook.
- **Retrieval does the heavy lifting** — chunking and embeddings determined which text the model ever saw. Garbage in, garbage out.
- **Guardrails make it trustworthy** — the "only use context" rule plus the fallback turn RAG from a demo into a reliable system.
- **Start simple, evolve deliberately** — Standard RAG is enough for the PDF assistant; corrective, speculative, and agentic variants are upgrades for when you hit specific limits.
- **Evaluation never stops** — log real queries, inspect failures, refine one component at a time.

---

## 🛠️ Project: Building the PDF Knowledge Assistant

Everything in this guide comes together in a working project:

1. **Ingestion:** Load PDFs → chunk → embed (HuggingFace embeddings) → store in **ChromaDB**.
2. **Query:** Question → embed → similarity search (top-5) → augmented prompt → LLM → answer with source citations.
3. **Upgrade path:** conversational memory → streaming responses → UI.

> **The pipeline you'll implement IS standard RAG — Section 5's loop IS how you'll debug it — Section 7's architectures are where it can grow.**

---

## Quick Reference Card

| Stage | Action |
|---|---|
| Ingest | Load → Chunk (~300–500 tokens, ~15% overlap) → Embed → Store |
| Ask | Embed the question |
| Retrieve | Similarity search → top-K chunks |
| Generate | Augmented prompt + "answer only from context" + fallback |
| Evaluate | Log failures → diagnose retrieval vs. generation → refine |

**The key components:** Document · Chunking · Embedding · Vector DB · Retrieval · Augmentation · Generation
**The four architectures:** Standard → Corrective → Speculative → Agentic

Github: https://ragapp-3f2yica2wnxgcyqbmu9frm.streamlit.app/

---

*Part of the "RAG Foundations & PDF Knowledge Assistant" learning series.*
