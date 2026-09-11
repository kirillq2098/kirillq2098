# Architecture Evidence

Этот раздел показывает не только технологии, но и **как я раскладываю систему на компоненты, потоки и границы ответственности**.

## 1. Service Automation & LeadRouter

### Context

```mermaid
graph LR
    A[Email / Calls / Chat / Forms] --> B[Bitrix24]
    B --> C[Webhook / Intake]
    C --> D[LeadRouter]
    D --> E[LLM Classifier]
    D --> F[Persistence]
    D --> G[Routing Rules]
    G --> H[CRM stage / owner / review]
    F --> I[Audit / Idempotency / Retry]
```

### Processing sequence

```mermaid
sequenceDiagram
    participant S as External Source
    participant API as FastAPI
    participant DB as PostgreSQL
    participant AI as LLM Provider
    participant CRM as Bitrix24

    S->>API: webhook(event_id, payload)
    API->>DB: create PROCESSING if unique
    alt duplicate event
        DB-->>API: existing event
        API-->>S: no second processing
    else new event
        API->>AI: structured classification
        AI-->>API: route + confidence + status
        API->>DB: persist result
        API->>CRM: permitted action / review route
        API->>DB: mark PROCESSED
        API-->>S: success
    end
```

### Architectural concerns

- idempotency before side effects;
- explicit transactional boundaries;
- retryable failures separated from business failures;
- read/write permissions constrained at integration layer;
- AI response treated as untrusted structured input;
- observable state instead of invisible automation.

---

## 2. FlyPingAvia

### Container view

```mermaid
graph TB
    U[Telegram User] --> TG[Telegram Bot / Mini App]
    TG --> API[FastAPI]
    API --> DB[(Subscription Storage)]
    SCH[APScheduler] --> CHECK[Price Checker]
    CHECK --> P[Travelpayouts / Price Provider]
    CHECK --> DB
    CHECK --> N[Notification Service]
    N --> TG
    API --> ATTR[Attribution / Deep Links]
    API --> HEALTH[Health / Readiness]
```

### Primary user flow

```mermaid
sequenceDiagram
    participant U as User
    participant APP as Telegram Mini App
    participant API as FastAPI
    participant DB as Storage
    participant SCH as Scheduler
    participant P as Price Provider

    U->>APP: route + dates + target price
    APP->>API: signed Telegram initData + watch
    API->>API: verify initData
    API->>DB: save watch
    SCH->>P: periodic price check
    P-->>SCH: current offers
    SCH->>DB: compare with threshold
    alt threshold reached
        SCH-->>U: price alert
    end
```

### Architectural concerns

- authentication boundary at Telegram initData;
- user interaction separated from scheduled work;
- provider failures must not corrupt watches;
- development tunnel and production URL are explicitly separated;
- health/recovery considered part of the product, not an afterthought.

---

## 3. TopStyle Control

### Position in enterprise landscape

```mermaid
graph LR
    CRM[Bitrix24] --> TC[TopStyle Control]
    ERP[1C / operational systems] --> TC
    MAN[Manual / imported data] --> TC
    TC --> WEB[Planning & Control UI]
    TC --> DB[(PostgreSQL)]
    WEB --> PM[Managers / planners / executives]
```

**Design principle:** TopStyle Control is intended as a management and planning layer. It should integrate with existing systems rather than duplicate every function of CRM or ERP.

### Application containers

```mermaid
graph TB
    WEB[Next.js Frontend] --> API[FastAPI Backend]
    API --> DB[(PostgreSQL)]
    MIG[Alembic] --> DB
    AUTH[Authentication / RBAC] --> API
    OPS[Docker Compose / Ops Scripts] --> WEB
    OPS --> API
    OPS --> DB
```

## Architecture principles I use

1. **Business boundary before framework.** First define what the system owns and what it should not own.
2. **Idempotency before retries.** Retrying unsafe side effects without idempotency creates duplicate business operations.
3. **State is explicit.** Important automation states must be inspectable and auditable.
4. **AI is a component, not the architecture.** LLM use should be bounded by contracts, validation, fallback and evaluation.
5. **Implementation ≠ production.** Repository evidence, tests and runtime evidence are separate evidence classes.
6. **Prefer the simplest architecture that preserves reliability and future evolution.** Microservices are not a default goal.

## Next architecture evidence to add

- C4 Context / Container diagrams generated from confirmed project state;
- ADR examples for important design decisions;
- threat model for public-facing AI services;
- observability model: logs, metrics, traces, SLO;
- cost model for LLM/API-dependent flows.
