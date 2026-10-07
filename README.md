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
- Splitting long recordings into
