# LangGraph Multi-Agent Research Assistant

A simple multi-agent orchestration demo built with **LangGraph**, served through **FastAPI**, with a lightweight HTML/JS frontend. Give it a topic, and a supervisor agent coordinates a researcher agent (web search) and a writer agent (summary generation) to produce a written article — automatically, in one request.

## How it works

1. **Supervisor** — decides what happens next: send the task to the researcher, the writer, or finish.
2. **Researcher** — searches the web (via Tavily) for information on the given topic.
3. **Writer** — takes the researcher's findings and writes a clean, structured summary.
4. The supervisor loops after each agent runs, until the writer has produced a final article, then the graph ends.

This follows the **supervisor (orchestrator-worker) pattern** for multi-agent systems in LangGraph.

## Tech stack

- **LangGraph** — agent orchestration / state graph
- **LangChain + Groq** — LLM calls (`openai/gpt-oss-120b`)
- **Tavily** — web search tool for the researcher agent
- **FastAPI** — backend API, also serves the frontend
- **HTML/JS + marked.js** — minimal frontend, renders the LLM's markdown output as formatted HTML
- **Docker** — containerized for deployment
- **Render** — hosting

## Project structure

```
.
├── main.py            # LangGraph agents + FastAPI app
├── index.html          # Frontend UI
├── requirements.txt    # Python dependencies
├── Dockerfile           # Container build instructions
├── .dockerignore
└── .gitignore
```

## Setup

### 1. Clone the repo

```bash
git clone https://github.com/bharathp95/Langgraph_MultiAgent_Orch_FastAPI.git
cd Langgraph_MultiAgent_Orch_FastAPI
```

### 2. Create a `.env` file

```
GROQ_API_KEY=your_groq_api_key
TAVILY_API_KEY=your_tavily_api_key
```

### 3. Install dependencies

```bash
python -m venv venv
source venv/bin/activate   # on Windows: venv\Scripts\activate
pip install -r requirements.txt
```

### 4. Run locally

```bash
uvicorn main:app --reload
```

Open `http://127.0.0.1:8000` in your browser.

## Running with Docker

```bash
docker build -t langgraph-fastapi .
docker run -p 8000:8000 --env-file .env langgraph-fastapi
```

## API

### `POST /research`

**Request body:**
```json
{ "topic": "benefits of solar energy" }
```

**Response:**
```json
{
  "topic": "benefits of solar energy",
  "article": "## Benefits of Solar Energy\n\n...",
  "steps": [
    { "agent": "researcher", "content": "..." },
    { "agent": "writer", "content": "..." }
  ]
}
```

## Deployment

Deployed on [Render](https://render.com) using the included `Dockerfile`. 

## Notes / limitations

- Uses Groq's free tier, which has a per-minute token limit — large or repeated requests may hit rate limits.
- Each request uses a fresh `thread_id`, so conversation memory is not persisted between requests.
