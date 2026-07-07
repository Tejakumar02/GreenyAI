# 🍃 GreenyAI

GreenyAI is a full-featured, dark-themed AI chat assistant built with **Streamlit**. It combines fast LLM chat (via **Groq**), document Q&A (**RAG over PDFs**), image understanding (**Gemini Vision**), live web search (**Tavily**), and persistent multi-chat history (**Supabase**) — all wrapped in a custom ChatGPT-style UI.

![Python](https://img.shields.io/badge/Python-3.9%2B-blue)
![Streamlit](https://img.shields.io/badge/Streamlit-App-FF4B4B)
![License](https://img.shields.io/badge/License-MIT-green)

---

## ✨ Features

- **💬 Multi-chat sessions** — create, switch between, rename (auto-titled), and delete conversations, all persisted to Supabase so history survives page reloads.
- **⚡ Fast LLM responses** — powered by Groq (`llama-3.1-8b-instant` / `llama-3.3-70b-versatile`), with token streaming for a live-typing effect.
- **📄 PDF RAG (Retrieval-Augmented Generation)** — upload a PDF, and GreenyAI chunks and embeds it (`all-MiniLM-L6-v2`) to answer questions using only the relevant sections instead of the whole document.
- **🖼️ Image understanding (Vision)** — upload an image and ask questions about it, answered by Gemini (`gemini-2.5-flash`), with support for combining image context with PDF or web context.
- **🌐 Live web search** — toggle web search on manually, or let GreenyAI auto-detect time-sensitive queries (e.g. "latest", "today", "current price") and pull in fresh results via Tavily, complete with cited sources.
- **🕒 Always-accurate date/time** — the system prompt is injected with the real current date/time so the model never guesses or relies on training data for "what's today's date."
- **🔐 Password-gated Admin Panel** — tweak the system prompt, temperature, top-p, and max response tokens without redeploying.
- **⬇️ Export chat** — download any conversation as a plain `.txt` file.
- **🎨 Custom UI** — a fully restyled Streamlit interface (dark green theme, pill-style buttons, custom chat bubbles, animated model/status indicators).

---

##  How It Works

| Situation | What happens |
|---|---|
| Plain question, no attachments | Sent straight to Groq with the system prompt + chat history |
| PDF uploaded & selected | Question is embedded, top matching chunks are retrieved, and only that context is sent to Groq |
| Image uploaded & selected | Image + question (+ optional PDF/web context) sent to Gemini Vision |
| Web search toggle ON (or auto-detected) | Tavily fetches live results, which are merged into the prompt sent to Groq/Gemini, and sources are appended to the reply |

> Note: PDF mode and Image mode are mutually exclusive per message — selecting one automatically turns the other off.

---

## 🛠️ Tech Stack

| Purpose | Library / Service |
|---|---|
| UI framework | [Streamlit](https://streamlit.io/) |
| Chat LLM | [Groq](https://groq.com/) (Llama 3.1 / 3.3) |
| Vision LLM | [Google Gemini](https://ai.google.dev/) (`google-genai`) |
| Web search | [Tavily](https://tavily.com/) |
| Embeddings | [Sentence-Transformers](https://www.sbert.net/) (`all-MiniLM-L6-v2`) |
| PDF parsing | [PyMuPDF](https://pymupdf.readthedocs.io/) (`fitz`) |
| Persistence | [Supabase](https://supabase.com/) |
| Numerical ops | NumPy |

---

## 🚀 Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/<your-username>/greenyai.git
cd greenyai
```

### 2. Create a virtual environment (recommended)

```bash
python -m venv venv
source venv/bin/activate   # Windows: venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install streamlit groq pymupdf numpy sentence-transformers supabase tavily-python google-genai
```

> `tavily-python` and `google-genai` are optional — the app degrades gracefully (web search / vision features simply turn off) if either isn't installed or its API key is missing.

### 4. Set up Supabase

Create a table named `messages` with (at minimum) the following columns:

| Column | Type |
|---|---|
| `id` | `int8` (primary key, auto-increment) |
| `user_id` | `text` |
| `chat_id` | `text` (nullable) |
| `role` | `text` |
| `content` | `text` |

### 5. Configure secrets

Create `.streamlit/secrets.toml` in the project root:

```toml
SUPABASE_URL = "https://your-project.supabase.co"
SUPABASE_KEY = "your-supabase-anon-or-service-key"

GROQ_API_KEY = "your-groq-api-key"

# Optional — enables the ⚙️ Admin settings panel
ADMIN_PASSWORD = "your-admin-password"

# Optional — enables 🌐 live web search
TAVILY_API_KEY = "your-tavily-api-key"

# Optional — enables 🖼️ image / vision support
GEMINI_API_KEY = "your-gemini-api-key"
```

### 6. Run the app

```bash
streamlit run app.py
```

Then open the URL Streamlit prints (usually `http://localhost:8501`).

---

## 📂 Project Structure

```
greenyai/
├── app.py                  # Main Streamlit application (this file)
├── .streamlit/
|__  config.toml
│   └── secrets.toml        # API keys & config (not committed)
└── README.md
├── requirements.txt
```

---

## ⚙️ Admin Settings

Click **⚙️ Admin settings** in the sidebar and enter the `ADMIN_PASSWORD` to unlock:

- Custom **system prompt**
- **Temperature** (0.0–2.0)
- **Top-p** (0.05–1.0)
- **Max response tokens** (128–4096)
- Clear the currently active document from memory

---

## 🗺️ Roadmap Ideas

- [ ] Support additional LLM providers (OpenAI, Anthropic, etc.)
- [ ] Multi-document RAG (search across several PDFs at once)
- [ ] User authentication (replace anonymous `user_id`)
- [ ] Streaming support for Gemini vision responses

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).

---

## 🙏 Acknowledgements

- [Groq](https://groq.com/) for blazing-fast LLM inference
- [Google Gemini](https://ai.google.dev/) for vision capabilities
- [Tavily](https://tavily.com/) for web search
- [Supabase](https://supabase.com/) for persistence
- [Sentence-Transformers](https://www.sbert.net/) for embeddings