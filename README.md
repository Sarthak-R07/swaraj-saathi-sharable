# 🇮🇳 Swaraj Saathi — Voice-Native AI Platform for Rural Welfare Discovery

> **Made for Viksit Bharat 🇮🇳 by Sarthak Rodge**

Swaraj Saathi is an **Agentic AI-powered mobile platform** that helps rural Indian citizens discover, understand, and apply for government welfare schemes through a simple, conversational chat interface — in their own language.

Instead of navigating complex government portals, citizens simply *talk* to the AI assistant. The system autonomously guides them step-by-step to file grievances, apply for schemes, and track application status — all without filling a single form.

---

## 🏗️ Tech Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| **Frontend** | React Native (Expo) | Cross-platform mobile app |
| **Backend** | Python FastAPI | REST API + Agentic Orchestrator |
| **AI Engine** | Sarvam AI / Qwen (via Ollama) | Local SLM for privacy-first inference |
| **Database** | PostgreSQL 16 | Scheme data, grievances, user profiles |
| **Cache** | Redis 7 | Session memory & chat context |
| **Data Ingestion** | Python Web Scrapers | Automated scheme data collection |
| **Containerization** | Docker & Docker Compose | One-command deployment |
| **Voice** | Sarvam AI STT/TTS | Native Indic language support |

---

## ⚡ Quick Start (One-Command Setup)

### Prerequisites
- [Docker Desktop](https://www.docker.com/products/docker-desktop/) (v24+)
- [Node.js](https://nodejs.org/) (v18+)
- [Expo CLI](https://docs.expo.dev/get-started/installation/) (`npm install -g expo-cli`)

### 1. Clone & Start Backend Infrastructure

```bash
git clone https://github.com/<your-org>/swaraj-saathi-sih.git
cd swaraj-saathi-sih

# Start all services (PostgreSQL, Redis, Ollama, FastAPI)
cd infra
docker-compose up -d

# Pull the local AI model (one-time, ~1.5 GB)
docker exec swaraj_ollama ollama pull qwen2.5
```

### 2. Start the Mobile App

```bash
cd frontend
npm install
npx expo start
```

Scan the QR code with **Expo Go** on your phone.

### 3. Verify Health

```bash
curl http://localhost:8000/api/v1/health
# Expected: {"status": "healthy", "version": "0.1.0"}
```

---

## 🔐 Environment Variables

Create a `.env` file in the `backend/` directory. **Never commit real credentials.**

```env
# ===== APP SETTINGS =====
APP_NAME=Swaraj Saathi
APP_VERSION=0.1.0
DEBUG=True
ENVIRONMENT=development

# ===== DATABASE =====
DATABASE_URL=postgresql://<DB_USER>:<DB_PASSWORD>@postgres:5432/swaraj_saathi
DATABASE_ECHO=False

# ===== REDIS =====
REDIS_URL=redis://redis:6379/0

# ===== LLM CONFIGURATION =====
# Backend options: "ollama" (local) or "sarvam" (Sarvam AI cloud)
LLM_BACKEND=ollama
LLM_ENDPOINT=http://ollama:11434
LLM_MODEL=qwen2.5
LLM_TEMPERATURE=0.3
LLM_MAX_TOKENS=1024

# ===== VOICE SETTINGS =====
STT_MODEL=whisper-small
TTS_MODEL=indicTTS
VOICE_LANGUAGE=marathi

# ===== SARVAM AI (Optional — for enhanced Indic NLP) =====
SARVAM_API_KEY=<your-sarvam-api-key>

# ===== CRAWLER SETTINGS =====
CRAWLER_ENABLED=True
CRAWLER_SCHEDULE_CRON=0 2 * * *
CRAWLER_TIMEOUT_SECONDS=300

# ===== API SETTINGS =====
API_TIMEOUT=30
CORS_ORIGINS=["http://localhost","http://10.0.2.2"]
```

Create a `.env` file in the `frontend/` directory:

```env
EXPO_PUBLIC_BACKEND_URL=http://<YOUR_SERVER_IP>:8000/api/v1
```

> ⚠️ Replace `<YOUR_SERVER_IP>` with your machine's local IP (e.g., `192.168.1.100`). Use `10.0.2.2` for Android Emulator.

---

## 📁 Project Structure

```
swaraj-saathi/
├── backend/                # Python FastAPI backend
│   ├── app/
│   │   ├── api/v1/         # REST endpoints (chat, schemes, health)
│   │   ├── services/       # Agentic orchestrator, portal adapters, crawlers
│   │   ├── db/             # Database models & migrations
│   │   └── core/           # Config, logging, exceptions
│   ├── Dockerfile
│   └── requirements.txt
├── frontend/               # React Native (Expo) mobile app
│   ├── src/
│   │   ├── app/            # Screens (Sahayak chat, interview, rakshak)
│   │   ├── components/     # Reusable UI components
│   │   ├── context/        # Auth & Language providers
│   │   ├── data/           # Fallback scheme data
│   │   └── lib/            # API clients
│   └── package.json
├── infra/                  # Docker Compose infrastructure
│   └── docker-compose.yml
├── docs/                   # Documentation
│   └── TECHNICAL_DESIGN.md
├── sample-data/            # Sample data & test cases
│   ├── sample_schemes.json
│   └── test_cases.json
├── LICENSE
└── README.md
```

---

## 👤 Author

Made with ❤️ for **Viksit Bharat** 🇮🇳 by **Sarthak Rodge**.

---

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.
