# FinanceBro (Project Plan)
# FinanceBro — Intelligent Investing Tutor

Product Requirements Document (PRD)

**Authors:** Yaphet Lemiesa
**Last updated:** 26 Jun 2025

---

## 1. Project Overview

### Project Name

FinanceBro — Intelligent Investing Tutor

### Target Users

* **Primary users:** Beginner‑to‑intermediate retail investors (ages 18–35) using online brokerages such as Robinhood, Webull, Fidelity and TD Ameritrade who want real‑time explanations while trading.
* **Secondary users:**

  * Personal‑finance content creators who embed tutorials in live streams
  * University finance clubs teaching portfolio basics
  * Casual investors seeking bite‑size refreshers during market hours

### Problem Statement

Retail investors face information overload and opaque jargon when viewing brokerage dashboards. Switching to external sites for definitions breaks flow and may expose sensitive data. FinanceBro provides an on‑page, privacy‑preserving AI coach that explains any chart element, offers credible sources, and guides risk‑aware decisions without leaving the trading screen.

---

## 2. User Stories

| As a…                 | I want to…                                                | So that I can…                                                                    |
| --------------------- | --------------------------------------------------------- | --------------------------------------------------------------------------------- |
| new trader            | hover over a P/E ratio and get a plain‑English definition | quickly grasp key metrics before buying                                           |
| intermediate investor | click “Buy or Sell?” on a stock chart                     | receive an AI assessment that references current fundamentals and my risk profile |
| mobile user           | quiz myself on recent lessons                             | reinforce concepts and track progress on the go                                   |

---

## 3. Market Analysis

### Existing Solutions

| Solution                        | Strengths                      | Weaknesses                                           |
| ------------------------------- | ------------------------------ | ---------------------------------------------------- |
| Investopedia pop‑ups            | Trusted brand, deep glossary   | Not context‑aware, manual search, no personalization |
| TradingView educational widgets | Real‑time data, chart overlays | Requires leaving broker site, subscription paywall   |
| ChatGPT finance queries         | Flexible Q\&A                  | Requires copy‑pasting, latency, privacy concerns     |

*FinanceBro differentiates by being broker‑overlay, privacy‑preserving (local model fallback), and quiz‑oriented.*

---

## 4. User Journey

1. **Install extension** from Chrome Web Store.
2. **Authenticate** with FinanceBro (OAuth or local mode).
3. **Open brokerage site**; extension injects unobtrusive helper.
4. **Hover/click** on page element → AI tooltip appears within 150 ms.
5. **Receive explanation + sources**; optional deeper chat.
6. **Complete daily quiz**; progress stored to profile.

---

## 5. Product Specifications

### Key Features

* Contextual on‑hover tooltips (definitions, formulae, risk notes)
* “Buy/Sell?” instant opinion with RAG‑backed evidence
* Source list with outbound links
* Spaced‑repetition quiz mode
* Dual‑path model serving: local Ollama or AWS inference
* Dashboard tracking learning streaks & quiz accuracy

### Component Breakdown

#### 1. Browser Extension UI

* **Design Specs:** React + Tailwind floating cards, dark/light adaptive
* **Interaction Rules:** On `mouseenter` of registered selector, debounce 200 ms → request explanation; on `mouseleave` hide card.
* **Logic Requirements:** Cache last 50 requests per tab; respect rate limit.

#### 2. Edge API Layer

* **Design Specs:** FastAPI endpoints `/explain`, `/recommend`, WebSocket `/stream`.
* **Interaction Rules:** If latency >150 ms local, fallback to AWS. JWT auth.
* **Logic Requirements:** Parse DOM element text, generate embedding, query vector DB, pass context to model, stream tokens.

#### 3. Model Serving

* **Local:** Ollama daemon with Llama 3 8 B‑Q4 on RTX 4090 (mac/Linux).
* **Cloud:** AWS ECS Fargate tasks (p4d instances) behind API Gateway + ALB; autoscale on CPU/GPU load.

#### 4. Data Store

* Postgres for user prefs & quiz results; Redis for session cache; FAISS for vector index of finance knowledge base.

---

## 6. Technical Requirements

* **Platforms:** Chrome, Edge, Chromium Brave; desktop first.
* **Languages/Frameworks:** TypeScript, React, FastAPI Python 3.12, Docker.
* **Dependencies:** Ollama, Llama.cpp, Haystack, SQLAlchemy, Redis, Postgres, AWS SDK.
* **Integrations:**

  * AWS CloudWatch / Prometheus → Grafana dashboards
  * CI/CD GitHub Actions → ECR + CloudFormation/Terraform
  * Broker page parsers (no API keys needed)

---

## 7. Success Metrics

| Metric                      | Target (6 mo post‑launch) |
| --------------------------- | ------------------------- |
| Tooltip latency (95 th pct) | ≤150 ms                   |
| Weekly active users (WAU)   | ≥1 000                    |
| Quiz completion rate        | ≥40 % of WAU              |
| D30 retention               | ≥30 %                     |
| Cloud API uptime            | ≥99.5 %                   |

---

## 8. Timeline & Milestones

| Date       | Milestone                            |
| ---------- | ------------------------------------ |
| 2025‑04‑15 | MVP extension + local model demo     |
| 2025‑05‑30 | AWS cloud serving + auth + basic RAG |
| 2025‑06‑30 | Quiz engine & user dashboard         |
| 2025‑07‑31 | Beta with 100 users + telemetry      |
| 2025‑09‑15 | Public launch v1.0                   |
