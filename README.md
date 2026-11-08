# Digisinc AI Chatbot

![Python](https://img.shields.io/badge/Python-3.11-3776AB?style=flat-square&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=flat-square&logo=fastapi&logoColor=white)
![Pinecone](https://img.shields.io/badge/Pinecone-Vector%20DB-000000?style=flat-square)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)

A RAG customer-support chatbot for **Digisinc**, a digital marketing agency. It answers visitor questions about
the agency's services, pricing and process using only the company's own documents, so the replies stay
accurate and on-brand.

## How it works

```
Agency profile (PDF / Markdown)
   └─► ingest.py: split into chunks → embed → upload to Pinecone

Visitor question
   └─► POST /chat
         └─► embed question → retrieve the most relevant chunks from Pinecone
               └─► Llama 3.1 8B (via Groq) answers using only those chunks
```

## Features

- **Grounded answers** from the agency's knowledge base, not the model's general knowledge
- **Fast responses** with Groq-hosted Llama 3.1 8B
- **Managed vector search** with Pinecone and Sentence-Transformers embeddings
- **Ready to deploy:** Dockerfile and Procfile included (runs on Hugging Face Spaces, port 7860)
- **CORS enabled** so the website front end can call it directly

## API

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/health` | Health check |
| `POST` | `/chat` | Send a question, get the chatbot's answer |

## Getting started

```bash
pip install -r requirements.txt
```

`.env`:

```env
GROQ_API_KEY=your_groq_key
PINECONE_API_KEY=your_pinecone_key
```

1. Load the knowledge base into Pinecone (run once, or whenever the documents change):
   ```bash
   cd fast-api
   python ingest.py
   ```
2. Start the API:
   ```bash
   uvicorn app:app --reload --port 7860
   ```

### Docker

```bash
docker build -t digisinc-chatbot .
docker run -p 7860:7860 --env-file .env digisinc-chatbot
```

## Project structure

```
data/Agency_Profile.md    Agency knowledge base
fast-api/app.py           Production API (Pinecone + Groq)
fast-api/ingest.py        Loads documents into Pinecone
fast-api/chatbot.py       Local prototype using Chroma
Dockerfile, Procfile      Deployment
```

## Tech stack

FastAPI · LangChain · Groq · Pinecone · Hugging Face embeddings · Docker
