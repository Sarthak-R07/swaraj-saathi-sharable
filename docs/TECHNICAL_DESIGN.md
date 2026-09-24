# 📐 Swaraj Saathi — Technical Design Document

> Version 1.0 | Smart India Hackathon Submission

---

## 1. Problem Statement

Over **60% of rural Indians** are eligible for government welfare schemes but fail to access them due to:
- Fragmented scheme data across 100+ government portals
- Complex form-based application processes requiring digital literacy
- Language barriers (most portals are English-only)
- No unified grievance tracking mechanism

**Swaraj Saathi** solves this by replacing static web forms with an **Agentic AI conversational interface** that autonomously guides citizens in their native language.

---

## 2. System Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                        CITIZEN'S PHONE                          │
│                   React Native (Expo) App                       │
│     ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐    │
│     │ Sahayak  │  │  Magic   │  │ Rakshak  │  │  Scheme  │    │
│     │  (Chat)  │  │Auto-Match│  │(Grievance)│  │ Browser  │    │
│     └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘    │
└──────────┼──────────────┼────────────┼──────────────┼──────────┘
           │              │            │              │
           ▼              ▼            ▼              ▼
    ┌──────────────────────────────────────────────────────┐
    │              REST API (HTTPS / JSON)                  │
    └──────────────────────┬───────────────────────────────┘
                           │
    ┌──────────────────────▼───────────────────────────────┐
    │               DOCKER INFRASTRUCTURE                   │
    │                                                       │
    │  ┌─────────────────────────────────────────────┐     │
    │  │         FastAPI Backend (Python)             │     │
    │  │                                             │     │
    │  │  ┌───────────┐  ┌──────────────────────┐   │     │
    │  │  │ Intent    │  │  Agentic State       │   │     │
    │  │  │ Classifier│──│  Machine (Orchestr.) │   │     │
    │  │  └───────────┘  └──────────┬───────────┘   │     │
    │  │                            │               │     │
    │  │  ┌───────────┐  ┌─────────▼───────────┐   │     │
    │  │  │ Scheme    │  │  Portal Adapters     │   │     │
    │  │  │ Matcher   │  │  (Form Templates)    │   │     │
    │  │  └───────────┘  └─────────────────────┘   │     │
    │  └─────────────────────┬───────────────────────┘     │
    │                        │                              │
    │  ┌─────────┐  ┌───────▼───────┐  ┌──────────────┐  │
    │  │ Redis   │  │ Local SLM     │  │ PostgreSQL   │  │
    │  │ (Cache) │  │ (Ollama /     │  │ (Schemes,    │  │
    │  │         │  │  Sarvam AI)   │  │  Grievances) │  │
    │  └─────────┘  └───────────────┘  └──────────────┘  │
    │                                                       │
    └───────────────────────────────────────────────────────┘

    ┌───────────────────────────────────────────────────────┐
    │            DATA INGESTION LAYER                       │
    │  Python Web Scrapers → Government Portal Data         │
    │  (Scheduled via CRON: daily at 2 AM)                  │
    └───────────────────────────────────────────────────────┘
```

---

## 3. Component Deep-Dive

### 3.1 Frontend — React Native (Expo)

| Screen | Purpose |
|--------|---------|
| **Sahayak (AI Chat)** | Conversational AI assistant with Agentic form-filling |
| **Magic Auto-Match** | Interview-based scheme eligibility engine |
| **Rakshak (Grievance)** | Visual grievance filing with photo upload & OCR |
| **Scheme Browser** | Searchable, categorized scheme catalog |
| **DigiLocker** | Secure document storage for citizens |

**Key Design Decisions:**
- Expo for cross-platform (Android + iOS) from single codebase
- NativeWind (Tailwind CSS) for responsive styling
- Multilingual support (English ↔ Marathi) via `LanguageContext`

---

### 3.2 Backend — FastAPI (Python)

The backend is not a simple CRUD API. It is an **Agentic Orchestrator** — an AI system that autonomously manages multi-step citizen interactions.

#### The Agentic Chat Pipeline

```
User Message → Intent Classifier → Router
                                      │
                    ┌─────────────────┼─────────────────┐
                    ▼                 ▼                  ▼
              TASK Intent       QUESTION Intent     PROFILE Intent
              (Grievance,       (Scheme info,       (Age, income,
               Apply, etc.)      eligibility)        location)
                    │                 │                  │
                    ▼                 ▼                  ▼
            State Machine       PostgreSQL           Redis Cache
            (Collect fields     Keyword Search       (Update user
             step-by-step)      + AI Response         profile)
                    │
                    ▼
            Portal Adapter
            (Execute task,
             return receipt)
```

**How the Agentic State Machine works:**

1. **IDLE** → User says "मुझे शिकायत दर्ज करनी है" (I want to file a complaint)
2. **INTENT DETECTED** → Classifier identifies `TASK: grievance_file`
3. **COLLECTING (Step 1/4)** → AI asks: "Which district?" 
4. **COLLECTING (Step 2/4)** → AI asks: "Which department?"
5. **COLLECTING (Step 3/4)** → AI asks: "Describe your issue"
6. **COLLECTING (Step 4/4)** → AI asks: "Your phone number?"
7. **EXECUTING** → Portal Adapter submits the grievance
8. **COMPLETED** → Receipt Card shown: `Ticket #GRV-2026-XXXX | Status: Registered`

