# 🎓 Lesson 14 — Study Tutor Agent

### 1. 🎯 The Learning Challenge

Explain the problem this application solves.

Students often need different kinds of study support:

- Understanding difficult concepts
- Testing their knowledge with quizzes
- Creating personalized study plans
- Summarizing notes
- Creating flashcards for revision

Explain why combining these capabilities into **one AI tutor** is useful.

Show the overall idea:

```
Student Request
      ↓
Understand Learning Need
      ↓
Choose Appropriate Capability
      ↓
Generate Result
      ↓
Continue Conversation with Memory
```

Emphasize that the goal is to create a **personalized, on-demand AI study companion** rather than a basic chatbot.

---

### 2. 🤖 What Is a Study Tutor Agent?

Explain that the Study Tutor Agent is a **single-agent AI application** built with **CrewAI**.

Technology:

- **CrewAI** → agent framework
- **Groq `openai/gpt-oss-120b`** → LLM
- **FastAPI** → backend
- **Streamlit** → frontend
- **Short-term memory** → conversational context
- **5 custom study tools** → learning capabilities

Explain the agent persona:

> **Study Tutor**

The agent understands what the student wants and uses the appropriate capability to help.

Show:

```
Student
   ↓
Streamlit Frontend
   ↓
FastAPI Backend
   ↓
CrewAI Study Tutor Agent
   ↓
Groq LLM + Study Tools
   ↓
Tutor Response
```

Mention that this implementation uses **Groq's OpenAI-compatible endpoint** with `openai/gpt-oss-120b` and does **not require LiteLLM**.

---

### 3. 🏗️ System Architecture

Explain the frontend/backend/agent architecture.

```
┌──────────────────────────────┐
│       Streamlit Frontend     │
│   Chat UI + Sidebar + Input  │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│         FastAPI Backend      │
│      API Request Handling    │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│       CrewAI Agent           │
│        Study Tutor           │
└──────────────┬───────────────┘
               ↓
        ┌──────┴──────┐
        ↓             ↓
     Groq LLM     Study Tools
        ↓             ↓
        └──────┬──────┘
               ↓
         Tutor Response
```

Also explain **short-term memory**.

The application remembers the **last 6 turns per session**, allowing follow-up questions to maintain context.

Example:

```
Student: Explain recursion.

Tutor: [explains recursion]

Student: Give me an example.

Tutor: [uses the previous conversation context]
```

---

### 4. 🛠️ The 5 Custom Study Tools

Explain each of the five tools in detail.

#### 💡 1. Explain Concept

Provides simple and understandable explanations.

Example:

```
"Explain recursion like I'm 12."
```

#### 📝 2. Generate Quiz

Generates questions to test the student's understanding.

Example:

```
"Quiz me on the French Revolution — 5 questions."
```

#### 📅 3. Create Study Plan

Creates a structured learning schedule.

Example:

```
"Give me a 10-day plan to learn React."
```

#### 📄 4. Summarize Text

Converts longer notes into concise summaries.

Example:

```
"Summarize: <paste your notes>"
```

#### 🧠 5. Flashcard Maker

Creates Q&A flashcards for revision.

Example:

```
"Make 8 flashcards for the periodic table trends."
```

Explain that the agent can determine which study capability is appropriate based on the student's request.

---

### 5. 🔄 How the Agent Works — Request → Decision → Tool → Response

Explain the complete agentic loop:

```
1. Student sends request
          ↓
2. Agent understands the request
          ↓
3. Agent decides what is needed
          ↓
4. Agent selects an appropriate tool
          ↓
5. Tool executes
          ↓
6. Agent receives the result
          ↓
7. Agent formats the response
          ↓
8. Student receives the answer
```

Use a concrete example:

```
Student:
"Make a 7-day Python study plan."

        ↓

CrewAI Study Tutor Agent

        ↓

Create Study Plan Tool

        ↓

7-Day Python Study Plan

        ↓

Student
```

Explain the important agentic concept:

> The agent isn't restricted to one fixed response pattern. It can select different tools depending on the student's learning goal.

---

### 6. 🧪 Technology Stack & Testing Phase

Include this technology stack:

