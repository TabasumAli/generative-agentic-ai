# 📄 Building a Resume Review Agent with CrewAI, Groq & Streamlit

## 1. 🎯 The Hiring Challenge

Manually comparing a candidate’s resume against a target job description is time-consuming, inconsistent, and often prone to human oversight. Job seekers frequently apply to positions without knowing how well their experience aligns with the job requirements or what key skills they are missing.

An AI-driven **Resume Review Agent** solves this problem by performing an instant, objective, and structured match analysis.

### Focus Areas
* **Matching Skills:** Identifying qualifications in the resume that explicitly line up with the job requirements.
* **Missing Skills:** Highlighting essential or preferred requirements listed in the job description that are completely absent from the resume.
* **Weak Areas:** Uncovering places where related experience exists but lacks depth, quantitative impact, or clear demonstration.
* **Improvement Opportunities:** Providing concrete steps to optimize the resume's impact, formatting, and alignment with the target role.

> 🚨 **Critical Rule — Anti-Fabrication Mandate:** The agent must **never** invent qualifications, hallucinate past roles, or assume a candidate possesses a skill simply because it appears in the job description. If a skill is omitted from the resume, it must be treated as missing until verified by the candidate.

---

## 2. 🤖 What Is a Resume Review Agent?

The **Resume Review Agent** is a single-agent AI application designed to evaluate candidates against specific job descriptions and deliver actionable career coaching feedback.

Powered by **CrewAI** for agent orchestration and **Groq LLMs** (`llama-3.3-70b-versatile`) for ultra-fast reasoning, the application accepts both plain text and PDF formats and turns raw job description requirements into structured guidance.

### Supported Inputs
* **Resume:** Pasted text OR uploaded `.pdf` file.
* **Job Description:** Pasted target job specification.

### Core Processing Flow

```
[Resume (Text/PDF)] + [Job Description]
                  │
                  ▼
          [CrewAI Agent]
                  │
                  ▼
         [Analyze Role Match]
                  │
                  ▼
        [Identify Skill Gaps]
                  │
                  ▼
   [Generate Recommendations]
                  │
                  ▼
      [Structured Review Output]
```

---

## 3. 🏗️ Resume Review Agent Architecture

The application adopts a lightweight, single-agent architecture designed for speed and reliability.

```
+-------------------------------------------------------+
|                    Streamlit UI                       |
|   (Captures Resume Text/PDF & Job Description Input)  |
+---------------------------+---------------------------+
                            |
                            v
+-------------------------------------------------------+
|                    CrewAI Agent                       |
|   Role: Senior Technical Recruiter & Career Coach     |
|   Goal: Provide honest, actionable gap analysis       |
+---------------------------+---------------------------+
                            |
                            v
+-------------------------------------------------------+
|                   Groq LLM API                        |
|           (llama-3.3-70b-versatile)                   |
+---------------------------+---------------------------+
                            |
                            v
+-------------------------------------------------------+
|           Structured Markdown & Metrics               |
|  (Match Score, Matching Skills, Missing Skills, etc.) |
+-------------------------------------------------------+
```

### Agent Persona Configuration
* **Role:** Senior Technical Recruiter & Career Coach
* **Goal:** Evaluate candidate resumes against job descriptions accurately, highlighting real strengths, exposing explicit skill gaps, and offering honest, practical recommendations without inflating experience.
* **Frontend-Backend Bridge:** The Streamlit frontend collects inputs, parses uploaded PDFs, passes the combined text payload into the CrewAI Task execution loop, and renders the returned Markdown and numeric match scores directly in interactive UI components.

---

## 4. 📥 Input Processing — PDF or Text

To ensure a seamless user experience, the system provides two distinct ways to submit a resume alongside robust validation routines.

```
       [User Input Choice]
          /          \
         /            \
  (Pasted Text)    (PDF Upload)
        │              │
        │              ▼
        │         [pypdf Parser]
        │              │
        └──────┬───────┘
               │
               ▼
     [Validation & Guardrails]
               │
               ▼
        [CrewAI Task Payload]
```

### Input Options
1. **Option 1 (Direct Text):** Copy and paste raw resume text into a text area.
2. **Option 2 (PDF Upload):** Upload a document processed via `pypdf.PdfReader` to extract embedded text stream pages.

### Robust Guardrail & Error Handling
To prevent application crashes, input validation explicitly catches edge cases before triggering agent runs:

