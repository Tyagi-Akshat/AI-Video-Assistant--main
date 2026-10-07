# 🎬 AI Video Assistant

An AI-powered video and meeting assistant that transforms long-form videos, meetings, and audio recordings into **structured, searchable knowledge**.

The system automatically transcribes audio, generates summaries, extracts actionable insights, and provides a **Retrieval-Augmented Generation (RAG)** interface for asking questions directly against the transcript.

## ✨ Features

- 🎥 **YouTube Video Processing**
  - Accepts YouTube URLs as input
  - Downloads audio automatically using `yt-dlp`

- 📁 **Local File Processing**
  - Supports local audio/video files
  - Converts input media to normalized WAV audio using FFmpeg/Pydub

- 📝 **Automatic Transcription**
  - Uses **OpenAI Whisper** for English transcription
  - Supports **Sarvam AI** for Hinglish speech-to-English transcription
  - Processes long recordings through configurable audio chunking

- 🧠 **LLM-Powered Meeting Intelligence**
  - Generates a professional meeting title
  - Produces concise meeting summaries
  - Extracts action items
  - Identifies responsible owners and deadlines when available
  - Extracts key decisions
  - Identifies unresolved questions and follow-up topics

- 🔎 **RAG-Based Question Answering**
  - Splits transcripts into semantic chunks
  - Generates vector embeddings using `all-MiniLM-L6-v2`
  - Stores embeddings in **ChromaDB**
  - Retrieves the most relevant transcript sections
  - Uses **Mistral AI** to generate context-grounded answers

- 💬 **Interactive Interface**
  - Streamlit-based web interface
  - Chat with processed videos/meetings
  - View transcript, summary, decisions, action items and questions

---

## 🏗️ Architecture

```text
                    ┌─────────────────────┐
                    │   YouTube URL /     │
                    │   Local Video/File  │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  Audio Extraction   │
                    │  yt-dlp / FFmpeg    │
                    │      / Pydub        │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   Audio Chunking    │
                    │     10 min chunks   │
                    └──────────┬──────────┘
                               │
                    ┌──────────┴──────────┐
                    ▼                     ▼
             ┌──────────────┐      ┌──────────────┐
             │   Whisper    │      │   Sarvam AI  │
             │   English    │      │   Hinglish   │
             └──────┬───────┘      └──────┬───────┘
                    │                     │
                    └──────────┬──────────┘
                               ▼
                    ┌─────────────────────┐
                    │    Full Transcript  │
                    └──────────┬──────────┘
                               │
              ┌────────────────┼────────────────┐
              │                │                │
              ▼                ▼                ▼
       ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
       │ Summarizer  │  │ Information │  │ RAG Pipeline│
       │   Mistral   │  │ Extraction  │  │             │
       └─────────────┘  └─────────────┘  └──────┬──────┘
                                                 │
                                                 ▼
                                        ┌─────────────────┐
                                        │    ChromaDB     │
                                        │ + HF Embeddings │
                                        └────────┬────────┘
                                                 │
                                                 ▼
                                        ┌─────────────────┐
                                        │ Contextual      │
                                        │ Retrieval       │
                                        └────────┬────────┘
                                                 │
                                                 ▼
                                        ┌─────────────────┐
                                        │    Mistral AI   │
                                        │  Grounded Q&A   │
                                        └─────────────────┘
```

---

## 🧩 Tech Stack

### AI / LLM

- **Mistral AI**
- **OpenAI Whisper**
- **Sarvam AI**
- **LangChain**
- LangChain Expression Language (LCEL)

### RAG

- **ChromaDB**
- **HuggingFace Sentence Transformers**
- `all-MiniLM-L6-v2`
- Recursive Character Text Splitter
- Similarity Search

### Audio / Video

- `yt-dlp`
- FFmpeg
- Pydub

### Application

- Python
- Streamlit

---

## 📂 Project Structure

```text
AI-Video-Assistant/
│
├── app.py
├── main.py
├── Requirements.txt
│
├── core/
│   ├── extractor.py
│   ├── rag_engine.py
│   ├── summarizer.py
│   ├── transcriber.py
│   └── vector_store.py
│
└── utils/
    └── audio_processor.py
```

### Core Modules

#### `audio_processor.py`

Responsible for:

- Downloading YouTube audio
- Converting local media to WAV
- Resampling audio to 16 kHz mono
- Splitting long recordings into manageable chunks

#### `transcriber.py`

Provides multiple transcription backends:

```text
English  → OpenAI Whisper
Hinglish → Sarvam AI
```

Long Sarvam requests are further divided into smaller pieces to comply with API audio-duration limits.

#### `summarizer.py`

Uses Mistral AI to:

- Generate meeting titles
- Summarize transcript chunks
- Combine partial summaries into a final summary

