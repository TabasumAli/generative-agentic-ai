# Lesson 15 — Agentic AI & AI Agents

> **From asking AI questions → to building AI systems that can reason, use tools, take actions, and work toward goals.**

Generative AI can produce answers. **Agentic AI goes a step further:** it can decide what to do, use tools, observe results, adjust its approach, and continue until a goal is completed.

---

## 1. What Is Agentic AI?

**Agentic AI** refers to AI systems designed to work toward a goal by taking actions rather than only generating a single response.

A traditional LLM interaction looks like:

```text
User
 ↓
LLM
 ↓
Answer
```

An agentic system can look like:

```text
Goal
 ↓
Understand
 ↓
Plan
 ↓
Act
 ↓
Observe
 ↓
Reason
 ↓
Act Again
 ↓
Goal Completed
```

The key idea is **autonomy around a goal**.

An agent may decide:

- What action should happen next?
- Which tool should be used?
- What information is missing?
- Did the previous action succeed?
- Should the plan change?
- Is the goal complete?

---

## 2. What Makes an AI System an Agent?

Not every AI application is an agent.

An AI system starts looking like an **agent** when it can operate around a goal and make decisions about its next actions.

A useful mental model is:

```text
Goal
 ↓
Decision
 ↓
Action
 ↓
Observation
 ↓
Decision
```

Important characteristics include:

- **Goal:** What should the system accomplish?
- **State:** What does the system currently know?
- **Reasoning:** What should happen next?
- **Planning:** What steps may be required?
- **Tools:** What external capabilities can it use?
- **Action:** What can it actually do?
- **Observation:** What happened after the action?
- **Progress:** Is the system getting closer to the goal?

An agent therefore combines an LLM with additional capabilities that allow it to operate in an environment.

---

## 3. AI Agent vs Chatbot

A **chatbot** primarily responds to user messages.

An **AI agent** can take actions to accomplish a goal.

| Chatbot | AI Agent |
|---|---|
| Responds to messages | Works toward a goal |
| Usually single-turn or conversational | Can perform multiple steps |
| Mainly generates text | Can reason and take actions |
| Limited external interaction | Can use tools |
| Usually waits for the user | Can continue through a task |
| Response-focused | Goal-focused |

### Example

**Chatbot:**

> User: "What's the weather in London?"

```text
Question → LLM → Answer
```

**Agent:**

> User: "Check tomorrow's weather in London and suggest what I should pack."

```text
Goal
 ↓
Get weather
 ↓
Analyze forecast
 ↓
Determine conditions
 ↓
Suggest clothing/items
 ↓
Final response
```

The agent performs a sequence of actions instead of simply producing one response.

---

## 4. Tool Calling — Giving Agents Capabilities

An LLM by itself cannot directly interact with every external system.

**Tool calling** gives an AI system access to functions it can invoke when needed.

Examples:

```text
Search Web
Get Weather
Query Database
Read File
Send Email
Call API
Run Code
Create Calendar Event
```

A simplified flow:

```text
User Request
     ↓
    LLM
     ↓
Need external information?
     ↓
   Tool Call
     ↓
Tool Executes
     ↓
Tool Result
     ↓
    LLM
     ↓
Final Answer
```

For example:

```text
User:
"Find the latest price of Product X."

LLM:
I need a product-search tool.

Tool Call:
search_product("Product X")

Tool Result:
$49.99

LLM:
"The current price is $49.99."
```

Tool calling is one of the most important building blocks of agentic systems because it connects **reasoning with real-world actions**.

---

## 5. Reasoning, Planning, Progress & the Action–Observation Loop

Agents often need more than one step.

Suppose the goal is:

> "Research three universities offering an AI master's degree and compare their requirements."

The agent may need to:

```text
Goal
 ↓
Plan
 ↓
Search University 1
 ↓
Observe Result
 ↓
Search University 2
 ↓
Observe Result
 ↓
Search University 3
 ↓
Observe Result
 ↓
Compare
 ↓
Generate Result
```

This creates an **action–observation loop**:

```text
        ┌─────────────────┐
        │     Reason      │
        └────────┬────────┘
                 ↓
        ┌─────────────────┐
        │      Action     │
        │   Use a Tool    │
        └────────┬────────┘
                 ↓
        ┌─────────────────┐
        │   Observation   │
        │   Tool Result   │
        └────────┬────────┘
                 ↓
        ┌─────────────────┐
        │ Evaluate Result │
        └────────┬────────┘
                 │
                 └──────→ Reason Again
```

The agent keeps evaluating its progress until it can complete the goal or determine that it needs help.

---

## 6. ReAct — Reason + Act

**ReAct** is a well-known agent pattern that combines reasoning with actions.

The basic idea is:

```text
Reason
  ↓
Act
  ↓
Observe
  ↓
Reason
  ↓
Act
  ↓
Observe
```

For example:

```text
Goal:
Find the population of a city and compare it
with another city.

Reason:
I need the population of City A.

Action:
Search City A population.

Observation:
Population = X

Reason:
Now I need City B.

Action:
Search City B population.

Observation:
Population = Y

Reason:
I can now compare the two.

Final Answer:
City A has X people, while City B has Y.
```

The important concept is not simply "thinking."

It is the **continuous interaction between decisions and actions**.

```text
Reason → Act → Observe → Reason → Act → Observe
```

