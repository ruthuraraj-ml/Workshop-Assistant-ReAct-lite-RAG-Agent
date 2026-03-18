# 🤖 Workshop Assistant — ReAct-lite RAG Agent

A lightweight AI agent that answers questions grounded in workshop materials (PDFs & Jupyter notebooks), powered by **Google Gemini** and a **local FAISS vector index**. Built with a heuristic-based ReAct-style decision loop to minimize API calls and avoid hallucination.

---

## 🧠 How It Works

```
User Question
     │
     ▼
Local Embedding (all-MiniLM-L6-v2)
     │
     ▼
FAISS Vector Search → Relevance Score
     │
     ├─ Score < Threshold ──► Grounded Answer (RAG + Gemini)
     │
     └─ Score ≥ Threshold ──► General Answer (Gemini only, clearly labelled)
```

**Key design choices:**
- **Local embeddings** (SentenceTransformers) — zero API calls for retrieval
- **FAISS flat index** — instant similarity search, no cloud dependency
- **Heuristic ReAct** — distance threshold replaces an LLM routing call, saving cost
- **Single Gemini call per question** — grounded or general, never both
- **Transparent labelling** — UI clearly signals whether answer is grounded or general

---

## 📁 Project Structure

```
workshop-agent/
│
├── agent_demo_dependencies.py   # Core agent logic (RAG pipeline + ask_agent())
├── app.py                       # Streamlit UI
├── Agent_Demo.ipynb             # Development notebook (Colab)
├── Streamlit_Interface_Colab.ipynb  # Streamlit-in-Colab setup notebook
├── requirements.txt             # Python dependencies
└── README.md
```

---

## ⚙️ Setup & Installation

### Option 1 — Google Colab (Recommended)

1. Open `Agent_Demo.ipynb` in [Google Colab](https://colab.research.google.com/)
2. Mount your Google Drive and point `folder` to your document library:
   ```python
   folder = "/content/drive/MyDrive/your_folder/Document Library"
   ```
3. Add your Gemini API key to Colab Secrets as `gemin_api_key_2`
4. Run all cells

To launch the Streamlit UI from Colab, open `Streamlit_Interface_Colab.ipynb` and follow the instructions there.

---

### Option 2 — Local Setup

#### Prerequisites
- Python 3.9+
- A [Google Gemini API key](https://aistudio.google.com/app/apikey)

#### Install dependencies

```bash
git clone https://github.com/YOUR_USERNAME/workshop-agent.git
cd workshop-agent
pip install -r requirements.txt
```

#### Set your API key

```bash
# Linux / macOS
export YOUR_API_KEY="your_gemini_api_key_here"

# Windows (PowerShell)
$env:YOUR_API_KEY="your_gemini_api_key_here"
```

#### Point the agent to your documents

In `agent_demo_dependencies.py`, update the folder path:

```python
folder = "path/to/your/Document Library"
```

#### Run the Streamlit app

```bash
streamlit run app.py
```

Then open `http://localhost:8501` in your browser.

---

## 🔧 Configuration

| Parameter | Location | Default | Description |
|---|---|---|---|
| `RELEVANCE_THRESHOLD` | `agent_demo_dependencies.py` | `1.2` | L2 distance cutoff — lower = stricter grounding |
| `chunk_size` | `chunk_text()` | `800` | Characters per chunk |
| `overlap` | `chunk_text()` | `100` | Overlap between chunks |
| `k` | `retrieve_with_score()` | `4` | Number of chunks retrieved |
| `model` | `agent_demo_dependencies.py` | `gemini-2.5-flash-lite` | Gemini model variant |

---

## 📦 Dependencies

See `requirements.txt`. Key libraries:

- `google-generativeai` — Gemini API
- `sentence-transformers` — Local embeddings
- `faiss-cpu` — Vector similarity search
- `pypdf` — PDF text extraction
- `streamlit` — Web UI

---

## 💡 Example Questions

```
✅ Grounded (found in workshop materials):
   "What is a CNN?"
   "How did image generation models evolve?"

💬 General (not in workshop materials):
   "Explain transformer attention equations in detail."
```

---

## 🚧 Known Limitations

- Scanned PDFs (image-only) are not supported — text must be extractable
- The FAISS index is rebuilt on every startup; for large document sets, consider persisting it with `faiss.write_index()`
- Colab path hardcoding in `app.py` (`sys.path.append(...)`) must be updated for local runs

---

## 📄 License

MIT License — free to use, modify, and distribute.
