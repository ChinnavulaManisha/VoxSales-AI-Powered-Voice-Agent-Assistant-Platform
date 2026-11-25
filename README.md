# VoxSales AI – Autonomous AI Sales Voice Agent

[![STT](https://img.shields.io/badge/STT-Web_Speech_Recognition-8A2BE2?style=flat-square)](https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API)
[![TTS](https://img.shields.io/badge/TTS-Speech_Synthesis_Audio-9333EA?style=flat-square)](https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API)
[![LLM](https://img.shields.io/badge/LLM-Groq_LPU_%7C_Gemini_2.5_Flash-F05A28?style=flat-square&logo=groq&logoColor=white)](https://groq.com)
[![RAG](https://img.shields.io/badge/RAG-FAISS_%2B_Sentence_Transformers-0081FB?style=flat-square)](https://github.com/facebookresearch/faiss)
[![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=flat-square&logo=FastAPI&logoColor=white)](https://fastapi.tiangolo.com)
[![React](https://img.shields.io/badge/React_19-Vite-61DAFB?style=flat-square&logo=react&logoColor=black)](https://react.dev)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=flat-square&logo=tailwind-css&logoColor=white)](https://tailwindcss.com)
[![MongoDB](https://img.shields.io/badge/MongoDB-8.x-green?style=flat-square&logo=mongodb&logoColor=white)](https://www.mongodb.com)

**VoxSales AI** is an autonomous AI-powered sales voice assistant designed for an online education platform (Fortivex Education). The assistant converses with prospective students, answers course queries accurately using **RAG (FAISS + Sentence Transformers)**, qualifies leads through a transparent rule-based scoring engine, extracts contact details with consent, and books 1-on-1 counseling appointments into **MongoDB** with strict concurrency and duplicate-prevention controls.

---

## Technical Evaluation Highlights (College Placement MVP)

- **End-to-End Functional Architecture**: Voice STT (Web Speech API) -> FastAPI -> RAG FAISS Vector Search -> Gemini Context Generation -> Dynamic Lead Scoring -> Real-time MongoDB Persistence -> Browser TTS Audio Voice Response.
- **Zero Mock / Hardcoded Data**: Dashboard KPIs, activity charts, lead tables, and appointment calendars are queried in real time from live MongoDB collections.
- **Transparent Rule-Based Lead Scoring**: Clear 0–100 rubric categorizing leads into **Hot (≥ 70)**, **Warm (40–69)**, and **Cold (< 40)** based on contact details, course specificity, buying signals, and scheduled counseling.
- **Duplicate Booking Prevention**: Atomic MongoDB queries guarantee that two prospects cannot book the same counseling slot simultaneously (returns HTTP 409 Conflict).
- **Graceful High-Reliability Fallback**: If a Gemini API key is not configured or network quota is exhausted during evaluation, an intelligent local counselor engine takes over seamlessly—guaranteeing 100% uptime and demonstration readiness.

---

## Technology Stack

| Layer | Technology | Purpose |
| :--- | :--- | :--- |
| **Primary Inference** | **Groq LPU Engine** (`qwen/qwen3.8-27b`, `llama-3.3-70b`) | Ultra-fast ~200–300ms conversational inference for voice streaming |
| **Secondary LLM** | **Google Gemini API** (`gemini-2.5-flash`, `gemini-1.5-flash`) | Advanced multi-turn conversational reasoning & consultative sales |
| **Vector RAG Embeddings** | **Hugging Face Sentence Transformers** (`all-MiniLM-L6-v2`) | Dense semantic embeddings (384-dim) over course syllabus chunks |
| **Vector Database** | **Meta FAISS** (`IndexFlatIP`) | Real-time dense inner-product vector similarity search |
| **Speech-to-Text (STT)** | **Browser Web Speech API** (`SpeechRecognition`) | Real-time client-side multilingual voice capture (English, Telugu, Hindi, etc.) |
| **Text-to-Speech (TTS)** | **Browser SpeechSynthesis API** | Natural voice audio playback with auto-speak and interrupt controls |
| **Frontend** | **React 19 + Vite + Tailwind CSS** | SaaS dashboard, voice orb, candidate registration, and CRM tables |
| **Icons & UI** | **Lucide React** | Modern SaaS icons, charts, and responsive interactive elements |
| **Backend** | **Python 3.14 + FastAPI + Uvicorn** | High-performance asynchronous REST API with ThreadPoolExecutor |
| **Database** | **MongoDB 8.x** (`voxsales_db`) | Collections: `leads`, `conversations`, `appointments`, `slots` |

---

## AI Tools & Architecture (STT, TTS, LLM, RAG)

VoxSales AI integrates a dedicated 4-pillar AI pipeline designed for ultra-low latency, natural voice interactions, and strictly grounded course information:

| Pillar | Technology | Implementation & Capabilities |
| :--- | :--- | :--- |
| **STT** (Speech-to-Text) | **Browser Web Speech API** (`SpeechRecognition`) | Real-time continuous voice recognition supporting multilingual speech (English, Hindi, Telugu, Tamil, Kannada, Malayalam). Zero network transit delay. |
| **TTS** (Text-to-Speech) | **Browser SpeechSynthesis API** | Instant conversational voice playback with natural cadence, Indian English accent support, auto-speak, and mid-sentence stop/interrupt controls. |
| **LLM** (Large Language Model) | **Groq LPU Engine + Google Gemini 2.5 Flash** | **Groq LPU**: Sub-300ms ultra-fast inference with `qwen/qwen3.8-27b` and `llama-3.3-70b-versatile` for live voice streaming.<br>**Gemini 2.5 Flash**: Consultative sales logic, sentiment classification, and structured prospect detail extraction. |
| **RAG** (Retrieval-Augmented Generation) | **Meta FAISS + Sentence Transformers (`all-MiniLM-L6-v2`)** | 384-dimensional dense semantic vector space with `IndexFlatIP` cosine similarity search over official academy syllabi, preventing hallucinations. |

---

## System Architecture

```mermaid
flowchart LR
    subgraph S1 ["1. VOICE INPUT (STT)"]
        User(["👤 Candidate"]) -->|"Speaks Query"| Mic["🎙️ Web Speech API (STT)"]
        Mic -->|"Transcribed Text"| Frontend["💻 React 19 Frontend"]
    end

    subgraph S2 ["2. KNOWLEDGE RETRIEVAL (RAG)"]
        Frontend -->|"POST /api/agent/interact"| API["⚡ FastAPI Backend"]
        API -->|"Query Embedding"| Embed["🔍 Sentence Transformers\n(all-MiniLM-L6-v2)"]
        Embed -->|"Vector Search"| FAISS[("📚 FAISS Vector DB\n(Course Syllabi & Fees)")]
        FAISS -->|"Top Verified Facts"| Engine["🧠 Context Builder"]
    end

    subgraph S3 ["3. INTELLIGENCE (LLM & SCORING)"]
        Engine -->|"Query + Course Facts"| LLM["⚡ Groq LPU (300ms)\n/ Gemini 2.5 Flash"]
        LLM -->|"Generated Voice Reply"| Scorer["📊 Lead Scorer\n(Hot / Warm / Cold | 0-100)"]
    end

    subgraph S4 ["4. STORAGE & VOICE OUTPUT (TTS)"]
        Scorer -->|"Persist Lead & Transcript"| Mongo[("🍃 MongoDB\n(Leads & Appointments)")]
        Scorer -->|"JSON Response"| Frontend
        Frontend -->|"Natural Audio"| TTS["🔊 SpeechSynthesis (TTS)"]
        TTS -->|"Speaks Answer"| User
    end

    style S1 fill:#f0f9ff,stroke:#0284c7,stroke-width:2px
    style S2 fill:#f5f3ff,stroke:#7c3aed,stroke-width:2px
    style S3 fill:#fffbeb,stroke:#d97706,stroke-width:2px
    style S4 fill:#ecfdf5,stroke:#059669,stroke-width:2px
```

### 💡 How to Explain This Architecture in 30 Seconds:
> *"The system works in 4 clean, sequential stages:"*
> 1. **Voice Capture (STT):** The candidate speaks into the browser microphone, and the native **Web Speech API** instantly converts speech into text.
> 2. **Knowledge Retrieval (RAG):** The text query hits **FastAPI**, where **Sentence Transformers** encodes it into a 384-dimensional vector, and **Meta FAISS** retrieves the exact course facts (fees, duration, syllabus) to eliminate hallucinations.
> 3. **Intelligence (LLM & Scoring):** The verified facts are passed to **Groq LPU** (or **Gemini**) for ultra-fast (sub-300ms) voice response generation, while the **Rule Scorer** evaluates student buying intent from 0 to 100 (*Hot / Warm / Cold*).
> 4. **Storage & Voice Vocalization (TTS):** The conversation and lead score are saved into **MongoDB**, and the browser speaks the reply out loud using **SpeechSynthesis (TTS)**.

---

## 5 Application Pages Overview

1. **Dashboard (`/`)**:
   - Executive overview of Total Leads, Hot Leads, Conversations, and Appointments.
   - Conversation & Lead activity timeline chart (last 7 days from MongoDB).
   - Academic course interest breakdown bars.
   - Recent qualified leads table with sentiment and direct booking shortcuts.
2. **Voice Agent (`/agent`)**:
   - Interactive microphone button with pulsing audio visualizer.
   - Live Speech-to-Text transcription and SpeechSynthesis audio reply.
   - Real-time **Prospect Qualification Panel** (name, phone, email, course, rule score, category).
   - **Retrieved RAG Sources Drawer**: displays matched course chunks and cosine similarity scores.
   - Fallback text chat input with quick-ask suggestion pills.
3. **Lead Management (`/leads`)**:
   - Comprehensive candidate table with search, category filtering (Hot/Warm/Cold), and course filtering.
   - Detailed candidate modal to edit notes, adjust qualification category, and review timelines.
   - Manual candidate lead creation form.
4. **Appointments (`/appointments`)**:
   - Available counseling slots calendar grouped by date across 6 daily windows.
   - Booked vs Open slot visual indicators.
   - Immediate confirmation with duplicate-booking rejection.
   - Confirmed bookings table with candidate details and timestamps.
5. **Knowledge Base (`/knowledge`)**:
   - Interactive **FAISS Cosine Similarity Simulator**: type any query to see vector search latency (ms), retrieved chunks, and scores.
   - Complete curricula, pricing, EMI options, and placement policies for:
     - Python Programming & Automation
     - Java Enterprise & Microservices
     - Full Stack Web Development (MERN + Next.js 14)
     - AI, Machine Learning & LLM Specialist
     - DSA & System Design Masterclass

---

## Quick Setup & Execution

### Prerequisites
- Node.js (v18+) & npm
- Python (v3.10+)
- MongoDB Community Server running on `127.0.0.1:27017`

### 1. Start MongoDB
Ensure MongoDB is running locally:
```powershell
mongod --dbpath "backend\data\db" --bind_ip 127.0.0.1 --port 27017
```

### 2. Backend Setup
```powershell
cd backend
pip install -r requirements.txt
# (Optional) Add your Gemini API key to backend\.env
# GEMINI_API_KEY=your_key_here
python -m uvicorn app.main:app --host 127.0.0.1 --port 8000 --reload
```
Backend API will be live at `http://127.0.0.1:8000` with Swagger Docs at `http://127.0.0.1:8000/docs`.

### 3. Frontend Setup
```powershell
cd frontend
npm install
npm run dev
```
Frontend application will be live at `http://localhost:5173`.

### 4. Run Automated End-to-End Verification Test
We have included a comprehensive test script that verifies all 11 evaluation requirements:
```powershell
$env:PYTHONPATH="backend"
python backend\test_e2e.py
```
Expected output: `>>> ALL 11 END-TO-END TEST CASES PASSED WITH 100% SUCCESS! <<<`

---

## One-Click Launchers (Windows)
- Double-click `run_demo.bat` to launch MongoDB, FastAPI backend, and React frontend simultaneously.
- Or use `start_backend.bat` and `start_frontend.bat` individually.