A map-reduce style summarization approach is used for long transcripts.

#### `extractor.py`

Uses structured LLM prompts to extract:

- Action items
- Owners
- Deadlines
- Key decisions
- Open questions

#### `vector_store.py`

Creates the retrieval layer using:

```text
Transcript
    ↓
Recursive Chunking
    ↓
HuggingFace Embeddings
    ↓
ChromaDB
    ↓
Similarity Retriever
```

#### `rag_engine.py`

Implements the conversational RAG pipeline using LangChain LCEL:

```text
User Question
      ↓
Retriever
      ↓
Relevant Transcript Chunks
      ↓
Prompt + Context
      ↓
Mistral AI
      ↓
Grounded Answer
```

The model is explicitly instructed to answer only from retrieved transcript context.

---

## 🚀 Installation

### 1. Clone the repository

```bash
git clone https://github.com/Tyagi-Akshat/AI-Video-Assistant.git
cd AI-Video-Assistant
```

### 2. Create a virtual environment

```bash
python -m venv venv
```

Activate it:

**Windows**

```bash
venv\Scripts\activate
```

**Linux/macOS**

```bash
source venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r Requirements.txt
```

### 4. Install FFmpeg

FFmpeg is required for audio/video processing.

Verify the installation:

```bash
ffmpeg -version
```

### 5. Configure environment variables

Create a `.env` file:

```env
MISTRAL_API_KEY=your_mistral_api_key
SARVAM_API_KEY=your_sarvam_api_key

WHISPER_MODEL=small
SARVAM_STT_MODEL=saaras:v2.5
```

---

## ▶️ Running the Application

Launch the Streamlit application:

```bash
streamlit run app.py
```

The application will open in your browser.

---

## 💻 CLI Usage

The project also provides a Python pipeline through `main.py`.

```bash
python main.py
```

Enter:

```text
Enter YouTube URL or local file path:
```

Then select:

```text
english
```

or

```text
hinglish
```

After processing, the application generates:

```text
Title
Summary
Action Items
Key Decisions
Open Questions
```

You can then interactively ask questions about the processed meeting.

---

## 🔍 RAG Pipeline

The RAG implementation follows a standard retrieval-augmented generation architecture.

### 1. Document Chunking

The transcript is split using:

```python
RecursiveCharacterTextSplitter(
    chunk_size=500,
    chunk_overlap=50
)
```

### 2. Embedding Generation

Each chunk is converted into a vector using:

```text
sentence-transformers/all-MiniLM-L6-v2
```

### 3. Vector Storage

Embeddings are persisted locally using:

```text
ChromaDB
```

### 4. Similarity Retrieval

The retriever returns the top relevant transcript chunks:

```python
k = 4
```

### 5. Context-Grounded Generation

Retrieved chunks are inserted into a prompt and passed to Mistral AI.

The system instructs the model to avoid hallucinating information that is not present in the transcript.

---

## 🧠 Long Transcript Summarization

Instead of sending the entire transcript to the LLM in a single request, the project uses a map-reduce style workflow:

```text
Long Transcript
      │
      ▼
Split into chunks
      │
      ▼
Summarize each chunk
      │
      ▼
Partial summaries
      │
      ▼
Combine summaries
      │
      ▼
Final meeting summary
```

This makes the summarization pipeline more suitable for longer recordings.

---

## 📌 Example Output

### Meeting Summary

```text
• Discussed Q4 product roadmap
• Agreed to prioritize authentication improvements
• Backend migration targeted for next sprint
• Product team will finalize requirements
```

### Action Items

```text
1. Finalize API requirements
   Owner: Backend Team
   Deadline: Friday

2. Prepare migration plan
   Owner: DevOps Team
   Deadline: Not specified
```

### Key Decisions

```text
1. Adopt the new authentication architecture.
2. Delay the analytics dashboard until the next release.
```

### Open Questions

```text
1. What is the final migration timeline?
2. Who will own the production rollout?
```

---

## 🔮 Future Improvements

- Speaker diarization
- Timestamp-aware retrieval
- Multi-video knowledge bases
- Metadata filtering
- Hybrid BM25 + vector retrieval
- Reranking retrieved documents
- Streaming LLM responses
- Conversation memory
- Evaluation using RAGAS
- Citation/source timestamps in generated answers
- Cloud deployment with persistent vector storage
- Authentication and multi-user workspaces

---

## 👨‍💻 Author

**Akshat Tyagi**

B.Tech — Electronics & Communication Engineering  
LNMIIT, Jaipur

---

## ⭐ Project Highlights

```text
🎥 Video → Audio
        ↓
🎙️ Speech → Text
        ↓
🧠 LLM Processing
        ↓
📋 Structured Meeting Intelligence
        ↓
🔎 Vector Search
        ↓
💬 RAG-powered Q&A
```
