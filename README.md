# 🇮🇳 Swaraj Saathi — Voice-Native AI Platform for Rural Welfare Discovery

> **Made for Viksit Bharat 🇮🇳 by Sarthak Rodge**

Swaraj Saathi is an **Agentic AI-powered mobile platform** that helps rural Indian citizens discover, understand, and apply for government welfare schemes through a simple, conversational chat interface — in their own language.

Instead of navigating complex government portals, citizens simply *talk* to the AI assistant. The system autonomously guides them step-by-step to file grievances, apply for schemes, and track application status — all without filling a single form.

---

## ✨ Key Features

- 🗣️ **Conversational AI** — Chat naturally in Hindi, Marathi, or English
- 🤖 **Agentic Workflow** — AI autonomously handles multi-step government processes
- 🔒 **Privacy-First** — All AI runs locally, citizen data never leaves the server
- 📋 **Smart Scheme Matching** — Profile-based eligibility engine
- 📢 **Grievance Filing** — File and track complaints through chat
- 🔄 **Auto-Updated Data** — Automated scrapers keep scheme info current

---

## ⚡ Quick Start

### Prerequisites
- [Docker Desktop](https://www.docker.com/products/docker-desktop/) (v24+)
- [Node.js](https://nodejs.org/) (v18+)

### Setup

```bash
# 1. Clone the repo
git clone <repo-url>
cd swaraj-saathi

# 2. Copy environment template and fill in your values
cp backend/.env.example backend/.env

# 3. Start all services
docker-compose up -d

# 4. Start the mobile app
cd frontend
npm install
npx expo start
```

---

## 🔐 Environment Variables

Create a `.env` file from the provided `.env.example`. **Never commit real credentials.**

```env
# App
APP_NAME=Swaraj Saathi
DEBUG=True

# Database
DATABASE_URL=postgresql://<DB_USER>:<DB_PASSWORD>@<HOST>:<PORT>/<DB_NAME>

# AI Engine
LLM_BACKEND=ollama
LLM_MODEL=<model-name>

# Voice
VOICE_LANGUAGE=marathi

# Frontend
EXPO_PUBLIC_BACKEND_URL=http://<YOUR_SERVER_IP>:<PORT>/api/v1
```

---

## 📁 Repository Contents

```
├── README.md                    ← This file
├── LICENSE                      ← MIT License
├── docs/
│   └── TECHNICAL_DESIGN.md      ← Architecture overview
└── sample-data/
    ├── sample_schemes.json      ← Sample scheme data
    └── test_cases.json          ← Functional test cases
```

---

## 👤 Author

Made with ❤️ for **Viksit Bharat** 🇮🇳 by **Sarthak Rodge**.

---

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.