| Edge Case | Detection Method | User Feedback Action |
| :--- | :--- | :--- |
| **Empty Resume / Job Description** | String length check (`len(text.strip()) == 0`) | Displays warning: *"Please provide both a resume and a job description."* |
| **Corrupted / Invalid PDF** | `pypdf` parsing exception | Displays error: *"Unable to process PDF. Please check the file or paste plain text."* |
| **Scanned / Image-Only PDF** | Extracted text length string equals `""` | Displays error: *"No extractable text found in PDF. If this is a scanned document, please paste text directly."* |
| **API Failure / Rate Limits** | Catch API exception block | Captures traceback and presents friendly alert message with retry guidance. |

---

## 5. 🔍 Gap Analysis — Matching Skills vs Missing Skills

The core capability of the agent lies in systematic qualification matching. The agent breaks down the candidate profile and compares it directly against the requirements of the job description.

### Analysis Categories
* **Matching Skills:** Direct evidence of required tools, frameworks, or competencies explicitly stated in the resume.
* **Missing Skills:** Requirements explicitly requested in the job description that do not appear anywhere in the candidate's document.
* **Weak Areas:** Skills or responsibilities that are listed, but lack context, recency, depth, or measurable metrics.

### Verification Matrix Example

| Requirement (JD) | Identified in Resume? | Status |
| :--- | :--- | :--- |
| **Python** | *"3+ years developing backends in Python"* | ✅ Present |
| **REST APIs** | *"Designed and consumed REST APIs using Django"* | ✅ Present |
| **SQL** | *"Managed PostgreSQL schemas"* | ✅ Present |
| **Docker** | *Not listed* | ❌ Missing |
| **AWS** | *Not listed* | ❌ Missing |
| **Git** | *"Used Git for version control"* | ✅ Present |

> ⚠️ **Strict Non-Inference Rule:** Even if a candidate mentions building large web platforms, the agent **must not infer** that they know Docker or AWS unless explicitly stated in the text.

---

## 6. 💡 Recommendations — Turning Gaps into Actions

Identifying gaps is only half the job. The agent translates identified missing or weak skills into immediate, actionable resume improvements.

### Categories for Recommendations
1. **Resume Wording & Keywords:** Rephrasing existing bullet points to align with industry terminology without misrepresenting experience.
2. **Missing Experience Strategy:** Providing two clear options depending on the candidate's real-world background:
   * **If experience exists:** Advise the candidate on where and how to incorporate the keyword into previous work or project sections.
   * **If experience is missing:** Advise building a target side project or completing specialized coursework before listing the skill.
3. **Quantifying Achievements:** Encouraging conversion of passive duty lists into high-impact metric statements.

### Example Recommendation Strategy

* **Identified Gap:** Docker is missing from the resume.
* **Agent Recommendation:** 
  > *"If you have hands-on experience containerizing applications with Docker, add a bullet point under your recent projects detailing how you containerized your backend APIs. If you do not have Docker experience, create a small containerized application and publish it on GitHub before adding Docker as a skill."*

---

## 7. 📊 Structured Output & Match Score

The output returned by the agent follows a strict Markdown structure, providing both quantitative ratings and qualitative analysis.

### Standardized Section Layout
1. **Match Summary:** A high-level qualitative overview of candidate alignment.
2. **Matching Skills:** Bulleted list of confirmed matching competencies.
3. **Missing or Weak Areas:** Bulleted breakdown of non-existent or underdeveloped skills.
4. **Improvement Recommendations:** Numbered, prioritized action items.
5. **Match Score:** A calculated numeric score ($0\text{--}100$) accompanied by justification.

```
Match Score: 72 / 100
Justification: Strong alignment on core language (Python) and API design concepts, 
but lacks cloud infrastructure (AWS) and containerization (Docker) competencies 
emphasized in the job description.
```

### Streamlit UI Components
To make the analysis easy to read, the Streamlit frontend maps sections into distinct components:
* `st.metric()` for displaying the overall **Match Score**.
* `st.tabs()` to divide *Summary*, *Detailed Analysis*, and *Recommendations*.
* `st.expander()` for displaying full raw prompt logs or extra debugging details.

---

## 8. 🧪 Technology Stack & Testing Phase

### Core Technology Stack

| Technology | Purpose |
| :--- | :--- |
| **Python 3.11+** | Application runtime |
| **Streamlit** | Interactive web application frontend |
| **CrewAI** | Multi-agent orchestration framework |
| **Groq** | Fast LLM inference host provider |
| **`llama-3.3-70b-versatile`** | Active production LLM model |
| **pypdf** | PDF parsing and text extraction engine |
| **LiteLLM** | Unified LLM abstraction layer for CrewAI |
| **Streamlit Secrets** | Production environment secret and API key management |

