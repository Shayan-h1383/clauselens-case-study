# ClauseLens — AI contract analysis SaaS

**Live:** [clauselens.co.uk](https://www.clauselens.co.uk) · **Role:** Founder & sole developer · **Built:** Oct 2025 – present
**Stack:** Vue 3 · Vite · Tailwind · Node.js · Express · SQLite (Knex) · OpenAI API · Stripe · Docker · Nginx · PM2 · AWS EC2

> The production source code is private because ClauseLens is a paid product.
> This repository is a technical case study. Full source can be walked through on request.

---

## The problem

Freelancers and small businesses sign contracts they don't fully understand, and a lawyer's review is expensive. They need to know quickly which clauses are risky, what each clause really means and what to push back on.

## What I built

- **Upload a PDF or DOCX contract** and get a structured report back: contract type, parties, duration, key terms, an overall risk level, risk-by-risk analysis with mitigations, a clause-by-clause breakdown with 1–10 risk scores, and potential loopholes with suggested fixes.
- **Accounts:** email sign-up with OTP verification, Google OAuth, JWT sessions and password reset.
- **Plans:** Free, Gold (one-off pack) and Business (monthly), with per-user analysis limits.
- **Payments:** Stripe Checkout, a customer portal and webhooks. Prices are found by lookup key, so switching from test to live keys needs no code change.
- **Admin panel** for users, roles, bans and subscriptions.

## Results

| Metric | Value |
|---|---|
| Paying customers (to date) | **80+** |
| Contracts analysed | **500+** |

## Architecture

```mermaid
flowchart LR
    U[User<br/>Vue 3 SPA] -->|HTTPS| N[Nginx + Let's Encrypt]
    N --> API[Express API · PM2]
    API --> AUTH[Auth<br/>JWT · Google OAuth · OTP email]
    API --> AN[/analyze/]
    AN --> P[PDF / DOCX parsing]
    P --> LLM[OpenAI API<br/>structured JSON analysis]
    LLM --> V[JSON validation]
    V --> DB[(SQLite<br/>users · limits · history)]
    API --> BILL[/billing/<br/>Stripe Checkout · portal · webhooks]
    BILL --> DB
    subgraph AWS EC2 · Docker
      N
      API
      DB
    end
```

## Engineering highlights

### 1. Reliable structured output from an LLM
The analysis is returned as a strict JSON schema (contract info, risks, clauses, loopholes), not free text, so the frontend can render scores, colour-coded risk levels and tables. Responses go through JSON mode and validation. The model name is set by configuration, and the client works with both older models and the newer reasoning models, so upgrading needs no code change.

### 2. Payments that are safe to change
Stripe prices are looked up by key and cached for 10 minutes. Checkout, the billing portal and webhook-driven plan updates keep the database in sync with Stripe, and analysis limits are enforced on the server for each plan.

### 3. Production deployment on my own
The app is containerised with Docker and runs on AWS EC2 behind Nginx with HTTPS, using PM2 for process management and scripts for repeatable deploys. Rate limiting, CORS and security headers protect the API.

<!-- PIPELINE SECTION: add once the long-document agent pipeline is pushed to the private repo -->

## What I learned
- Running a product people pay for changes priorities: reliability, clear errors and billing correctness matter more than features.
- LLM output has to be treated as untrusted input: enforce a schema and validate it.

---
Built by **Shayan Hosseini** · [LinkedIn](https://www.linkedin.com/in/shayan--hosseini/) · [GitHub](https://github.com/Shayan-h1383)
