# Agentic AI Assistant
A production-ready AI agent built with LangGraph, FastAPI, and Google Gemini 2.5 that combines conversational AI with long-term memory, RAG document search, real-time web search, and a streaming web interface — fully containerized and deployable to AWS.

---

## What This Project Does

Most AI chatbots are stateless — they forget everything after each session and can only answer from their training data. This project goes further by building a **full agentic system** that remembers users, reads your documents, searches the web in real time, and reasons about which tool to use automatically.

```
User sends message
        ↓
LangGraph Agent decides which tool to use
        ↓
        ├── Calculator      — math operations
        ├── Web Search      — real-time Tavily search
        ├── RAG             — query uploaded documents
        ├── Memory Save     — store user information
        └── Memory Recall   — retrieve stored information
        ↓
Gemini 2.5 generates response
        ↓
Streaming token-by-token to browser via SSE
        ↓
Conversation saved to SQLite — persistent across sessions
```

---

## Demo

**Automatic tool detection — agent decides which tool to call:**

```
"Calculate 46 * 8 + 200"           → Calculator tool
"What's the latest AI news today?" → Web Search (Tavily)
"Summarize my uploaded PDF"        → RAG retrieval
"Remember my name is Saqib"        → Memory Save
"What's my name?"                  → Memory Recall
```

No manual tool selection. The agent reads the message and decides automatically.

---

## Architecture

```
┌─────────────────────────────────────────────────────┐
│               AGENTIC AI ASSISTANT                  │
├─────────────────────────────────────────────────────┤
│                                                     │
│  Web Interface (HTML/CSS/JS)                        │
│  Streamlit Interface (alternative)                  │
│           ↓                                         │
│  FastAPI Backend                                    │
│  • REST API endpoints                               │
│  • SSE streaming                                    │
│  • File upload handling                             │
│           ↓                                         │
│  LangGraph Agent                                    │
│  • Gemini 2.5 LLM integration                      │
│  • Tool binding and execution                       │
│  • State management                                 │
│  • Conversation flow control                        │
│           ↓                                         │
│  ┌────────┬──────────┬──────────┬────────┐         │
│  │ Tools  │   RAG    │  Memory  │   DB   │         │
│  │        │          │          │        │         │
│  │ Calc   │ ChromaDB │ Vector   │ SQLite │         │
│  │ Search │ Chunking │ Storage  │ Msgs   │         │
│  │ Docs   │ Embeds   │ Retrieval│ History│         │
│  └────────┴──────────┴──────────┴────────┘         │
└─────────────────────────────────────────────────────┘
```

---

## Features

### Core Capabilities

| Feature | Description |
|---------|-------------|
| Multi-Model Support | Gemini 2.5 Flash, Pro, Lite, 1.5 Flash/Pro |
| Conversational Memory | Save and recall user information across sessions |
| RAG | Upload PDF, DOCX, TXT, CSV — query them in chat |
| Web Search | Real-time search with Tavily API |
| Calculator | Mathematical operations from natural language |
| Streaming Responses | Token-by-token via SSE — no waiting |
| Multi-Thread Conversations | Multiple independent conversation threads |
| Conversation History | Persistent chat history across sessions |
| Automatic Tool Selection | Agent decides which tool to call |

### User Interface
- Dark theme — modern, clean design
- Sidebar — conversation history and management
- File upload — drag-and-drop document upload
- Model selection — switch Gemini models on the fly
- Speech-to-Text — voice dictation support
- Tool progress indicators — visual feedback during tool execution
- Mobile responsive design

---

## Tech Stack