This pattern is useful when the next action depends on the result of the previous action.

---

## 7. Plan → Execute → Reflect + Human-in-the-Loop

A more structured agent pattern is:

```text
        PLAN
          ↓
       EXECUTE
          ↓
       OBSERVE
          ↓
       REFLECT
          ↓
    Goal Complete?
       ↙       ↘
     No         Yes
     ↓           ↓
   Re-plan     Finish
```

### Plan

Break a larger goal into manageable steps.

### Execute

Perform the planned actions using available tools.

### Reflect

Evaluate what happened:

- Did the action work?
- Is the information sufficient?
- Did something fail?
- Should the plan change?

### Human-in-the-Loop

Not every action should be fully autonomous.

For sensitive or high-impact actions, an agent can ask a human for approval:

```text
Agent
 ↓
Proposes Action
 ↓
Human Approval
 ↓
Execute
 ↓
Observe
```

Examples:

- Sending an important email
- Approving a financial transaction
- Deleting data
- Publishing content
- Making a production deployment

A strong agentic system knows when to **act independently and when to request human confirmation**.

---

## 8. When Should We Use an LLM, Fixed Workflow, or Agent?

Not every problem needs an agent.

### Normal LLM

Use a normal LLM when the task is mainly:

```text
Input → Generate → Output
```

Examples:

- Summarization
- Translation
- Brainstorming
- Writing
- Explanation

### Fixed Workflow

Use a fixed workflow when the steps are predictable.

```text
Step 1
 ↓
Step 2
 ↓
Step 3
 ↓
Output
```

Examples:

- Document processing
- Invoice extraction
- Data validation
- Standard reporting pipelines

### Agent

Use an agent when:

- The next step depends on previous results.
- Multiple tools may be required.
- The path to the solution is not completely known beforehand.
- The system needs to adapt while working.
- The task involves multiple decisions.

A simple mental model:

```text
Simple generation
      ↓
   Normal LLM

Predictable process
      ↓
 Fixed Workflow

Dynamic, tool-using, goal-oriented process
      ↓
     Agent
```

**More autonomy does not automatically mean better.**

Use the simplest architecture that reliably solves the problem.

---

## 9. Agent Framework Landscape & CrewAI Mental Model

Agent systems can be built at different levels.

### Agent Framework Landscape

```text
                 Agentic AI
                     │
        ┌────────────┼────────────┐
        ↓            ↓            ↓
    Core Python   LangChain   LangGraph
                                   │
                                   ↓
                                CrewAI
```

### Core Python

You can build an agent loop yourself:

```python
while not goal_completed:
    decision = llm(...)
    result = execute_tool(decision)
    state.append(result)
```

This gives maximum control and helps you understand the fundamentals.

### LangChain

Useful for connecting LLMs with:

- Tools
- Retrievers
- Models
- Prompts
- Agent components

### LangGraph

Designed for more structured, stateful agent workflows and graph-based execution.

### CrewAI

CrewAI provides a higher-level mental model around agents, tasks, tools, and execution.

---

### CrewAI Single-Agent Mental Model

A useful way to understand a single CrewAI agent is:

```text
                 AGENT
                   │
       ┌───────────┼───────────┐
       ↓           ↓           ↓
     Role         Goal       Backstory
       │           │           │
       └───────────┼───────────┘
                   ↓
                  LLM
                   ↓
                 Tools
                   ↓
               Tool Call
                   ↓
               Tool Result
                   ↓
                Evaluate
                   ↓
             Goal Completion
```

Think of an agent as a worker with:

- **Role:** Who is the agent?
- **Goal:** What should it accomplish?
- **Backstory:** What context or expertise should guide it?
- **LLM:** The reasoning/generation engine.
- **Tools:** What capabilities can it access?
- **Tool Calls:** What actions does it request?
- **Tool Results:** What did those actions return?
- **Goal Completion:** Has the task been successfully completed?

### Example

```text
Role:
Research Assistant

Goal:
Find reliable information about AI master's programs.

Backstory:
You are a research assistant specializing in
graduate-level computer science research.

LLM:
Reasoning and generation model

Tools:
Web search + document reader

Execution:
Understand → Search → Observe → Refine → Compare

Completion:
Produce a sourced comparison.
```

This mental model helps connect the abstract idea of **agents** with the practical components used in agent frameworks.

---

# Key Takeaways

- **Agentic AI** focuses on goal-oriented systems that can take actions.
- An **AI agent** combines an LLM with state, tools, actions, observations, and decision-making.
- **Chatbots** mainly respond; agents can work through multi-step goals.
- **Tool calling** connects LLMs to external capabilities.
- The **action–observation loop** allows agents to adapt based on results.
- **ReAct** combines reasoning with acting and observing.
- **Plan → Execute → Reflect** provides a structured approach to agent execution.
- **Human-in-the-loop** keeps people involved when approval or oversight is important.
- Use a **normal LLM for simple generation, fixed workflows for predictable processes, and agents for dynamic multi-step tasks**.
- Agent frameworks range from **core Python → LangChain → LangGraph → CrewAI**, each providing different levels of abstraction and control.
- In CrewAI, think in terms of **role → goal → backstory → LLM → tools → tool calls → tool results → goal completion**.

> **An LLM generates. A workflow executes predefined steps. An agent decides what to do next in pursuit of a goal.**
