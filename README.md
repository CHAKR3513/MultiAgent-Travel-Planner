# MultiAgent-Travel-Planner

# 🤖 Multi-Agent Travel Planner using LangGraph, MCP, Supervisor, Guardrails & HITL

A production-inspired **Multi-Agent AI system** for travel planning built using **LangGraph, MCP (Model Context Protocol), LLM-based Supervisor orchestration, Guardrails, and Human-in-the-Loop (HITL)**.

The system accepts a user's travel request, validates the request through guardrails, coordinates specialized AI capabilities through a supervisor-driven workflow, retrieves external information through MCP tools, generates a travel plan, and pauses for **human approval before finalizing the response**.

---

## 🏗️ Architecture

```text
                         ┌──────────────────────┐
                         │        User          │
                         │  Travel Request      │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │      FastAPI         │
                         │    Application       │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │   Input Guardrails   │
                         │                      │
                         │ Validate / Reject    │
                         │ unsafe or invalid    │
                         │ requests             │
                         └──────────┬───────────┘
                                    │
                                    ▼
                    ┌──────────────────────────────┐
                    │       Supervisor Agent       │
                    │                              │
                    │ Understand task & coordinate │
                    │ the required capabilities    │
                    └──────────────┬───────────────┘
                                   │
                     ┌─────────────┴─────────────┐
                     │                           │
                     ▼                           ▼
            ┌─────────────────┐         ┌─────────────────┐
            │  Agent / Logic  │         │   MCP Tools     │
            │                 │         │                 │
            │ Travel Planning │◄───────►│ Weather / Data  │
            │ Reasoning       │         │ External Tools  │
            └────────┬────────┘         └─────────────────┘
                     │
                     ▼
            ┌─────────────────────┐
            │   Draft Travel Plan │
            └──────────┬──────────┘
                       │
                       ▼
             ┌────────────────────┐
             │       HITL         │
             │ Human Approval     │
             └─────────┬──────────┘
                       │
             ┌─────────┴─────────┐
             │                   │
          Approved             Rejected
             │                   │
             ▼                   ▼
      ┌──────────────┐     ┌──────────────┐
      │ Final Plan   │     │ User Feedback│
      │ / Response   │     │ & Revision   │
      └──────────────┘     └──────┬───────┘
                                  │
                                  └──────► Workflow resumes
```

---

# 🎯 Project Objective

The goal of this project is to demonstrate how modern **agentic AI architectures** can be designed as a controlled workflow rather than simply calling an LLM once.

The system combines:

* **LangGraph** → workflow and state orchestration
* **Supervisor Agent** → coordinates the workflow
* **MCP** → exposes external capabilities/tools to the agent
* **Guardrails** → validates incoming requests
* **Human-in-the-Loop** → allows a human to review the generated plan
* **FastAPI** → exposes the system through REST APIs
* **Frontend UI** → provides an interactive travel-planning experience

This architecture is particularly useful for enterprise AI systems where AI-generated decisions should be **controlled, reviewable, and auditable**.

---

# ✨ Key Features

### 🧠 Multi-Agent Architecture

Uses LangGraph to model the application as a stateful graph rather than a simple sequential LLM chain.

The graph can coordinate different capabilities and maintain state throughout the planning process.

---

### 🎯 Supervisor-Based Orchestration

The Supervisor acts as the coordinator of the workflow.

Instead of every agent independently deciding what to do next, the supervisor controls the flow and delegates work to the appropriate capability.

Conceptually:

```text
User Request
     │
     ▼
 Supervisor
     │
     ├──► Travel Planning
     │
     ├──► External Information
     │
     └──► Validation / Decision
              │
              ▼
         Final Draft
```

This follows the supervisor pattern used in hierarchical multi-agent systems, where a central supervisor controls communication and task delegation.

---

# 🔌 MCP — Model Context Protocol

The project demonstrates integration with **MCP**, allowing external capabilities to be exposed as tools that can be consumed by the AI workflow.

The repository contains:

```text
custom_weather_mcp_server.py
```

which demonstrates a custom MCP server for weather-related functionality.

The MCP client functionality is implemented in:

```text
mcp_client.py
```

### Why MCP?

MCP provides a standardized way for AI applications to interact with external tools and data sources.

Instead of putting every integration directly inside the agent logic:

```text
Agent
  │
  ├── Weather API
  ├── Database
  ├── Search API
  └── Other services
```

MCP can provide a cleaner separation:

```text
                AI Application
                      │
                      ▼
                 MCP Client
                      │
                      ▼
                MCP Server
                      │
              ┌───────┴───────┐
              ▼               ▼
          Weather          Other Tools
```

This makes tool integrations more modular and reusable.

---

# 🛡️ Input Guardrails

Before processing a travel request, the system can validate the incoming user input.

The purpose of guardrails is to prevent inappropriate, invalid, or unsupported requests from entering the main agent workflow.

Conceptually:

```text
User Input
    │
    ▼
Guardrail
    │
    ├── Valid ───────► Agent Workflow
    │
    └── Invalid ─────► Reject / Ask for Correction
```