![Python](https://img.shields.io/badge/Python-3.11+-blue?style=flat-square)
![FastAPI](https://img.shields.io/badge/FastAPI-Backend-green?style=flat-square)
![LangGraph](https://img.shields.io/badge/LangGraph-Agent-purple?style=flat-square)
![Gemini](https://img.shields.io/badge/Google-Gemini%202.5-orange?style=flat-square)
![ChromaDB](https://img.shields.io/badge/ChromaDB-Vector%20DB-red?style=flat-square)
![Docker](https://img.shields.io/badge/Docker-AWS-black?style=flat-square)

**Backend:** FastAPI · Python 3.11+ · LangGraph · LangChain · SQLAlchemy · SQLite

**AI & ML:** Google Gemini 2.5 · sentence-transformers · ChromaDB · Tavily Search

**Frontend:** HTML5/CSS3 · Vanilla JavaScript · Streamlit · SSE Streaming

**DevOps:** Docker · AWS ECR · AWS EC2 · GitHub Actions CI/CD

---

## Project Structure

```
agentic-ai-assistant/
│
├── app.py                        # FastAPI entry point
├── agent/
│   ├── graph.py                  # LangGraph agent workflow
│   ├── tools.py                  # Calculator, search, memory, RAG tools
│   ├── memory.py                 # Long-term memory management
│   └── rag.py                    # Document retrieval pipeline
├── models/                       # SQLAlchemy ORM models
│   ├── conversation.py
│   └── message.py
├── routers/                      # FastAPI endpoint handlers
│   ├── chat.py                   # Streaming chat endpoint
│   ├── upload.py                 # Document upload
│   └── history.py                # Conversation history
├── services/
│   ├── gemini_service.py         # Gemini API integration
│   ├── chroma_service.py         # ChromaDB vector store
│   └── tavily_service.py         # Web search integration
├── static/                       # CSS and JavaScript
├── templates/                    # HTML templates
├── streamlit_app.py              # Alternative Streamlit UI
├── Dockerfile                    # Container definition
├── docker-compose.yml            # Multi-container setup
├── .github/workflows/            # CI/CD pipeline
│   └── deploy.yml
├── requirements.txt
├── .env.example
└── README.md
```

---

## Getting Started

### 1. Clone the repo
```bash
git clone https://github.com/Saqib00712/Agentic-AI-Assistant.git
cd Agentic-AI-Assistant
```

### 2. Create virtual environment
```bash
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

### 4. Set up environment variables
```bash
cp .env.example .env
```
Edit `.env`:
```
GOOGLE_API_KEY=your_gemini_api_key_here
TAVILY_API_KEY=your_tavily_api_key_here
SECRET_KEY=any-random-secret-key
DEFAULT_MODEL=gemini-2.5-flash
DATABASE_URL=sqlite:///data/chatbot_memory.db
LANGSMITH_API_KEY=your_langsmith_key     # optional
```

### 5. Run the application
```bash
python app.py
```

### 6. Open in browser
```
Web UI:   http://localhost:8080
API Docs: http://localhost:8080/docs
```

### Docker (alternative)
```bash
docker build -t agentic-ai-assistant .
docker run -d -p 8080:8080 \
  -e GOOGLE_API_KEY="your_key" \
  -e TAVILY_API_KEY="your_key" \
  agentic-ai-assistant
```

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/` | Web interface |
| POST | `/chat/stream` | Streaming chat response |
| POST | `/upload` | Upload document for RAG |
| GET | `/history/{thread_id}` | Get conversation history |
| GET | `/conversations` | List all conversations |
| DELETE | `/conversations/{thread_id}` | Delete conversation |
| GET | `/docs` | Swagger API documentation |

---

## Tools Available

| Tool | Example Trigger |
|------|----------------|
| Calculator | "Calculate 15 * 23 + 47" |
| Web Search | "What's the latest AI news?" |
| RAG — Documents | "Summarize my uploaded PDF" |
| Memory Save | "Remember my name is Saqib" |
| Memory Recall | "What's my name?" |

### Adding a Custom Tool
```python
from langchain_core.tools import tool

@tool
def custom_tool(param: str) -> str:
    """Description of what the tool does — agent reads this to decide when to use it"""
    return result

# Add to the tools list in agent/tools.py
tools = [calculator, web_search, rag_search, memory_save, memory_recall, custom_tool]
```

---

## Key Concepts Covered

- **LangGraph agent** — stateful graph-based agent with conditional tool routing
- **Tool binding** — attaching tools to Gemini LLM with automatic selection
- **RAG pipeline** — document upload, chunking, embedding, ChromaDB retrieval
- **Long-term memory** — saving and recalling user information across sessions
- **SSE streaming** — token-by-token response streaming without WebSockets
- **Multi-thread conversations** — independent conversation state per thread ID
- **FastAPI + SQLAlchemy** — async REST API with persistent conversation storage
- **Docker + AWS deployment** — containerized production deployment with CI/CD
- **LangSmith observability** — tracing every agent step in production

---

## Deployment

### AWS Deployment
```bash
# Build and push to ECR
docker build -t agentic-ai-assistant .
aws ecr get-login-password | docker login --username AWS --password-stdin $ECR_URI
docker tag agentic-ai-assistant:latest $ECR_URI/agentic-ai-assistant:latest
docker push $ECR_URI/agentic-ai-assistant:latest

# Deploy to EC2 via GitHub Actions
# Push to main branch → automatic build and deploy
```

### CI/CD Pipeline
GitHub Actions automatically:
1. Runs tests on every pull request
2. Builds Docker image on merge to main
3. Pushes to AWS ECR
4. Deploys to EC2

---

## Related Certifications

Built applying skills from the IBM **Building AI Agents and Agentic Workflows Specialization** and **Agentic AI with LangGraph, CrewAI, AutoGen and BeeAI** on Coursera.

[![IBM Badge](https://img.shields.io/badge/IBM-AI%20Agents%20Specialization-blue?style=flat-square)](https://www.credly.com/users/muhammad-saqib.361f9b8c)
[![IBM Badge](https://img.shields.io/badge/IBM-LangGraph%20%26%20Agentic%20AI-blue?style=flat-square)](https://www.credly.com/users/muhammad-saqib.361f9b8c)

---

## Author

**Muhammad Saqib**
- GitHub: [@Saqib00712](https://github.com/Saqib00712)
- LinkedIn: [muhammad-saqib](https://www.linkedin.com/in/muhammad-saqib-68b9b3374/)
- Email: saqibkhosa649@gmail.com
- Credly: [15x IBM Certified](https://www.credly.com/users/muhammad-saqib.361f9b8c)
