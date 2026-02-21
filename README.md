# 🤖 RAG Workflow for Company Documents — n8n + Ollama + Pinecone

*Created by [Mert Güneş](http://mertgunes.com/)* 🚀

[![n8n](https://img.shields.io/badge/n8n-workflow-orange?logo=n8n)](https://n8n.io)
[![Ollama](https://img.shields.io/badge/Ollama-local%20LLM-black?logo=ollama)](https://ollama.com)
[![Pinecone](https://img.shields.io/badge/Pinecone-vector%20store-blue)](https://www.pinecone.io)
[![Google Drive](https://img.shields.io/badge/Google%20Drive-integration-4285F4?logo=google-drive)](https://drive.google.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

---

A fully **local, privacy-first RAG (Retrieval-Augmented Generation)** pipeline built with **n8n**, **Ollama**, and **Pinecone**. 

Upload company documents to Google Drive → they are automatically indexed into Pinecone → chat with them using a local LLM — **no data ever leaves your machine** (except for Pinecone storage).

---

## ✨ Features

- 📁 **Auto-sync with Google Drive** — Watches a folder for new/updated files
- 🧠 **Local LLM via Ollama** — Uses `llama3.1` for chat, `nomic-embed-text` for embeddings
- 🔍 **Pinecone Vector Store** — Fast semantic search over your documents
- 💬 **Chat Interface** — Built-in n8n chat trigger for instant Q&A
- 🪟 **Window Buffer Memory** — Remembers recent conversation context
- ✂️ **Smart Text Chunking** — Recursive character splitter (1000 chars / 200 overlap)
- 🔒 **Privacy-first** — LLM runs entirely locally

---

## 🏗️ Architecture

```
Google Drive (watch folder)
        │
        ├─ File Created  ──┐
        └─ File Updated  ──┴─▶  Download File  ──▶  Pinecone (INSERT)
                                                          │
                                                    Ollama Embeddings
                                                    (nomic-embed-text)

Chat Trigger  ──▶  AI Agent  ──▶  Vector Store Tool  ──▶  Pinecone (RETRIEVE)
                      │                                          │
               Window Memory                             Ollama Embeddings
               Ollama LLM                                Ollama Chat Model
               (llama3.1)                                   (llama3.1)
```

---

## 🛠️ Tech Stack

| Component | Tool |
|-----------|------|
| Workflow Automation | [n8n](https://n8n.io) |
| Local LLM | [Ollama](https://ollama.com) — `llama3.1:latest` |
| Embeddings | [Ollama](https://ollama.com) — `nomic-embed-text:latest` |
| Vector Database | [Pinecone](https://www.pinecone.io) |
| Document Source | Google Drive |

---

## 🚀 Setup & Usage

### Prerequisites

- [n8n](https://docs.n8n.io/getting-started/installation/) (self-hosted or cloud)
- [Ollama](https://ollama.com/download) installed and running locally
- A [Pinecone](https://www.pinecone.io) account (free tier works)
- Google account with Drive API credentials

### Step 1 — Pull Ollama Models

```bash
ollama pull llama3.1
ollama pull nomic-embed-text
```

### Step 2 — Set Up Pinecone

1. Create a free account at [pinecone.io](https://www.pinecone.io)
2. Create a new index named **`company-documents`** (or any name you prefer)
3. Set dimension to **`768`** (matches `nomic-embed-text`)
4. Copy your **API Key**

### Step 3 — Configure n8n Credentials

In n8n, add the following credentials:

| Credential | Details |
|---|---|
| **Ollama** | Base URL: `http://localhost:11434` |
| **Pinecone** | Your API Key |
| **Google Drive** | OAuth2 credentials (see [n8n docs](https://docs.n8n.io/integrations/builtin/credentials/google/)) |

### Step 4 — Import the Workflow

1. Open your n8n instance
2. Go to **Workflows → Import**
3. Upload `workflow.json`
4. Update the following in the workflow:
   - **Google Drive nodes** → set your folder ID to watch
   - **Pinecone nodes** → select your index name
5. **Activate** the workflow

### Step 5 — Add Documents & Chat!

1. Drop any document (PDF, DOCX, TXT…) into your watched Google Drive folder
2. The workflow auto-indexes it into Pinecone
3. Open the n8n **Chat** interface and start asking questions!

---

## ⚙️ Configuration

### Changing the LLM model

In the **Ollama Chat Model** nodes, change `llama3.1:latest` to any model you have pulled:

```bash
ollama pull mistral
ollama pull gemma3
ollama pull qwen2.5
```

### Chunk Size

In the **Recursive Character Text Splitter** node:

- `chunkSize`: `1000` — characters per chunk
- `chunkOverlap`: `200` — overlap between chunks

Increase chunk size for longer context, decrease for faster retrieval.

### System Prompt

The AI Agent's system prompt can be customized in the **AI Agent** node:

> *"You are an expert academic assistant in the field of Management Information Systems (MIS). Answer questions in Turkish, clearly and academically, by scanning the user's course notes. Do not make up information if it is not in the documents."*

Modify this to match your use case, language, and domain.

---

## 📁 Project Structure

```
n8n-rag-workflow-ollama/
├── workflow.json   # n8n workflow (import this)
├── README.md       # This file
└── LICENSE         # MIT License
```

---

## 🤝 Contributing

Contributions, issues and feature requests are welcome!
Feel free to [open an issue](https://github.com/gunesmert67/n8n-rag-workflow-ollama/issues) or submit a pull request.

---

## 📄 License

Distributed under the MIT License. See [LICENSE](LICENSE) for more information.

---

## 👤 Author

**Mert Güneş**

- 🌐 Portfolio: [mertgunes.com](http://mertgunes.com/)
- 💻 GitHub: [@gunesmert67](https://github.com/gunesmert67)
