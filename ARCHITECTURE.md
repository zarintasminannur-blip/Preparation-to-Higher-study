# Architecture Overview

This document provides a high-level architecture overview for the Preparation-to-Higher-study project. The diagram below shows the main components and how they interact.

```mermaid
flowchart LR
  %% Clients
  subgraph Clients
    direction TB
    U[Student / Admin]
    Web[Web App<br/>(React / Vue)]
    Mobile[Mobile App<br/>(Flutter / React Native)]
  end

  U --> Web
  U --> Mobile

  %% Edge
  Web -->|HTTPS| APIGW[API Gateway / Reverse Proxy<br/>(Nginx / Cloud Load Balancer)]
  Mobile -->|HTTPS| APIGW

  %% Core
  APIGW -->|REST / GraphQL| Backend[Backend API<br/>(Django / FastAPI / Node)]
  Backend --> Auth[Auth Service<br/>(JWT / OAuth)]
  Backend --> DB[(Relational DB<br/>(Postgres))]
  Backend --> Cache[(Cache<br/>(Redis))]
  Backend --> Storage[S3 / Object Storage]
  Backend --> Worker[Background Workers<br/>(Celery / RQ / Sidekiq)]
  Worker --> Email[Email / SMS Provider<br/>(SendGrid / Twilio)]
  Worker --> Analytics[Analytics / Metrics<br/>(Prometheus / Segment)]

  %% Integrations & Ops
  External[External Services]
  External -->|OAuth / SSO| Auth
  External -->|File Sync| Storage
  External -->|Payments| Backend

  CI[CI / CD<br/>(GitHub Actions)]
  CI -->|build & deploy| Hosting[Hosting / Cloud<br/>(Heroku / AWS / Vercel / GCP)]
  Hosting --> APIGW
  Hosting --> Storage

  %% Notes
  classDef infra fill:#f9f9f9,stroke:#333,stroke-width:1px;
  class APIGW,Hosting,CI infra
```

Notes
- Clients: web and mobile applications used by students and admins.
- API Gateway / Reverse Proxy: handles TLS, routing, rate-limiting.
- Backend: implements business logic, exposes REST/GraphQL endpoints.
- Auth: central authentication (JWT + optional OAuth providers).
- DB / Cache: Postgres for primary data, Redis for sessions/queues/caching.
- Storage: S3-compatible for user uploads and static assets.
- Workers: handle long-running tasks (emails, report generation, imports).
- CI/CD: automated builds and deployments via GitHub Actions.
- External services: analytics, payment providers, SSO, email/SMS providers.

Recommended next steps
- Adjust the diagram to reflect the exact tech choices used in this repo (framework names, hosting provider).
- Add deployment diagram (infrastructure-as-code, network, secrets management).
- Commit this file to the repository root (ARCHITECTURE.md) and link it from README.