---

### 3.3 AI Engine — Local SLM (Privacy-First)

| Feature | Implementation |
|---------|---------------|
| **Model** | Sarvam AI / Qwen 2.5 (run locally via Ollama) |
| **Hosting** | Self-hosted — NO data leaves the server |
| **Language Support** | Hindi, Marathi, English (native Indic understanding) |
| **Tasks** | Intent classification, field extraction, response generation |
| **Privacy** | 100% data localization — compliant with government data policies |

**Why Sarvam AI?**
- Built in India, optimized for 10+ Indian languages
- Superior accuracy on Indic dialects vs generic models (GPT, LLaMA)
- Lightweight enough to run on-premise without GPU clusters

**Why Local (Ollama)?**
- Government citizen data (Aadhaar, income, caste) must NEVER leave sovereign servers
- Zero dependency on third-party cloud APIs (OpenAI, Google, etc.)
- Works offline in areas with limited internet

---

### 3.4 Data Ingestion — Automated Web Scrapers

```
Government Portals          Python Scrapers          PostgreSQL
┌──────────────┐           ┌──────────────┐        ┌──────────────┐
│ mahadbt.gov  │──scrape──▶│  Crawler     │──save──▶│ schemes      │
│ pmjay.gov    │           │  Engine      │        │ (name, desc, │
│ maha.gov     │           │  (Scheduled) │        │  eligibility,│
│ india.gov    │           └──────────────┘        │  documents)  │
└──────────────┘             runs daily            └──────────────┘
```

- Scrapers run on a **CRON schedule** (daily at 2 AM)
- Data is cleaned, deduplicated, and stored in PostgreSQL
- Ensures the scheme database is always up-to-date without manual entry

---

### 3.5 Infrastructure — Docker Compose

All services are containerized for **one-command deployment**:

| Container | Image | Port | Purpose |
|-----------|-------|------|---------|
| `swaraj_backend` | Custom (FastAPI) | 8000 | API server |
| `swaraj_postgres` | postgres:16-alpine | 5432 | Persistent storage |
| `swaraj_redis` | redis:7-alpine | 6379 | Session cache |
| `swaraj_ollama` | ollama/ollama | 11434 | Local AI inference |

**Deployment:** `docker-compose up -d` starts the entire platform.

---

## 4. Data Flow Diagram

```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│  Citizen  │───▶│ Frontend │───▶│ Backend  │───▶│  Local   │
│  (Phone)  │◀───│  (Expo)  │◀───│ (FastAPI)│◀───│   SLM    │
└──────────┘    └──────────┘    └────┬─────┘    └──────────┘
                                     │
                              ┌──────┴──────┐
                              │             │
                         ┌────▼───┐   ┌─────▼────┐
                         │ Redis  │   │PostgreSQL │
                         │(Cache) │   │ (Data)    │
                         └────────┘   └──────────┘
```

**Request Lifecycle:**
1. Citizen types/speaks in Marathi on their phone
2. Frontend sends message to FastAPI backend via REST
3. Backend checks Redis for existing session context
4. Backend sends prompt to Local SLM for intent classification
5. If TASK → Agentic State Machine collects fields step-by-step
6. If QUESTION → PostgreSQL keyword search + AI-generated response
7. Response sent back to frontend with optional agent_step metadata
8. Frontend renders chat bubble (+ progress badge / receipt card if applicable)

---

## 5. Security & Privacy Architecture

| Concern | Solution |
|---------|----------|
| **Data Localization** | All AI inference runs locally (Ollama). Zero external API calls. |
| **Citizen PII** | Stored only in PostgreSQL behind Docker network isolation. |
| **API Security** | CORS whitelisting, session-based auth, rate limiting. |
| **Credentials** | Environment variables only — never hardcoded. `.env` in `.gitignore`. |
| **Network** | Docker bridge network — services not exposed beyond required ports. |

---

## 6. Scalability Path

| Stage | Infrastructure | Capacity |
|-------|---------------|----------|
| **Hackathon Demo** | Single laptop (Docker) | ~50 concurrent users |
| **Pilot (1 District)** | 2-core VM + GPU | ~1,000 users |
| **State Rollout** | Kubernetes cluster + vLLM | ~100,000+ users |
| **National** | Multi-region K8s + load balancer | Millions |

The Docker-first architecture ensures the same code runs identically from a laptop to a government data center.

---

## 7. Innovation Highlights

1. **Agentic AI (not a chatbot):** The system autonomously identifies missing information and guides the user — no static forms.
2. **Privacy-by-Design:** Indigenous SLM (Sarvam AI) runs on-premise; citizen data never touches third-party clouds.
3. **Zero Digital Literacy Required:** Citizens just text/talk naturally in their language. The AI handles the rest.
4. **Automated Data Pipeline:** Web scrapers keep the scheme database current without manual government intervention.
5. **One-Command Deployment:** `docker-compose up -d` deploys the entire platform — database, cache, AI, and API.

---

*Document prepared for Smart India Hackathon evaluation.*
