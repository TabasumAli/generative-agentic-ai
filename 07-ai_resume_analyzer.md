# AI Resume Assistant - Documentation

## 1. The Problem
Many companies use ATS systems to scan resumes. A resume can be good but still perform poorly because of missing keywords, weak structure, or formatting issues. The goal is to make resume analysis easier with AI.

---

## 2. The Project — AI Resume Assistant
A Streamlit app that lets users upload a PDF or DOCX resume. It uses the Groq API to analyze the resume. It provides an ATS compatibility score and practical improvement suggestions.

---

## 3. How the App Works
Upload Resume → Extract Text → Validate Resume → Send to Groq → AI Analysis → Display Results

The app extracts text from PDFs using `pdfplumber` and DOCX files using `python-docx`.

---

## 4. Resume Validation
Before running the AI analysis, the app checks whether the uploaded document actually looks like a resume.

It checks for: Experience, Education, Skills, Projects, Contact info, Action verbs, Professional terminology, and Quantifiable achievements.

Result: High, Medium, or Low confidence.

---

## 5. AI-Powered ATS Analysis
The resume text is sent to Groq with a structured prompt.

The AI returns:
* Overall ATS score & Strengths
* ATS risks & Specific issues
* Strong keywords & Missing useful words
* Recommended improvements & Section-by-section feedback

---

## 6. Five-Dimension ATS Score

| Dimension | What It Measures |
|---|---|
| **Parseability** | How easily an ATS can read the resume |
| **Keyword Alignment** | Relevant role-specific keywords |
| **Section Structure** | Organization and standard headings |
| **Skills Relevance** | Match between skills and target role |
| **Achievement Quality** | Action verbs and measurable results |

---

## 7. Interactive Results Dashboard
Organizes results into tabs: Overview → Score Breakdown → Keywords & Risks → Issues & Fixes → Section Feedback → Strengths.

Includes visual score gauge, keyword chips, recommendations, and raw JSON output.

---

## 8. From AI Output to Actionable Feedback
Explains **why** the score is what it is and **what** to improve.

* **Example:** Keyword Alignment: 58/100
* **Problem:** Important role-related keywords are missing.
* **Action:** Add relevant skills and keywords that naturally match the target position.

---

## 9. What This Project Demonstrates
Combines: Python, Streamlit, Groq / LLMs, PDF & DOCX processing, Regular expressions, Structured JSON, Prompt engineering, Error handling, and Interactive UI design.

Real Problem → Document Processing → AI Integration → Structured Output → Interactive UI → Useful Product
Github link: https://github.com/TabasumAli/AI-Resume_Analyzer

**The goal isn't just to connect an AI API to an application. The goal is to turn AI into something genuinely useful.**