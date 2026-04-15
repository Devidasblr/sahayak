# 🤝 Sahayak — सहायक

> **Voice AI Agent for Accessibility & Societal Impact**  
> HackBLR 2025 · Problem Statement 3

[![Built with Vapi](https://img.shields.io/badge/Voice-Vapi-4A9EFF?style=flat-square)](https://vapi.ai)
[![Powered by Qdrant](https://img.shields.io/badge/VectorDB-Qdrant-3ECF8E?style=flat-square)](https://qdrant.tech)
[![FastAPI](https://img.shields.io/badge/Backend-FastAPI-FF6B1A?style=flat-square)](https://fastapi.tiangolo.com)
[![Language](https://img.shields.io/badge/Language-Hindi%20%2B%20English-F5A623?style=flat-square)](#)

---

## 🌟 What is Sahayak?

Sahayak (सहायक, meaning *helper*) is a **multilingual voice AI agent** that helps rural and low-literacy users in India access government welfare schemes and basic healthcare guidance — through natural conversation in **Hindi and English**.

No reading. No typing. No confusion. Just talk.

---

## 🎯 The Problem We Solve

- **77%** of Ayushman Bharat eligible beneficiaries have never used it
- **30 crore+** Indians are functionally illiterate — text apps don't work for them
- **22+ languages** spoken across India — most AI tools are English-only

Government schemes worth crores go unclaimed every year — not because they don't exist, but because people can't navigate complex portals or understand eligibility criteria.

---

## 🏗️ Architecture

```
User (Voice Call)
      │
      ▼
   [ Vapi ]
   STT: Deepgram Nova-2 (Hindi)
   LLM: GPT-4o
   TTS: Azure hi-IN-SwaraNeural
      │
      │  tool call → search_knowledge(query, language)
      ▼
[ FastAPI /webhook ]
      │
      │  embed query → cosine search
      ▼
[ Qdrant Vector DB ]
   Model: paraphrase-multilingual-MiniLM-L12-v2
   Collection: sahayak_knowledge
      │
      │  top 3 relevant docs
      ▼
[ FastAPI ] ──► context injected into LLM prompt
      │
      ▼
[ Vapi ] ──► speaks answer back to user in Hindi/English
```

---

## 🔧 Tech Stack

| Layer | Technology | Why |
|-------|-----------|-----|
| Voice (STT + TTS + Call) | **Vapi** | Handles full call lifecycle, Hindi STT via Deepgram |
| Vector Database | **Qdrant** | Semantic search across Hindi+English knowledge base |
| Backend | **FastAPI** (Python) | Lightweight webhook handler for Vapi tool calls |
| Embeddings | **paraphrase-multilingual-MiniLM-L12-v2** | Natively understands Hindi + English |
| LLM | **GPT-4o** via Vapi | Grounded responses from retrieved context only |
| Voice (TTS) | **Azure hi-IN-SwaraNeural** | Natural Indian female voice users trust |

---

## 🗂️ Project Structure

```
sahayak/
├── main.py              # FastAPI app + Vapi webhook handler
├── qdrant_handler.py    # Qdrant search + embedding logic
├── ingest.py            # One-time data ingestion script
├── requirements.txt
├── .env.example
├── vapi_config.json     # Paste into Vapi dashboard
└── data/
    ├── schemes.json     # 6 government schemes (Hindi + English)
    └── healthcare.json  # 6 healthcare topics with home care guidance
```

---

## 🚀 How to Run Locally

### 1. Install dependencies
```bash
pip install -r requirements.txt
```

### 2. Set up environment
```bash
cp .env.example .env
# Fill in your QDRANT_URL and QDRANT_API_KEY
```

> **No Qdrant account?** The app automatically falls back to in-memory mode — perfect for demos.

### 3. Load knowledge base into Qdrant
```bash
python ingest.py
```

### 4. Start the server
```bash
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

### 5. Expose publicly via ngrok
```bash
ngrok http 8000
# Copy the https:// URL for Vapi webhook
```

### 6. Configure Vapi
1. Sign up at [vapi.ai](https://vapi.ai) · Use code `vapixhackblr` for $30 credits
2. Create a new Assistant
3. Open `vapi_config.json` → replace `YOUR_SERVER_URL` with your ngrok URL
4. Paste config into Vapi dashboard → click **Test**

---

## 🎙️ Sample Conversation

**User (Hindi):** *"Mera bachcha beemar rehta hai, hospital ka kharcha nahi utha sakta"*

**Sahayak:** *"Ji, Ayushman Bharat yojana mein aapke bachche ka 5 lakh rupaye tak ka muft ilaaj hoga. Aapko bas Aadhaar card aur ration card lekar nazdiki sarkari hospital jaana hai. Helpline number hai 14555."*

---

## 📚 Knowledge Base

### Government Schemes
| Scheme | Category | Key Benefit |
|--------|----------|-------------|
| Ayushman Bharat PM-JAY | Healthcare | ₹5 lakh free hospitalization/year |
| PM-KISAN | Agriculture | ₹6,000/year direct to farmers |
| PM Awas Yojana | Housing | ₹1.2 lakh for pucca house |
| Jan Dhan Yojana | Banking | Zero-balance bank account |
| PMJJBY | Insurance | ₹2 lakh life cover at ₹436/year |
| PM Ujjwala Yojana | Energy | Free LPG connection for BPL women |

### Healthcare Topics
- Fever (Bukhar) — Home care & when to see doctor
- Diabetes — Management & diet guide
- Maternal Health — Pregnancy care & free government support
- Child Vaccination — Full schedule & free centers
- Diarrhea & Dehydration — ORS guide for children
- Mental Health — Helplines & self-help

---

## ➕ Extending Sahayak

Add a new scheme in minutes — drop a JSON file in `data/` and re-run ingestion:

```json
{
  "id": "scheme_007",
  "title": "Scheme Name",
  "title_hi": "योजना का नाम",
  "category": "healthcare",
  "description": "English description",
  "description_hi": "Hindi description",
  "eligibility": "Who qualifies",
  "benefits": "What they receive",
  "how_to_apply": "Step by step",
  "documents": "Required documents",
  "helpline": "1800-xxx-xxxx",
  "keywords": ["keyword1", "keyword2"]
}
```

```bash
python ingest.py  # re-ingests all data files
```

**Adding a new language** (e.g. Kannada) = add translated fields to JSON + change Vapi transcriber language. Zero code changes needed.

---

## 🏅 Evaluation Criteria

| Criterion | How Sahayak delivers |
|-----------|---------------------|
| **Relevance** | Directly addresses PS3 — accessibility for low-literacy rural users |
| **Technical Complexity** | Multilingual RAG + real-time voice + tool-calling architecture |
| **Implementation Quality** | Clean separation of concerns, error handling, in-memory fallback |
| **Presentation** | Live voice demo in Hindi — emotional and concrete impact |

---

## 📄 License

MIT License — Built for HackBLR 2025
