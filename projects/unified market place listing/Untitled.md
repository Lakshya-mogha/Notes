Understood — this is a substantial rewrite. Let me build the complete revised HLD incorporating all fixes: image compliance as a first-class pipeline, structured error contracts, pgvector purpose + brand-voice vectorization, cost-optimized model routing, and the consistency fixes.

---

# AI Listing Agent — Revised High-Level Design (v2.0)

## 1. System Overview

A four-layer platform that lets D2C brands list products on Amazon and Flipkart through an AI-assisted pipeline: **AI proposes, deterministic rules validate, humans approve, adapters execute, performance feeds back.**

```
┌─────────────────────────────────────────────────────────────────┐
│  EXPERIENCE — Next.js dashboard (connect, bulk import, review,   │
│              approve, analytics)                                 │
├─────────────────────────────────────────────────────────────────┤
│  INTELLIGENCE — LangGraph agents (content, keyword, attribute,   │
│              image, brand-voice retrieval)                       │
├─────────────────────────────────────────────────────────────────┤
│  CONTROL — Deterministic rules engine + structured error         │
│           contracts + human approval gate                        │
├─────────────────────────────────────────────────────────────────┤
│  EXECUTION — Amazon SP-API + Flipkart Seller API adapters        │
│              (canonical model → marketplace-specific mapping)    │
└─────────────────────────────────────────────────────────────────┘
```

**MVP scope: Amazon + Flipkart** — two adapters proving the adapter pattern generalizes. (This supersedes the earlier "one marketplace" note.)

---

## 2. End-to-End User Flow

1. **Connect** — OAuth to Amazon / Flipkart; tokens encrypted at rest.
2. **Import** — CSV bulk upload, manual entry, or brand catalog sync.
3. **Generate** — AI generates text (title, bullets, description, keywords, attributes) *and* checks images; brand voice retrieved from vectors.
4. **Validate** — Deterministic rules engine + vision model return structured pass/fail.
5. **Approve** — Human reviews generated output; rejects carry feedback back to the agent.
6. **Publish** — Adapters push to each marketplace with per-field result reporting.
7. **Learn** — Performance metrics (conversion, CTR, returns, ranking) feed the optimization loop.

---

## 3. AI Workflow (LangGraph)

```
START
  │
  ▼
LOAD ── product + marketplace rules + brand context (vector retrieval)
  │
  ▼
GENERATE ── text agents + image agent (parallel)
  │
  ▼
STRUCTURED LISTING (canonical JSON schema)
  │
  ▼
VALIDATION (rules engine + vision)
  │
  ├── FAILED ──► returns structured ErrorReport
  │                │
  │                ▼
  │             CORRECTION (agent receives exact field-level errors)
  │                │
  │                ▼
  │             bounded retries (max 3)
  │                │
  │                └── still failing ──► job blocked, exact reason surfaced
  │
  ▼ PASSED
QUALITY CHECK (confidence scoring)
  │
  ▼
HUMAN APPROVAL
  │
  ▼
PUBLISH (per-marketplace adapter)
```

**Batch mode (new):** CSV import → row-level validation → fan-out into parallel Agent Runs (bounded concurrency per marketplace to respect rate limits) → aggregate per-row results → per-row approval UI. Partial failures isolate to the row; the batch completes with a summary report.

---

## 4. Image Compliance Pipeline (New Section)

### 4.1 Why this is a dedicated pipeline

Amazon and Flipkart reject listings silently or visibly for image violations — white-background rules, resolution, text overlays, watermarks. This pipeline runs **before** human approval, in parallel with text generation.

### 4.2 Checks (deterministic + vision)

| Check | Amazon | Flipkart | Method |
|---|---|---|---|
| Main image background | Pure white RGB (255,255,255) | White / light | Programmatic pixel analysis + vision confirmation |
| Minimum resolution | 1000×1000 px (zoom) | 500 px min, 1000 recommended | Deterministic (PIL/exif) |
| Product fill ratio | ≥ 85% of frame | — | Vision model |
| Text overlays / watermarks | Not allowed on main image | Restricted | Vision model |
| Logo / branding rules | Only product logo on product itself | Same | Vision model |
| Aspect ratio | 1:1 main; others vary by slot | 1:1 and 3:4 | Deterministic |

