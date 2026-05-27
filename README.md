# Knowledge Base — AI System

A production-ready, self-hosted AI knowledge base built on **Retrieval-Augmented Generation (RAG)**. Upload your documents, ask questions, and get grounded answers backed strictly by your uploaded content — powered by NVIDIA NIM and Llama 3.

---

## How It Works

```
Upload PDF/TXT → Extract Text → Chunk → Embed → Vector Store
       Query → Semantic Search → Retrieve Chunks → LLM → Answer
```

The system only answers from documents you upload. If the answer isn't in your docs, it says so.

---

## Features

- **Ask AI** — Chat interface with streaming answers grounded in your documents
- **Documents** — Upload and manage PDF and text files
- **Knowledge Graph** — Interactive visual graph of concepts, entities, and relationships extracted from your docs
- **Wiki** — Auto-saved Q&A pairs from your conversations
- **Dashboard** — Overview of indexed documents, chunks, and stats
- **Settings** — Tune search parameters (Top-K, similarity threshold, model)

---

## Requirements

- Node.js **≥ 18.0.0**
- An **NVIDIA NIM API key** (free tier available at [build.nvidia.com](https://build.nvidia.com))

---

## Setup

**1. Install dependencies**
```bash
npm install
```

**2. Configure your API key**

Edit the `.env` file in the project root:
```env
NVIDIA_API_KEY=your_nvidia_api_key_here
NVIDIA_MODEL=meta/llama-3.3-70b-instruct
PORT=3000
NODE_ENV=development
```

**3. Start the server**
```bash
# Production
npm start

# Development (auto-restarts on file changes)
npm run dev
```

**4. Open in browser**
```
http://localhost:3000
```

---

## Project Structure

```
open_kb_9th/
├── server.js          # Express server — all API routes and RAG logic
├── documents.js       # Document registry
├── .env               # API keys and config (do not commit)
├── data/
│   └── vectors.json   # Persisted vector store (auto-created)
├── lib/
│   ├── chunker.js     # Text chunking with overlap
│   ├── embedder.js    # Embedding via NVIDIA NIM
│   ├── pdfParser.js   # PDF text extraction
│   └── vectorStore.js # In-memory vector store with cosine similarity
└── public/
    ├── index.html     # Single-page app shell
    ├── app.js         # Frontend logic (chat, graph, documents, wiki)
    └── style.css      # UI styles
```

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/upload` | Upload a PDF or text file |
| `GET` | `/api/documents` | List all indexed documents |
| `DELETE` | `/api/documents/:id` | Delete a document |
| `POST` | `/api/chat` | Stream a RAG answer (SSE) |
| `GET` | `/api/graph` | Get knowledge graph nodes and edges |
| `GET` | `/api/stats` | Document and chunk counts |
| `GET` | `/api/models` | List available models |
| `GET` | `/api/wiki` | Get saved wiki entries |

---

## Uploading Documents

1. Go to the **Documents** tab
2. Click **Upload** and select a `.pdf` or `.txt` file
3. The system extracts text, splits it into overlapping chunks, embeds each chunk, and stores the vectors
4. Once indexed, the document is immediately available for questions

---

## Asking Questions

1. Go to the **Ask AI** tab
2. Type your question and press Enter or click Send
3. The system finds the most relevant chunks from your documents and generates a grounded answer
4. If the answer is not in your documents, the AI responds: *"I cannot find this answer in the uploaded documents."*
5. Sources used are shown below each answer in a collapsible panel

---

## Knowledge Graph

The **Graph** tab shows a dark-themed interactive force-directed graph of concepts, entities, documents, and processes extracted from your indexed content. You can:

- **Drag** nodes to rearrange
- **Scroll** to zoom in/out
- **Click** a node to see its details and connections
- Use **+/-** buttons to zoom, and the reset button to recenter

Node colors by type: Indigo = Document, Cyan = Concept, Emerald = Entity, Orange = Process, Pink = Technology.

---

## Settings

| Setting | Description |
|---------|-------------|
| **Top-K** | Number of chunks retrieved per query (default: 5) |
| **Similarity Threshold** | Minimum relevance score to include a chunk (0–1) |
| **Model** | Locked to Llama 3.1 8B · Fast |

---

## Dependencies

| Package | Purpose |
|---------|---------|
| `express` | HTTP server and routing |
| `multer` | File upload handling |
| `pdf-parse` | PDF text extraction |
| `node-fetch` | NVIDIA NIM API calls |
| `dotenv` | Environment variable loading |
| `nodemon` *(dev)* | Auto-restart during development |

---

## Notes

- Vector data is persisted to `data/vectors.json` automatically — your indexed documents survive server restarts
- The `.env` file contains your API key — never commit it to version control (it is already in `.gitignore`)
- The system uses **keyword + semantic hybrid search** with a full-text fallback so queries still work if embedding is unavailable
- Answers are auto-saved to the Wiki only when the AI finds a valid answer in the documents
