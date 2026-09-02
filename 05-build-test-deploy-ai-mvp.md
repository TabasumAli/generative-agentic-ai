# Lesson 5 — Build, Test & Deploy an AI MVP

> **An AI idea becomes real when people can actually use it.**

## 1. Introduction

In the previous lesson, we built a simple Generative AI application.

Now we're taking the next step:

> **Build → Test → Deploy**

For this lesson, we built **ThreatLens** — a small AI-powered threat intelligence assistant that helps users investigate an IP address, domain, or URL.

ThreatLens combines external security data with an LLM to turn technical results into a human-readable explanation.

The goal isn't to build a complete cybersecurity platform. It's to build a useful **MVP (Minimum Viable Product)** that solves one focused problem.

---

## 2. What Is an AI MVP?

An **MVP** is the smallest version of a product that provides real value to users.

Instead of building a huge cybersecurity platform with authentication, dashboards, threat feeds, analytics, alerts, and AI agents, we start with:

```text
User Input
    ↓
Security APIs
    ↓
AI Analysis
    ↓
Useful Result
```

### MVP mindset

> **Build the smallest useful version first. Improve it after you learn from real usage.**

---

## 3. Meet ThreatLens 🔍

**ThreatLens** is an AI-powered threat intelligence assistant.

A user selects an input type, enters an indicator, chooses their knowledge level, and clicks **Analyze**.

The application uses:

- **VirusTotal** → threat detection and security analysis
- **WHOIS** → domain/registration information
- **Groq** → AI-powered explanation
- **Streamlit** → web interface

The knowledge-level input allows explanations to be tailored for different users:

```text
Beginner
   ↓
Simple explanation
   ↓
Intermediate
   ↓
More technical explanation
   ↓
Expert
   ↓
Technical security analysis
```

---

## 4. How ThreatLens Works

The application follows a simple pipeline:

```text
User
 ↓
IP / Domain / URL
 ↓
Input Validation
 ↓
VirusTotal + WHOIS
 ↓
Collect Security Data
 ↓
Groq / LLM
 ↓
Risk Explanation
 ↓
Streamlit UI
```

The LLM isn't responsible for discovering everything by itself.

We first collect information from external security services and then ask the model to **interpret and explain that information**.

This is an important AI application pattern:

> **External Data → AI Reasoning → Human-Friendly Output**

---

## 5. Building the MVP

A simple MVP can be broken into four components:

### Input Layer

The user provides:

```text
Input Type
Knowledge Level
IP / Domain / URL
```

### Data Layer

The application retrieves relevant information from services such as VirusTotal and WHOIS.

### AI Layer

Groq sends the collected information to an LLM that produces a readable risk explanation.

### Presentation Layer

Streamlit displays:

- Verdict
- Detection counts
- Metadata
- AI explanation
- Relevant security information

```text
┌───────────────┐
│ User Input    │
└───────┬───────┘
        ↓
┌───────────────┐
│ Security APIs │
└───────┬───────┘
        ↓
┌───────────────┐
│ AI Analysis   │
└───────┬───────┘
        ↓
┌───────────────┐
│ Streamlit UI  │
└───────────────┘
```

---

## 6. Testing the Application

Building the app is only half the job.

We need to test different scenarios.

### Test 1 — Known Safe URL

```text
Input:
https://www.google.com
```

Check whether the security information and verdict are displayed correctly.

### Test 2 — Suspicious or Malicious Indicator

Use a controlled test indicator or a known security-testing sample rather than randomly visiting suspicious websites.

Check whether:

- The API returns detection data.
- The verdict is displayed correctly.
- The AI explanation matches the available evidence.
- Errors are handled properly.

### Test 3 — Invalid Input

Try:

```text
hello-world
```

The application should respond gracefully rather than crashing.

### Test 4 — Different Knowledge Levels

Run the same indicator as:

```text
Beginner
Intermediate
Expert
```

The underlying security data should remain consistent, while the explanation can change in complexity.

---

## 7. Handling Real-World Problems

Real applications don't always receive perfect responses.

APIs can:

- Fail
- Timeout
- Return incomplete data
- Reach rate limits
- Return unexpected formats

A good MVP should handle errors gracefully.

Instead of:

```text
Application crashed ❌
```

we want:

```text
⚠️ Unable to retrieve VirusTotal data.
Please try again later.
```

Useful protections include:

- Input validation
- API timeout handling
- Missing-data handling
- Clear error messages
- Avoiding unnecessary API calls

A polished application isn't one that never encounters errors.

It's one that **handles errors intelligently**.

---

## 8. Deploying the MVP 🚀

Once the application works, we want other people to use it.

For ThreatLens, we deployed the Streamlit application publicly.

```text
Development
     ↓
Build
     ↓
Test
     ↓
GitHub
     ↓
Streamlit
     ↓
Public Web App
     ↓
Users
```

### 🔐 Protect API Keys

Never commit API keys to GitHub.

Bad:

```python
GROQ_API_KEY = "gsk_..."
VIRUSTOTAL_API_KEY = "..."
```

Use environment variables or your deployment platform's secret management instead.

For Streamlit deployments, store secrets securely rather than exposing them in source code.

---

## 9. Real Example — Input → Output

### Input

```text
Input Type:
URL

Knowledge Level:
Intermediate

URL:
https://www.google.com
```

### ThreatLens Output

The application can display:

```text
Verdict: HARMLESS

Malicious: 0
Suspicious: 0
Harmless: 64
Undetected: 27
```

It can then show metadata such as:

```text
Final URL: https://www.google.com/
Title: Google
Reputation: 408
```

Finally, the LLM converts the collected security information into a plain-language explanation.

For a different indicator, the result may look like:

```text
Verdict: MALICIOUS

Malicious: 2
Suspicious: 0
Harmless: 60
Undetected: 30
```

The important lesson is not the specific numbers.

It's the workflow:

> **Indicator → Security Data → AI Interpretation → Human-Friendly Risk Explanation**

### 🚀 Try ThreatLens

**Google Colab — Source & Development**

urlOpen ThreatLens in Google Colabhttps://colab.research.google.com/drive/1Vp5qCxw6omm6lcssDnDkuWo4vvIie7nQ?usp=sharing

**Live Streamlit Application**

urlOpen ThreatLens Live Apphttps://threatlens-8sihqqyvmcebsjbuqzirzh.streamlit.app/

---

# Key Takeaways

> **The goal of an MVP isn't to build everything. It's to build enough to prove that the idea is useful.**

Remember:

1. **Start small.**
2. **Solve one clear problem.**
3. **Connect your application to useful APIs.**
4. **Use AI where it adds real value.**
5. **Test normal, invalid, and edge-case inputs.**
6. **Handle API failures gracefully.**
7. **Protect your secrets and API keys.**
8. **Deploy so real users can try the product.**
9. **Collect feedback and iterate.**

The complete journey:

```text
Idea
 ↓
MVP
 ↓
Build
 ↓
Test
 ↓
Fix
 ↓
Deploy
 ↓
Feedback
 ↓
Improve
```

That's how an AI idea starts becoming a **real product**.