### 4.3 Pipeline flow
```
Image upload → S3
     │
     ▼
Deterministic checks (resolution, format, aspect ratio)
     │
     ▼
Vision model review (background, fill, text, watermark)
     │
     ▼
Structured ImageReport (per image, per marketplace)
     │
     ├── pass → attach to listing
     └── fail/warn → surface issue + suggested fix to user
```

### 4.4 ImageReport schema (structured output)
```json
{
  "image_id": "img_123",
  "marketplace": "amazon",
  "slot": "main",
  "pass": false,
  "issues": [
    {
      "type": "background",
      "severity": "error",
      "detail": "Background is RGB(248,250,249), not pure white",
      "suggestion": "Replace background with RGB(255,255,255) or use background removal"
    },
    {
      "type": "resolution",
      "severity": "error",
      "detail": "Image is 800x800; minimum 1000x1000 for zoom",
      "suggestion": "Upscale or re-export at 1000x1000"
    },
    {
      "type": "text_overlay",
      "severity": "warning",
      "detail": "Small text detected in corner",
      "suggestion": "Remove text from main image; move to secondary slot"
    }
  ]
}
```

---

## 5. Structured Error Contract (New Section)

### 5.1 Design principle

Errors move through three layers and must remain **machine-readable at every boundary** so the correction agent can act on them without ambiguity.

### 5.2 Unified ErrorReport schema

Every failure — rules engine, vision model, marketplace API — returns the same envelope:

```json
{
  "status": "failed",
  "error_code": "VALIDATION_FAILED",
  "retryable": true,
  "attempts_used": 1,
  "max_attempts": 3,
  "errors": [
    {
      "field": "title",
      "error_code": "TITLE_TOO_LONG",
      "message": "Title is 221 chars; Amazon limit is 200",
      "value_submitted": "Organic Cotton Kurta Set... (221 chars)",
      "suggestion": "Shorten to <= 200 chars; move detail to bullets",
      "source": "rules_engine",
      "retryable": true,
      "marketplace": "amazon"
    },
    {
      "field": "bullets[2]",
      "error_code": "BANNED_TERM",
      "message": "Contains 'best' which is restricted on Flipkart",
      "value_submitted": "Best quality fabric...",
      "suggestion": "Replace with factual claim, e.g. 'Premium quality fabric'",
      "source": "rules_engine",
      "retryable": true,
      "marketplace": "flipkart"
    },
    {
      "field": "main_image",
      "error_code": "API_REJECTED_PARTIAL",
      "message": "Amazon accepted listing but rejected main image: background not white",
      "value_submitted": "s3://images/img_123.jpg",
      "suggestion": "Re-run through image pipeline with background removal",
      "source": "amazon_adapter",
      "retryable": true,
      "marketplace": "amazon",
      "api_error_detail": "Amazon SP-API: IMAGE_NOT_COMPLIANT"
    }
  ]
}
```

### 5.3 Error source taxonomy

| Source | Codes (examples) | Retryable? |
|---|---|---|
| `rules_engine` | `FIELD_TOO_LONG`, `BANNED_TERM`, `MISSING_ATTRIBUTE`, `CATEGORY_MISMATCH` | Yes — agent can correct |
| `vision_model` | `BACKGROUND_OFF`, `RESOLUTION_LOW`, `TEXT_DETECTED`, `WATERMARK` | Yes — image can be reprocessed |
| `amazon_adapter` | `API_REJECTED_PARTIAL`, `RATE_LIMITED`, `AUTH_EXPIRED`, `SKU_CONFLICT` | Depends — rate/auth retryable; SKU conflict requires user |
| `flipkart_adapter` | `API_REJECTED_PARTIAL`, `RATE_LIMITED`, `AUTH_EXPIRED`, `CATEGORY_NOT_MAPPED` | Same logic |