| Technology            | Purpose                   |
| --------------------- | ------------------------- |
| Python                | Application/backend logic |
| CrewAI                | Single-agent framework    |
| Groq                  | LLM provider              |
| `openai/gpt-oss-120b` | Language model            |
| FastAPI               | Backend API               |
| Streamlit             | Frontend UI               |
| Short-term memory     | Conversation context      |
| GitHub                | Version control           |
| Render                | Backend deployment        |
| Streamlit Cloud       | Frontend deployment       |

Mention the Streamlit UI design:

- Black background
- White text
- Yellow buttons
- Sidebar
- Chat-style interface

Testing should include:

- Normal study questions
- All 5 custom tools
- Follow-up questions
- Memory across turns
- Empty/invalid inputs
- Missing API key
- Invalid API key
- Groq API errors
- Rate limits
- Backend connection failures
- Long requests

Explain that testing should verify both **tool selection** and **conversation memory**.

---

### 7. 🚀 Deployment — Render + Streamlit Cloud

Explain the two-part deployment.

#### Backend → Render

Configuration:

```
Root Directory:
backend

Build Command:
pip install -r requirements.txt

Start Command:
uvicorn app:app --host 0.0.0.0 --port $PORT
```

Explain the `.python-version` file:

```
3.13.4
```

The project pins Python 3.13.4 because the provided project configuration requires a Python version below 3.14 for CrewAI.

#### Frontend → Streamlit Cloud

Main file:

```
frontend/streamlit_app.py
```

Configure:

```
BACKEND_URL = "https://your-backend.onrender.com"
```

Explain that Streamlit communicates with the FastAPI backend.

Also explain the project's API-key design:

- User enters their Groq API key through the frontend.
- It is sent to the backend per request.
- It is not persisted by the application.

---

### 8. 📁 GitHub Project Structure

Use the actual project structure:

```
study-tutor-agent/
│
├── .python-version
├── .gitignore
├── README.md
│
├── backend/
│   ├── app.py
│   ├── agent.py
│   ├── tools.py
│   ├── memory.py
│   ├── requirements.txt
│   └── .gitignore
│
└── frontend/
    ├── streamlit_app.py
    ├── requirements.txt
    └── .gitignore
```

Explain the important files:

**`backend/app.py`**

- FastAPI entry point
- API request handling

**`backend/agent.py`**

- CrewAI agent
- Groq LLM configuration

**`backend/tools.py`**

- Five custom study tools

**`backend/memory.py`**

- Short-term memory implementation

**`frontend/streamlit_app.py`**

- Streamlit interface
- User interaction
- Backend communication

Explain why separating frontend and backend makes the application easier to develop, deploy, and maintain.

---

### 9. 💬 Real Example — Input → Output

This section must contain **realistic examples from the application's intended use cases**.

#### Example 1 — Concept Explanation

**Input:**

```
Explain recursion like I'm 12.
```

**Output:**

A simple explanation of recursion using an everyday analogy, followed by the idea of a function calling itself and eventually reaching a base case.

---

#### Example 2 — Study Plan

**Input:**

```
Give me a 7-day plan to learn Python.
```

**Output:**

```
Day 1: Python Basics
Day 2: Control Flow
Day 3: Data Structures
Day 4: Functions
Day 5: Modules & Files
Day 6: Practice Project
Day 7: Revision + Final Project
```

---

#### Example 3 — Flashcards

**Input:**

```
Make 8 flashcards for the periodic table trends.
```

**Output:**

Generate concise Q&A flashcards covering concepts such as atomic radius, ionization energy, electronegativity, and periodic trends.

---

## 🔑 Key Takeaways

- Single-agent architecture
- CrewAI
- Groq `openai/gpt-oss-120b`
- Five custom study tools
- Short-term memory
- Streamlit frontend
- FastAPI backend
- Render deployment
- Streamlit Cloud deployment
- Secure API-key handling
- Tool selection
- Agentic workflow



App link: https://github.com/TabasumAli/study_tutor_agent

https://studytutoragent-net.streamlit.app/

https://study-tutor-backend.onrender.com


> **Instead of building five separate study applications, one AI agent can understand the student's goal and use the right tool to help them learn.**