This is especially important in enterprise agentic systems because LLMs should not be treated as unrestricted decision-making components.

---

# 👨‍💼 Human-in-the-Loop (HITL)

One of the important features of this project is **Human-in-the-Loop approval**.

Instead of automatically returning or executing every generated plan:

```text
AI generates plan
       │
       ▼
Human reviews plan
       │
   ┌───┴────┐
   │        │
Approve   Reject
   │        │
   ▼        ▼
Final    Revision
```

The human can:

* Approve the generated plan
* Reject the plan
* Provide feedback
* Resume the workflow with additional instructions

This pattern is useful for applications where AI output requires human oversight before being accepted or executed.

---

# 🔄 End-to-End Workflow

A typical request follows this lifecycle:

```text
1. User submits travel request
             │
             ▼
2. FastAPI receives request
             │
             ▼
3. Input guardrails validate request
             │
             ▼
4. LangGraph workflow starts
             │
             ▼
5. Supervisor determines required actions
             │
             ▼
6. Agent interacts with MCP tools
             │
             ▼
7. Travel plan is generated
             │
             ▼
8. Workflow pauses for HITL approval
             │
       ┌─────┴─────┐
       ▼           ▼
    Approve      Reject
       │           │
       ▼           ▼
 Final Plan    Feedback
                   │
                   ▼
              Workflow resumes
```

---

# 📂 Project Structure

```text
Multi-Agent-System-using-LangGraph-MCP-Supervisor-Guardrails-HITL/
│
├── app.py
│
├── backend.py
│
├── mcp_client.py
│
├── custom_weather_mcp_server.py
│
├── requirements.txt
│
├── Dockerfile
│
├── .dockerignore
├── .gitignore
├── LICENSE
│
├── demo.excalidraw
│
├── templates/
│   └── ...
│
└── static/
    └── ...
```

### `app.py`

FastAPI application layer.

Responsible for:

* Starting the web application
* Exposing API endpoints
* Serving the frontend
* Handling travel-planning requests
* Handling HITL approval requests

---

### `backend.py`

Contains the core travel-planning and agent orchestration logic.

This is where the application connects the API layer with the LangGraph/MCP workflow.

---

### `mcp_client.py`

Contains helper functionality for communicating with MCP servers.

---

### `custom_weather_mcp_server.py`

Example MCP server exposing weather-related capabilities.

It demonstrates how an external capability can be implemented as an MCP server and consumed by the AI application.

---

### `templates/`

Frontend HTML templates.

---

### `static/`

Frontend assets such as JavaScript and CSS.

---

### `Dockerfile`

Containerization configuration for deploying the application.

---

# 🛠️ Technology Stack

| Technology          | Purpose                               |
| ------------------- | ------------------------------------- |
| Python              | Core programming language             |
| LangGraph           | Stateful agent workflow orchestration |
| LangChain           | LLM and tool integration              |
| MCP                 | External tool/context integration     |
| FastAPI             | Backend REST API                      |
| HTML/CSS/JavaScript | Frontend interface                    |
| Docker              | Application containerization          |
| LLM                 | Reasoning and generation              |
| HITL                | Human approval and intervention       |

---

# 🚀 Getting Started

## 1. Clone the repository

```bash
git clone https://github.com/entbappy/Multi-Agent-System-using-LangGraph-MCP-Supervisor-Guardrails-HITL.git

cd Multi-Agent-System-using-LangGraph-MCP-Supervisor-Guardrails-HITL
```

---

## 2. Create a virtual environment

### Windows

```powershell
python -m venv .venv

.venv\Scripts\Activate.ps1
```

### macOS / Linux

```bash
python3 -m venv .venv

source .venv/bin/activate
```

---

## 3. Install dependencies

```bash
pip install -r requirements.txt
```

---

# 🔐 Environment Variables

Create a `.env` file if the configured LLM or other integrations require API credentials.

For example:

```env
OPENAI_API_KEY=your_api_key
```

> Never commit API keys, passwords, tokens, or other secrets to GitHub.

---

# ▶️ Run the Application

### Option 1 — Python

```bash
python app.py
```

### Option 2 — Uvicorn

```bash
uvicorn app:app --reload --host 127.0.0.1 --port 8000
```

Open:

```text
http://127.0.0.1:8000
```

The application provides the TripMate travel-planning interface.

---

# 🔌 Run the MCP Server

The repository contains an example weather MCP server.

Start it separately:

```bash
python custom_weather_mcp_server.py
```

The application can then use the MCP server as an external tool provider.

---

# 📡 API Endpoints

## Health Check

```http
GET /health
```

Used to verify that the application is running.

---

## Create Travel Plan

```http
POST /api/travel
```

Example:

```json
{
  "message": "Plan a 5-day trip to Goa",
  "thread_id": "travel-session-001"
}
```

The `thread_id` allows the application to associate multiple interactions with the same workflow/thread.

---

## Approve Travel Plan

```http
POST /api/travel/approve
```

Example:

```json
{
  "thread_id": "travel-session-001",
  "approved": true,
  "feedback": ""
}
```

---