### 5.4 Retry policy
- Rules-engine failures: up to 3 agent correction attempts (bounded, documented).
- Rate-limit failures: exponential backoff, no agent involvement.
- Auth failures: auto-refresh token once; escalate to user if still failing.
- Partial API success: retry **only failed fields**, never re-push the whole listing (idempotency keys on the marketplace side).

---

## 6. Brand Voice Vectorization + pgvector (New/Clarified)

### 6.1 What gets vectorized

Everything the brand defines about itself, chunked and embedded:

| Artifact | Chunking | Example content |
|---|---|---|
| Brand voice guide | Per rule / tone clause | "Playful, not corporate. Use 'you' not 'the customer'." |
| Style preferences | Per guideline | "Never use exclamation marks. Bullets start with verbs." |
| Glossary / terminology | Per term | "Say 'kurta set' not 'kurta outfit'." |
| Past approved listings | Per listing field | High-performing titles, bullets, descriptions |
| Compliance preferences | Per rule | "Never claim 'organic' without certification." |

### 6.2 Embedding model
- **Default: a text-embedding model** (e.g., OpenAI `text-embedding-3-small`, or a self-hostable open model via sentence-transformers if you want zero per-token cost). Choice is a config, not an architecture decision.
- Images for brand *visual* style (if added later) can reuse the vision model's embeddings.

### 6.3 Retrieval at generation time
```
Generation request (product + brand_id + marketplace)
     │
     ▼
Embed the request context ("women's ethnic wear, brand X, tone: playful")
     │
     ▼
pgvector cosine-similarity search over brand embeddings (top-k = 8)
     │
     ▼
Inject retrieved chunks into the agent prompt as "Brand Voice Context"
     │
     ▼
Agent generates text conditioned on voice + marketplace rules
```

### 6.4 Why pgvector (not a separate vector DB)
- One database for relational data (products, listings, rules) **and** embeddings.
- Zero extra operational surface for MVP.
- Cosine similarity via `<=>` operator is sufficient for top-k retrieval at this scale (thousands of brand chunks, not millions).
- Migrate to a dedicated vector store only if retrieval latency or scale demands it (not before 50–100 brands).

### 6.5 Brand context contract
```json
{
  "brand_id": "brand_123",
  "retrieval_query": "organic cotton ethnic wear, playful premium voice",
  "retrieved_chunks": [
    {"chunk_id": "bv_01", "type": "tone_rule", "text": "Playful but premium...", "score": 0.89},
    {"chunk_id": "bv_12", "type": "glossary", "text": "Say 'kurta set' not 'outfit'", "score": 0.84}
  ],
  "embedding_model": "text-embedding-3-small",
  "top_k": 8
}
```

---

## 7. Listing Generation Responsibilities

| Agent | Input | Output | Notes |
|---|---|---|---|
| **Content Agent** | Product + brand voice chunks + rules | Title, bullets, description | Highest token spend |
| **Keyword Agent** | Category + competitor signals | Search terms, backend keywords | Feeds SEO score |
| **Attribute Agent** | Product + category template | Structured attributes (size, color, material) | Must match marketplace category schema |
| **Image Agent** | Product images | ImageReport per image per slot | Vision model + deterministic checks |
| **Optimization Agent** | Performance metrics | Proposed changes to existing listings | Runs on schedule, not in the create path |
| **Rules Engine** | Any generated output | ErrorReport or pass | Deterministic — never an LLM |

**Principle: the Rules Engine is the final arbiter. No LLM output ships without passing it.**

---

## 8. Data Architecture

| Entity | Key fields (new/changed in **bold**) |
|---|---|
| Tenant/Brand | id, name, **tone, guidelines, glossary, embedding_refs** |
| **BrandChunk** | **id, brand_id, type, text, embedding(vector), updated_at** |
| Product | id, brand_id, sku, title, images, attributes |
| MarketplaceAccount | id, brand_id, marketplace, token_enc, status |
| Listing | id, product_id, marketplace, status, external_id |
| ListingVersion | id, listing_id, version, content_json, **image_reports_json** |
| Rules | id, marketplace, category, rule_def, severity |
| **ValidationRun** | **id, listing_id, source(rules/vision/api), passed, error_report_json** |
| Performance | id, listing_id, impressions, clicks, orders, returns, date |
| **AgentRun** | **id, product_id, trigger, model_used, tokens_in, tokens_out, cost, status, error_report_json** |
| AuditLog | id, actor, action, entity, timestamp, before/after |

