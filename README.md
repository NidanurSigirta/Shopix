# 🛍️ Shopix — AI-Powered Conversational Shopping Assistant


**Shopix** is a production-grade, multi-module conversational AI agent that transforms how users discover, compare, and purchase products. Built as an end-to-end e-commerce assistant, it combines a bias-free recommendation engine, GPT-4o Vision-powered visual search, and a fully hybrid deterministic + AI architecture — all orchestrated through n8n and backed by a real FastAPI microservice.

---

## 📖 About the Project

Shopix was engineered around a single non-negotiable principle: **the agent must act as a trustworthy advisor, never a salesperson.** Every product recommendation is transparently scored, every claim is grounded in real data, and every module was stress-tested — not just designed on paper.

Unlike a typical single-flow chatbot, Shopix routes every incoming message (text *or* image) through an intelligent Intent Router into one of nine specialized modules, each with its own data strategy, error-handling logic, and — where it matters — a deliberate choice between LLM reasoning and deterministic code.

---

## Key Features

- **🧭 Intelligent Intent Routing** — Classifies every message into 8 categories (or routes images directly to Visual Search) using GPT-4o-mini, with priority-ordered disambiguation rules for edge cases.
- **⚖️ Bias-Free Recommendation Engine** — A transparent, weighted scoring formula (text match, budget fit, rating, stock) ranks products with a human-readable "why I recommended this" rationale for every result.
- **🔍 Hybrid Product Comparison** — Deterministic table generation combined with AI-generated, criteria-aware narrative advice; multi-turn memory lets users answer follow-up questions without repeating product names.
- **💬 Full CRUD Review & Sentiment System** — Users can add, view, and delete reviews. Deletion works via fuzzy, content-based matching (Levenshtein distance) — session-independent and typo-tolerant.
- **📸 Visual Search (GPT-4o Vision)** — Upload a product photo with zero text, and Shopix identifies the item and matches it against the real catalog — fully integrated with the same Redis session state as every other search path.
- **📦 Live Stock & Order Support** — Real-time stock lookups via direct FastAPI calls (no LLM guesswork — the system never fabricates availability).
- **🔐 Privacy by Design (KVKK-Compliant)** — Automatic name masking, content moderation, strict TTL-based session expiry, and user-scoped deletion permissions — all baked into the architecture, not bolted on.
- **🚀 Load-Tested for Scale** — Validated at 50/150/300 concurrent users with a custom async load-testing suite: **100% success rate**, zero dropped requests, graceful degradation under 6x load.

---

## 👥 What Users Can Do

- **Discover** — Search naturally ("kulaklık arıyorum, 5000 TL bütçemle") and get ranked, justified recommendations.
- **Compare** — Ask the assistant to compare any two products and receive a criteria-driven verdict.
- **Review** — Read AI-summarized sentiment across real reviews, or add/remove their own.
- **Search Visually** — Upload a photo instead of typing a description.
- **Get Support** — Ask about shipping, payment, returns, or privacy — answered from a structured knowledge base, never invented.

---

## 🧠 Architecture Philosophy: AI vs. Deterministic Code

A core engineering decision throughout Shopix: **not every task should be solved with an LLM.**

| Used AI (GPT-4o / GPT-4o-mini) | Used Deterministic Code |
|---|---|
| Intent classification | Product ID resolution (Levenshtein fuzzy match) |
| Natural language criteria extraction | Scoring formula (budget/rating/stock weights) |
| Comparison narrative generation | Comparison table alignment |
| Sentiment summarization | Order support text search |
| Visual product analysis | Defensive JSON parsing layer |
| Greeting / fallback responses | KVKK name masking |

This split reduced API cost, eliminated an entire class of LLM-inconsistency bugs, and made the system's behavior predictable where predictability matters most.

---

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| **n8n** | Workflow orchestration engine connecting all 9 modules |
| **FastAPI (Python)** | Product, review, and scoring microservice with auto-generated Swagger docs |
| **Docker & Docker Compose** | Isolated, network-linked service architecture |
| **Redis** | Session memory, multi-turn context, TTL-based ephemeral state |
| **GPT-4o / GPT-4o-mini** | Intent routing, NLU, sentiment analysis, and image understanding (Vision) |
| **Python (asyncio/aiohttp)** | Custom load-testing suite for concurrency validation |
| **Mock Product Catalog (JSON)** | 941 products across a real-world 10-category, 80+ subcategory taxonomy |

---

## 📂 Project Structure

```
shopix/
├── shop-product-service/       # FastAPI microservice
│   ├── main.py                 # API endpoints (search, recommend, compare, reviews)
│   ├── catalog.json            # 941-product mock catalog
│   └── requirements.txt
├── n8n-workflows/               # Exported n8n workflow definitions
│   └── shopix-intent-router.json
├── load_test.py                 # Async concurrency test suite
├── docker-compose.yml           # Service orchestration
└── README.md                    # This file
```

---

## 🚀 Installation & Setup

**1. Clone the repository**
```bash
git clone https://github.com/nidanursigirta/shopix.git
cd shopix
```

**2. Start all services**
```bash
docker-compose up -d --build
```

**3. Verify the API is live**
```
http://localhost:8000/docs
```

**4. Open the n8n editor and activate the workflow**
```
http://localhost:5678
```

**5. Run a load test (optional)**
```bash
pip install aiohttp
python load_test.py --concurrent 50 --requests-per-user 5 --base-url http://localhost:8000
```

---

## 📊 Performance Benchmarks

| Concurrent Users | Success Rate | Median Latency | Requests/sec |
|---|---|---|---|
| 50 | 100% | 432 ms | 91.0 |
| 150 | 100% | 1,134 ms | 93.4 |
| 300 | 100% | 1,208 ms | 92.8 |

Zero failed requests across 6,000+ total requests, even at 6x baseline load — the system degrades gracefully (higher latency) rather than failing.

---

## 🔐 Security & Privacy

- Automatic **name masking** on all user-submitted reviews ("Nidanur Sığırta" → "Nidanur S.")
- **Content moderation** on new reviews only — never blocks a user's own deletion rights
- **TTL-based auto-expiry** on all session data (10–30 minutes, no indefinite storage)
- **Scoped delete permissions** — users can only remove their own contributions
- **No hallucinated data** — the agent never asserts stock/price facts it hasn't verified via API

---

## ✒️ Developer

**Nidanur Sigirta**

---

## 🛡️ License

© 2026 Shopix. All rights reserved.

*Built with intention — trustworthy AI, one honest recommendation at a time.*