## Request Revision

The same approval endpoint can be used to reject a draft and provide feedback.

```json
{
  "thread_id": "travel-session-001",
  "approved": false,
  "feedback": "Reduce the budget and add more outdoor activities."
}
```

The workflow can then resume using the provided feedback.

---

# 🧩 Understanding the LangGraph Workflow

The important idea is that LangGraph represents the application as a **stateful graph**.

Instead of:

```text
Input → LLM → Output
```

the application follows a controlled workflow:

```text
                    ┌─────────────┐
                    │    Input    │
                    └──────┬──────┘
                           ▼
                    ┌─────────────┐
                    │ Guardrails  │
                    └──────┬──────┘
                           ▼
                    ┌─────────────┐
                    │ Supervisor  │
                    └──────┬──────┘
                           ▼
                    ┌─────────────┐
                    │   Agents /  │
                    │    Tools    │
                    └──────┬──────┘
                           ▼
                    ┌─────────────┐
                    │ Draft Plan  │
                    └──────┬──────┘
                           ▼
                    ┌─────────────┐
                    │    HITL     │
                    └──────┬──────┘
                           │
                    ┌──────┴──────┐
                    ▼             ▼
                 Approve        Reject
                    │             │
                    ▼             ▼
                  Final        Feedback
                                  │
                                  └──────► Resume
```

This is the major difference between a simple chatbot and a workflow-oriented agent system.

---

# 🔍 Why Use LangGraph?

LangGraph is useful when an AI application needs:

* Stateful workflows
* Conditional routing
* Multiple agents
* Tool calls
* Checkpoints
* Human intervention
* Workflow interruption and resumption
* More predictable control over agent execution

The supervisor pattern is particularly useful when different specialized capabilities need to be coordinated by a central decision-maker.

---

# 👤 Why HITL Matters

Fully autonomous agents can make incorrect decisions.

For higher-risk workflows, a better architecture is:

```text
AI
 │
 ├── Analyze
 ├── Plan
 └── Recommend
        │
        ▼
   Human Review
        │
   ┌────┴────┐
   ▼         ▼
Approve    Revise
```

This allows AI to perform the reasoning-heavy work while humans retain control over important decisions.

---

# 🐳 Docker

The repository also contains a `Dockerfile`, allowing the application to be packaged as a container.

Build:

```bash
docker build -t travel-agent .
```

Run:

```bash
docker run -p 8000:8000 travel-agent
```

Then access:

```text
http://localhost:8000
```

---

# 🧪 Development

The repository is primarily intended as a demonstration and learning project for:

* Multi-agent architecture
* LangGraph
* MCP
* Supervisor workflows
* Guardrails
* Human-in-the-loop systems
* FastAPI integration

Automated tests are not currently included in the repository.

---

# 📈 Possible Production Enhancements

For a production deployment, the following improvements could be added:

### Observability

Integrate:

* LangSmith
* OpenTelemetry
* Application logging
* Agent execution tracing

### Persistence

Add:

* PostgreSQL
* Redis
* LangGraph checkpointers

for durable state and multi-user sessions.

### Security

Add:

* Authentication
* Authorization
* API rate limiting
* Input/output validation
* Secret management

### Agent Reliability

Add:

* Retry policies
* Timeouts
* Tool failure handling
* Structured outputs
* Maximum iteration limits
* Circuit breakers

### MCP Security

External MCP tools should be:

* Authenticated
* Permission-controlled
* Validated
* Rate-limited
* Audited

---

# 🎓 What This Project Demonstrates

This project demonstrates several important concepts in modern **Agentic AI**:

```text
                 ┌──────────────────┐
                 │   Agentic AI      │
                 └────────┬─────────┘
                          │
       ┌──────────────────┼──────────────────┐
       ▼                  ▼                  ▼
  LangGraph             MCP             Supervisor
       │                  │                  │
       ▼                  ▼                  ▼
 Stateful Workflow   Tool Integration   Agent Routing
       │
       ▼
     HITL
       │
       ▼
 Human Oversight
       │
       ▼
  Guardrails
```

Together, these components provide a foundation for building AI systems that are not only capable of reasoning, but also **structured, controllable, and reviewable**.

---

# 🌟 Key Takeaway

The core idea of this project is:

> **LLMs provide intelligence; LangGraph provides workflow control; MCP provides tool connectivity; Guardrails provide safety; and HITL provides human oversight.**

That combination forms a practical architecture for building more reliable enterprise-grade agentic AI applications.

---

# 📚 References

* [LangGraph](https://github.com/langchain-ai/langgraph)
* [LangChain](https://github.com/langchain-ai/langchain)
* [Model Context Protocol](https://modelcontextprotocol.io/)
* [FastAPI](https://fastapi.tiangolo.com/)

---

# 📄 License

This project follows the license included in the repository.

---

## 🙌 Acknowledgements

Inspired by the growing ecosystem around:

* LangChain
* LangGraph
* Model Context Protocol
* Agentic AI
* Human-in-the-Loop AI systems

If you find this project useful, consider ⭐ starring the repository and exploring the architecture further.