---

## 9. Marketplace Adapter Pattern

```python
class MarketplaceAdapter(ABC):
    async def get_listing(self, sku) -> ListingResult
    async def create_listing(self, listing) -> ListingResult
    async def update_listing(self, listing) -> ListingResult
    async def publish_listing(self, listing) -> ListingResult
    async def get_performance(self, listing_id, from_date, to_date) -> PerformanceResult
```

**Contract (strengthened):**

- Every method returns a `ListingResult` with **per-field success/failure**, not a boolean.
- On partial success, return exactly which fields failed and the marketplace's raw error string — the Rules Engine consumes this to decide retry vs. block.
- Adapters own: auth refresh, rate limiting, idempotency, error mapping to the unified ErrorReport taxonomy (Section 5.3).

---

## 10. Model Routing & Cost Optimization (New Section)

**Policy: choose the cheapest model that meets the quality bar for each task. No vendor lock-in.**

| Task | Model | Why | Approx. cost per op |
|---|---|---|---|
| Text generation (title, bullets, description) | **GPT-5.4 mini** (default) | Strong structured output + JSON mode + vision-capable, very cheap | $0.75/1M in, $4.50/1M out |
| Text generation (budget fallback) | DeepSeek-V3 | Cheaper input; good text quality; no native vision | ~$0.27/1M in |
| Image compliance vision | **GPT-5.4 mini (vision)** | One model for both text and images = simpler pipeline | Included in token pricing |
| Embeddings (brand voice) | `text-embedding-3-small` | Cheap, adequate for cosine retrieval | ~$0.02/1M tokens |
| Heavy reasoning / complex corrections | GPT-5.4 (up-tier) | Only if mini fails repeated corrections | On-demand |

**Routing rules:**

1. Default route = GPT-5.4 mini for text + vision.
2. If output fails JSON validation twice → escalate to next tier (only for that agent run).
3. If task is text-only and budget-constrained → route to DeepSeek-V3 via the same abstraction.
4. Embeddings always use the embedding model; never run the generation model for retrieval.
5. Log `model_used`, tokens, and cost on every AgentRun (Section 8) — this is your cost observability.

**Why GPT-5.4 mini is the right default:** it handles both text and vision, supports structured/JSON output natively, and at $0.75/$4.50 per 1M tokens a full listing generation costs well under ₹1. The model abstraction (point 3) means you can swap to DeepSeek for specific text workloads without architectural change.

---

## 11. API Layer (8 endpoints, +1 new)

| Endpoint | Purpose |
|---|---|
| `POST /products` | Create product (manual or bulk) |
| `POST /products/bulk` | **CSV upload → queue → validate → generate (new)** |
| `POST /listings/generate` | Trigger AI generation for a product |
| `POST /listings/validate` | Run rules engine + vision; return ErrorReport |
| `POST /listings/approve` | Human approval (with optional feedback) |
| `POST /listings/publish` | Push to marketplace(s) via adapter |
| `POST /marketplaces/connect` | OAuth connect Amazon / Flipkart |
| `GET /analytics` | Unified performance + sales attribution |
| `GET /images/:id/report` | **Fetch ImageReport for review (new)** |

---

## 12. Security & Multi-Tenancy (unchanged, retained)

- JWT (NextAuth) for app auth; OAuth for marketplace tokens; tokens AES-256 encrypted at rest.
- Mandatory `tenant_id` on every query (enforced at the ORM/repository layer, not optional).
- RBAC: owner / operator / reviewer / admin; generator cannot approve own listing (maker-checker).
- Full audit trail; TLS everywhere; secrets in AWS Secrets Manager.

---