### Secret Management Configuration
API keys are maintained securely using Streamlit Secrets.
```python
# Accessing API key safely in code
import os
import streamlit as st

groq_api_key = st.secrets["GROQ_API_KEY"]
os.environ["GROQ_API_KEY"] = groq_api_key
```

### Comprehensive Test Matrix

```
                          [Testing Framework]
                                   │
      ┌────────────────────────────┼────────────────────────────┐
      ▼                            ▼                            ▼
[Input Validation]         [API & Guardrails]           [Output Quality]
• Pasted text              • Missing API Key            • Strong match profile
• Valid PDF upload         • Invalid API Key            • Weak match profile
• Empty input fields       • Groq Rate Limits (429)     • Ambiguous JD criteria
• Scanned / Image PDFs     • Exception fallbacks        • Non-fabrication check
```

---

## 9. 🚀 GitHub, Deployment & Real Example — Input → Output

### Minimal Repository Structure

```
resume-review-agent/
│
├── app.py                   # Streamlit web application & CrewAI orchestration
├── requirements.txt         # Project dependencies (streamlit, crewai, pypdf, etc.)
├── README.md                # Documentation and setup instructions
│
└── .streamlit/
    └── secrets.toml         # LOCAL ONLY — contains secrets (NEVER commit to git)
```

### Deployment Pipeline
1. Push codebase to a **GitHub repository** (ensuring `.streamlit/secrets.toml` is in `.gitignore`).
2. Log into **Streamlit Community Cloud** and create a **New app**.
3. Link the GitHub repository, branch, and entry point (`app.py`).
4. In **Advanced Settings $\rightarrow$ Secrets**, add the production key:
   ```toml
   GROQ_API_KEY = "gsk_your_groq_production_api_key_here"
   ```
5. Deploy and monitor live logs.

---

### Real-World Execution Example

#### Sample Job Description Input
```text
Looking for a Backend Developer with experience in:
Python, REST APIs, SQL, Docker, AWS, Git,
and building production backend applications.
```

#### Sample Candidate Resume Input
```text
Backend Developer with experience in Python,
Django, REST APIs, PostgreSQL, Git, and backend
API development.
```

#### Expected Agent Analysis Output

```markdown
### 📊 Match Score: 70 / 100

### 🎯 Match Summary
The candidate demonstrates strong alignment with backend core development stack (Python, REST APIs, SQL, Git), but lacks explicit cloud deployment (AWS) and containerization (Docker) background requested in the job description.

### ✅ Matching Skills
* **Python:** Explicitly listed under backend experiences.
* **REST APIs:** Demonstrated via Django API development experience.
* **SQL / Databases:** Supported by PostgreSQL usage.
* **Git:** Listed under core developer skills.
* **Backend Application Development:** Directly validated through past project roles.

### ❌ Missing or Weak Areas
* **Docker:** No mention of containerization tools or workflows.
* **AWS:** Cloud platform services are absent from candidate history.

### 💡 Actionable Improvement Recommendations
1. **Highlight Existing Containerization Experience:** If you have used Docker in side projects or prior jobs, explicitly list container orchestration bullet points under your project section.
2. **Build a Dockerized Project:** If you lack Docker experience, containerize your existing Django/PostgreSQL backend application before adding Docker to your resume.
3. **Gain Cloud Exposure:** Gain hands-on exposure deploying applications to AWS (e.g., Elastic Beanstalk or EC2) prior to listing AWS as a core skill.
4. **Quantify Backend Achievements:** Add metrics to API bullet points (e.g., *"Built REST APIs serving 10,000+ daily requests"*).
```

---

## 📌 Key Takeaways

* **Resume vs. JD Alignment:** Automates gap analysis to help job seekers tailor their applications effectively.
* **Single-Agent Simplicity:** Uses a streamlined single-agent CrewAI pattern paired with Groq (`llama-3.3-70b-versatile`) for reliable execution speed.
* **Dual Input Pipeline:** Supports plain text and PDF upload with robust fallback guardrails for scanned files or empty submissions.
* **Strict Non-Fabrication Rule:** Prevents hallucinated experience, insisting that recommendations build upon legitimate candidate qualifications.
* **Actionable Feedback:** Turns missing skills into specific learning outcomes or resume formatting instructions.
* **Production Deployment:** Uses Streamlit Community Cloud and Streamlit Secrets for safe, credential-secure deployments.


Project link: https://github.com/TabasumAli/agentic_resume_analyzer
App link: https://agenticresumeanalyzer.streamlit.app/

> 💡 **Core Philosophy:** The goal of an AI resume reviewer is **not** to decide whether someone should be hired. Its job is to help candidates understand the relationship between their current resume and a target role — and identify concrete ways to improve.