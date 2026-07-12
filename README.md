# LangGraph Tool-Calling Agent

A minimal example of LangGraph's prebuilt ReAct agent: a chat UI where the LLM decides on its own whether to answer directly or call a tool (weather lookup, multiplication), executes it, and folds the result into its reply.

## What It Does

- Chat interface (Streamlit) backed by a LangGraph `create_react_agent` — LangGraph's built-in reasoning loop that lets the LLM decide, per message, whether it needs a tool or can answer directly.
- Two example tools: `get_weather` (a small hardcoded lookup for a couple of cities, to keep the demo dependency-free) and `multiply`.
- Runs on Groq's hosted `openai/gpt-oss-120b` model via `langchain-groq`.

## End-to-End Flow

```
 User asks a question in the Streamlit chat
        │
        ▼
 create_react_agent (LangGraph's prebuilt ReAct loop)
        │
        ▼
 LLM reads the message + available tool descriptions, decides:
   - answer directly, or
   - call get_weather(city) / multiply(a, b)
        │
        ├── tool called -> result returned to the LLM as context
        │
        ▼
 LLM produces the final answer using any tool output
        │
        ▼
 Response rendered in the chat UI, added to session history
```

This is deliberately the smallest possible LangGraph agent — one file, two trivial tools — as a clear reference for how `create_react_agent` wires an LLM to tools without hand-writing the reasoning loop yourself (contrast with a hand-rolled decision loop, which has to parse the LLM's tool choice manually).

## Tech Stack

| Layer | Technology |
|---|---|
| UI | Streamlit |
| Agent loop | LangGraph (`create_react_agent`) |
| LLM | Groq — `openai/gpt-oss-120b` (via `langchain-groq`) |

## Setup

```bash
pip install -r requirements.txt
```

Create a `.env` file:
```env
GROQ_API_KEY=your_groq_api_key_here
```
Get a key from [console.groq.com](https://console.groq.com/keys).

## Run

```bash
streamlit run agent.py
```
Open the URL Streamlit prints (typically `http://localhost:8501`), then try *"What's the weather in London?"* or *"What is 12 multiplied by 8?"*.

## Security Note

A previous commit had a live Groq API key hardcoded directly in `agent.py`. It's been removed — the app now requires `GROQ_API_KEY` in `.env` (gitignored) and stops with a clear error if it's missing. **If you're reusing this repo, rotate that key**, since anything committed to a public repo's history should be treated as compromised even after removal.
