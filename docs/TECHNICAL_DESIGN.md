# 📐 Swaraj Saathi — Technical Design Document

> Made for Viksit Bharat 🇮🇳 by Sarthak Rodge

---

## 1. Problem Statement

Over **60% of rural Indians** are eligible for government welfare schemes but fail to access them due to fragmented data across 100+ portals, complex form-based processes, and language barriers.

**Swaraj Saathi** replaces static web forms with an **Agentic AI conversational interface** that autonomously guides citizens in their native language.

---

## 2. System Architecture Overview

```
  ┌────────────────────┐
  │   Citizen's Phone  │
  │  (React Native App)│
  └────────┬───────────┘
           │ REST API
  ┌────────▼───────────────────────────────┐
  │        DOCKER INFRASTRUCTURE            │
  │                                         │
  │  ┌──────────────────────────────────┐  │
  │  │     FastAPI Backend + Agentic    │  │
  │  │        AI Orchestrator           │  │
  │  └──────┬──────────┬───────────┬────┘  │
  │         │          │           │        │
  │    ┌────▼───┐ ┌────▼────┐ ┌───▼─────┐ │
  │    │ Redis  │ │Local SLM│ │PostgreSQL│ │
  │    │(Cache) │ │(Ollama/ │ │ (Data)   │ │
  │    │        │ │Sarvam)  │ │          │ │
  │    └────────┘ └─────────┘ └──────────┘ │
  │                                         │
  │  ┌──────────────────────────────────┐  │
  │  │   Automated Data Scrapers       │  │
  │  │   (Government Portal Ingestion) │  │
  │  └──────────────────────────────────┘  │
  └─────────────────────────────────────────┘
```

---

## 3. Key Components

| Component | Technology | Role |
|-----------|-----------|------|
| **Frontend** | React Native (Expo) | Multilingual mobile app |
| **Backend** | Python FastAPI | Agentic orchestration engine |
| **AI Engine** | Sarvam AI + Ollama | Privacy-first local inference |
| **Database** | PostgreSQL 16 | Scheme & grievance storage |
| **Cache** | Redis 7 | Session memory |
| **Data Pipeline** | Python Scrapers (CRON) | Automated scheme ingestion |
| **Infrastructure** | Docker Compose | One-command deployment |

---

## 4. Core Innovation — Agentic AI (Proprietary)

Unlike traditional chatbots that only answer questions, Swaraj Saathi uses a **proprietary Agentic State Machine** that:

- **Autonomously detects** citizen intent from natural language (Hindi / Marathi / English)
- **Guides step-by-step** through complex government processes without any web forms
- **Executes tasks** (file grievances, apply for schemes) and delivers a confirmation receipt — all within the chat

> *Implementation details of the state machine, intent classification pipeline, and portal adapter architecture are proprietary.*

---

## 5. Privacy-by-Design

| Concern | Approach |
|---------|----------|
| **Data Localization** | All AI runs locally via Ollama — zero external API calls |
| **Citizen PII** | Stored in isolated Docker network, never leaves the server |
| **Credentials** | Environment variables only, `.env` in `.gitignore` |

**Why Sarvam AI?** Built in India, optimized for Indic languages, lightweight enough for on-premise deployment without GPU clusters.

---

## 6. Deployment

The entire platform — database, cache, AI model, and API — is fully containerized. A single `docker-compose up -d` deploys everything.

| Container | Purpose |
|-----------|---------|
| `swaraj_backend` | API + Agentic Engine |
| `swaraj_postgres` | Persistent storage |
| `swaraj_redis` | Session cache |
| `swaraj_ollama` | Local AI inference |

---

## 7. Scalability Path

| Stage | Infrastructure |
|-------|---------------|
| Demo | Single laptop (Docker) |
| Pilot (1 District) | 2-core VM + GPU |
| State Rollout | Kubernetes + vLLM |
| National | Multi-region K8s |

---

*Proprietary implementation details are classified. This document provides an architectural overview for evaluation purposes only.*