## 13. Async Processing & Reliability (unchanged, retained)

- Redis queue (ARQ/Celery) for bulk upload, generation, publishing.
- Exponential backoff on rate limits; per-marketplace throttling.
- Pydantic/JSON schema validation + repair before persistence.
- Idempotency keys on all marketplace mutations.
- Every AgentRun stores its ErrorReport; failed jobs are re-runnable with the same input.

---

## 14. Tech Stack (updated)

| Layer | Choice | Notes |
|---|---|---|
| Frontend | Next.js + TypeScript + Tailwind | Dashboard, review UI, analytics |
| Auth | **NextAuth** | Self-hosted, replaces Clerk |
| Backend | FastAPI (Python) | Async, typed, auto OpenAPI docs |
| AI orchestration | LangGraph | Agent graph with bounded retries |
| LLM | **GPT-5.4 mini (default)** + DeepSeek-V3 fallback | Model routing layer (Section 10) |
| Vision | GPT-5.4 mini (vision) | Same model, image inputs |
| Embeddings | `text-embedding-3-small` | Brand voice + chunk retrieval |
| Vector store | **pgvector** | Same DB for relational + vectors |
| Database | PostgreSQL (RDS) | With pgvector extension |
| Queue | Redis + ARQ | Background jobs |
| Storage | S3-compatible | Images, CSVs, batch artifacts |
| Observability | Sentry + OpenTelemetry | Error + trace + cost logs |
| Infra | **AWS EC2 + RDS + ElastiCache** | Replaces Railway |

---

## 15. MVP Cost Estimate (updated)

| Item | Monthly (₹) | Notes |
|---|---|---|
| EC2 (dev + worker) | 3,750 | t3.medium + t3.small |
| RDS PostgreSQL | 1,500 | db.t3.micro, 20 GB |
| ElastiCache Redis | 1,200 | For queue |
| S3 + egress | 250 | Images + CSVs |
| LLM (GPT-5.4 mini) | 1,500–4,000 | ~2k listing generations + image checks + embeddings |
| **Total (infra + AI)** | **8,000–11,000** | Ballpark, small team, MVP phase |

At 50 brands in production, LLM scales roughly linearly with listing volume — budget 10–20k/month at that scale.

---

## 16. MVP Roadmap (updated)

| Phase | Deliverables |
|---|---|
| **1 (SLC)** | Amazon + Flipkart adapters, CSV bulk import, generation (text + image), validation with structured errors, approval, publish, basic analytics |
| **2** | Version history, image pipeline refinements, unified analytics dashboard, sales attribution, performance monitoring |
| **3** | Optimization agent (auto-suggest improvements), competitive signals, low-risk auto-publish (confidence threshold + consecutive-approval unlock) |

---

## 17. Why This Works (retained)

- **Reliability** — LLM can never bypass the Rules Engine; structured errors make every failure actionable.
- **Defensibility** — brand-voice vectors + version history + performance feedback loop compound over time.
- **Marketplace independence** — canonical model + adapters; adding Myntra/Ajio later is an adapter, not a rewrite.
- **Cost efficiency** — event-driven generation (only when needed), model routing, and the optimization agent only fire on schedule, not continuously.

---

### Summary of changes from your original document

| # | Change | Where |
|---|---|---|
| 1 | Fixed "GPT-5.4 mini" — confirmed available; expanded into a model-routing section with cost policy | §10 |
| 2 | Fixed ₹ symbol and cost realism | §15 |
| 3 | Aligned MVP scope to Amazon + Flipkart everywhere | §1, §16 |
| 4 | **Added full Image Compliance Pipeline** with schema | §4 |
| 5 | **Added Structured Error Contract** with unified schema + taxonomy | §5 |
| 6 | **Added pgvector purpose + brand-voice vectorization** | §6 |
| 7 | Added batch/bulk upload flow to the workflow | §3 |
| 8 | Defined the "Learn" mechanism concretely | §2, §16 |
| 9 | Specified adapter partial-failure behavior | §9 |
| 10 | Added maker-checker approval principle | §12 |
