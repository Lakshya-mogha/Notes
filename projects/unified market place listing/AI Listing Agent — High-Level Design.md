## Purpose

Build an AI-powered marketplace listing operator for D2C brands. The platform lets brands create, validate, and publish product listings across multiple marketplaces through a single interface — with AI assistance at every step.

## Primary Users

- **D2C Brand Owners / E-commerce Managers** — non-technical users who want to list products without mastering each marketplace's rules
- **Listing Operators / Catalog Teams** — need speed, consistency, and error reduction across bulk uploads
- **Reviewers / Approvers** — need trust, auditability, and control before anything goes live

## MVP Scope

Amazon + Flipkart — two adapters proving the adapter pattern generalizes.

## Core Design Principle

**AI proposes, deterministic rules validate, humans approve, adapters execute, performance feeds back.**

---

## 1. System Overview

A four-layer platform:

| Layer | Responsibility | Components |
|---|---|---|
| **Experience** | User-facing dashboard | Next.js app for connect, import, review, approve, analytics |
| **Intelligence** | AI generation & retrieval | LangGraph agents for content, keywords, attributes, images, brand-voice |
| **Control** | Deterministic validation & approval | Rules engine, structured error contracts, human approval gate |
| **Execution** | Marketplace operations | Amazon SP-API + Flipkart Seller API adapters |

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

---

## 2. End-to-End User Flow

1. **Connect** — OAuth to Amazon / Flipkart; tokens encrypted at rest.
2. **Import** — CSV bulk upload, manual entry, or brand catalog sync.
3. **Generate** — AI generates text (title, bullets, description, keywords, attributes) and checks images; brand voice retrieved from vectors.
4. **Validate** — Deterministic rules engine + vision model return structured pass/fail.
5. **Approve** — Human reviews generated output; rejects carry feedback back to the agent.
6. **Publish** — Adapters push to each marketplace with per-field result reporting.
7. **Learn** — Performance metrics (conversion, CTR, returns, ranking) feed the optimization loop.

---

## 3. AI Listing Agent Workflow

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

**Bounded retry policy:**

- Rules-engine failures: up to 3 agent correction attempts (bounded, documented).
- Rate-limit failures: exponential backoff, no agent involvement.
- Auth failures: auto-refresh token once; escalate to user if still failing.
- Partial API success: retry only failed fields, never re-push the whole listing (idempotency keys on the marketplace side).

**Batch mode:** CSV import → row-level validation → fan-out into parallel Agent Runs (bounded concurrency per marketplace to respect rate limits) → aggregate per-row results → per-row approval UI. Partial failures isolate to the row; the batch completes with a summary report.

---

## 4. Listing Generation Responsibilities

| Agent / Service | Input | Output | Notes |
|---|---|---|---|
| **Content Agent** | Product + brand voice chunks + rules | Title, bullets, description | Highest token spend |
| **Keyword Agent** | Category + competitor signals | Search terms, backend keywords | Feeds SEO score |
| **Attribute Agent** | Product + category template | Structured attributes (size, color, material) | Must match marketplace category schema |
| **Image Agent** | Product images | ImageReport per image per slot | Vision model + deterministic checks |
| **Optimization Agent** | Performance metrics | Proposed changes to existing listings | Runs on schedule, not in the create path |
| **Rules Engine** | Any generated output | ErrorReport or pass | Deterministic — never an LLM |

**Principle: the Rules Engine is the final arbiter. No LLM output ships without passing it.**

---

## 5. Data Architecture

| Entity | Key fields |
|---|---|
| Tenant/Brand | id, name, tone, guidelines, glossary, embedding_refs |
| BrandChunk | id, brand_id, type, text, embedding(vector), updated_at |
| Product | id, brand_id, sku, title, images, attributes |
| MarketplaceAccount | id, brand_id, marketplace, token_enc, status |
| Listing | id, product_id, marketplace, status, external_id |
| ListingVersion | id, listing_id, version, content_json, image_reports_json |
| Rules | id, marketplace, category, rule_def, severity |
| ValidationRun | id, listing_id, source (rules/vision/api), passed, error_report_json |
| Performance | id, listing_id, impressions, clicks, orders, returns, date |
| AgentRun | id, product_id, trigger, model_used, tokens_in, tokens_out, cost, status, error_report_json |
| AuditLog | id, actor, action, entity, timestamp, before/after |

---

## 6. Marketplace Adapter Pattern

```python
class MarketplaceAdapter(ABC):
    async def get_listing(self, sku) -> ListingResult
    async def create_listing(self, listing) -> ListingResult
    async def update_listing(self, listing) -> ListingResult
    async def publish_listing(self, listing) -> ListingResult
    async def get_performance(self, listing_id, from_date, to_date) -> PerformanceResult
```

**Contract:**

- Every method returns a `ListingResult` with per-field success/failure, not a boolean.
- On partial success, return exactly which fields failed and the marketplace's raw error string — the Rules Engine consumes this to decide retry vs. block.
- Adapters own: auth refresh, rate limiting, idempotency, error mapping to the unified ErrorReport taxonomy.
- Canonical model → marketplace-specific mapping happens inside the adapter; the rest of the system never touches raw marketplace APIs.

---

## 7. API Layer

| Endpoint | Purpose |
|---|---|
| `POST /products` | Create product (manual or bulk) |
| `POST /products/bulk` | CSV upload → queue → validate → generate |
| `POST /listings/generate` | Trigger AI generation for a product |
| `POST /listings/validate` | Run rules engine + vision; return ErrorReport |
| `POST /listings/approve` | Human approval (with optional feedback) |
| `POST /listings/publish` | Push to marketplace(s) via adapter |
| `POST /marketplaces/connect` | OAuth connect Amazon / Flipkart |
| `GET /analytics` | Unified performance + sales attribution |
| `GET /images/:id/report` | Fetch ImageReport for review |

---

## 8. Security & Multi-Tenancy

| Concern | Design |
|---|---|
| App authentication | JWT via NextAuth (self-hosted) |
| Marketplace authentication | OAuth (Amazon SP-API, Flipkart Seller API) |
| Token storage | AES-256 encrypted at rest |
| Tenant isolation | Mandatory `tenant_id` on every query, enforced at the repository layer |
| Roles | Owner / operator / reviewer / admin |
| Approval integrity | Generator cannot approve own listing (maker-checker) |
| Auditability | Full audit trail on every action |
| Transport | TLS everywhere |
| Secrets | AWS Secrets Manager |

---

## 9. Async Processing & Reliability

| Problem | Solution |
|---|---|
| Bulk uploads time out synchronously | Redis queue (ARQ) for batch jobs |
| Amazon/Flipkart APIs are slow | All marketplace calls are async, never blocking the request cycle |
| Rate limits rejected by marketplaces | Exponential backoff + per-marketplace throttling |
| LLM output is not guaranteed valid | Pydantic/JSON schema validation + repair before persistence |
| Network failures mid-publish | Idempotency keys on all marketplace mutations |
| Failed jobs lose context | Every AgentRun stores its ErrorReport; failed jobs are re-runnable with same input |

---

## 10. Recommended Tech Stack

| Layer | Choice | Notes |
|---|---|---|
| Frontend | Next.js + TypeScript + Tailwind | Dashboard, review UI, analytics |
| Auth | NextAuth | Self-hosted |
| Backend | FastAPI (Python) | Async, typed, auto OpenAPI docs |
| AI orchestration | LangGraph | Agent graph with bounded retries |
| LLM | GPT-5.4 mini (default) + DeepSeek-V3 fallback | Model routing layer |
| Vision | GPT-5.4 mini (vision) | Same model, image inputs |
| Embeddings | text-embedding-3-small | Brand voice + chunk retrieval |
| Vector store | pgvector | Same DB for relational + vectors |
| Database | PostgreSQL (RDS) | With pgvector extension |
| Queue | Redis + ARQ | Background jobs |
| Storage | S3-compatible | Images, CSVs, batch artifacts |
| Observability | Sentry + OpenTelemetry | Error + trace + cost logs |
| Infra | AWS EC2 + RDS + ElastiCache | Production hosting |

---

## 11. MVP Operating Cost

| Item | Monthly (₹) | Notes |
|---|---|---|
| EC2 (dev + worker) | 3,750 | t3.medium + t3.small |
| RDS PostgreSQL | 1,500 | db.t3.micro, 20 GB |
| ElastiCache Redis | 1,200 | For queue |
| S3 + egress | 250 | Images + CSVs |
| LLM (GPT-5.4 mini) | 1,500–4,000 | ~2k listing generations + image checks + embeddings |
| **Total (infra + AI)** | **8,000–11,000** | Ballpark, small team, MVP phase |

At 50 brands in production, LLM scales roughly linearly with listing volume — budget 10,000–20,000/month at that scale.

---

## 12. MVP Roadmap

| Phase | Deliverables |
|---|---|
| **Phase 1** | Amazon + Flipkart adapters, CSV bulk import, generation (text + image), validation with structured errors, approval, publish, basic analytics |
| **Phase 2** | Version history, image pipeline refinements, unified analytics dashboard, sales attribution, performance monitoring |
| **Phase 3** | Optimization agent (auto-suggest improvements), competitive signals, low-risk auto-publish (confidence threshold + consecutive-approval unlock) |

---

## 13. Why This Architecture Works

- **Reliability** — LLM can never bypass the Rules Engine; structured errors make every failure actionable.
- **Defensibility** — brand-voice vectors + version history + performance feedback loop compound over time.
- **Marketplace independence** — canonical model + adapters; adding Myntra/Ajio later is an adapter, not a rewrite.
- **Cost efficiency** — event-driven generation (only when needed), model routing, and the optimization agent only fires on schedule, not continuously.

---

## Image Compliance Pipeline

### Why a dedicated pipeline

Amazon and Flipkart reject listings silently or visibly for image violations — white-background rules, resolution, text overlays, watermarks. This pipeline runs before human approval, in parallel with text generation.

### Checks

| Check | Amazon | Flipkart | Method |
|---|---|---|---|
| Main image background | Pure white RGB (255,255,255) | White / light | Programmatic pixel analysis + vision confirmation |
| Minimum resolution | 1000×1000 px (zoom) | 500 px min, 1000 recommended | Deterministic (PIL/exif) |
| Product fill ratio | ≥ 85% of frame | — | Vision model |
| Text overlays / watermarks | Not allowed on main image | Restricted | Vision model |
| Logo / branding rules | Only product logo on product itself | Same | Vision model |
| Aspect ratio | 1:1 main; others vary by slot | 1:1 and 3:4 | Deterministic |

### Pipeline flow

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

### ImageReport schema

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

## Structured Error Contract

### Design principle

Errors move through three layers and must remain machine-readable at every boundary so the correction agent can act on them without ambiguity.

### Unified ErrorReport schema

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

### Error source taxonomy

| Source | Codes (examples) | Retryable? |
|---|---|---|
| `rules_engine` | `FIELD_TOO_LONG`, `BANNED_TERM`, `MISSING_ATTRIBUTE`, `CATEGORY_MISMATCH` | Yes — agent can correct |
| `vision_model` | `BACKGROUND_OFF`, `RESOLUTION_LOW`, `TEXT_DETECTED`, `WATERMARK` | Yes — image can be reprocessed |
| `amazon_adapter` | `API_REJECTED_PARTIAL`, `RATE_LIMITED`, `AUTH_EXPIRED`, `SKU_CONFLICT` | Depends — rate/auth retryable; SKU conflict requires user |
| `flipkart_adapter` | `API_REJECTED_PARTIAL`, `RATE_LIMITED`, `AUTH_EXPIRED`, `CATEGORY_NOT_MAPPED` | Same logic |

---

## Brand Voice Vectorization

### What gets vectorized

Everything the brand defines about itself, chunked and embedded:

| Artifact | Chunking | Example content |
|---|---|---|
| Brand voice guide | Per rule / tone clause | "Playful, not corporate. Use 'you' not 'the customer'." |
| Style preferences | Per guideline | "Never use exclamation marks. Bullets start with verbs." |
| Glossary / terminology | Per term | "Say 'kurta set' not 'kurta outfit'." |
| Past approved listings | Per listing field | High-performing titles, bullets, descriptions |
| Compliance preferences | Per rule | "Never claim 'organic' without certification." |

### Embedding model

Default: `text-embedding-3-small`. Choice is a config, not an architecture decision.

### Retrieval at generation time

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

### Why pgvector

- One database for relational data (products, listings, rules) and embeddings.
- Zero extra operational surface for MVP.
- Cosine similarity via the `<=>` operator is sufficient for top-k retrieval at this scale (thousands of brand chunks, not millions).
- Migrate to a dedicated vector store only if retrieval latency or scale demands it.

### Brand context contract

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

## Model Routing & Cost Optimization

**Policy: choose the cheapest model that meets the quality bar for each task. No vendor lock-in.**

| Task | Model | Why |
|---|---|---|
| Text generation (title, bullets, description) | GPT-5.4 mini (default) | Strong structured output + JSON mode + vision-capable, very cheap |
| Text generation (budget fallback) | DeepSeek-V3 | Cheaper input; good text quality; no native vision |
| Image compliance vision | GPT-5.4 mini (vision) | One model for both text and images = simpler pipeline |
| Embeddings (brand voice) | text-embedding-3-small | Cheap, adequate for cosine retrieval |
| Heavy reasoning / complex corrections | GPT-5.4 (up-tier) | Only if mini fails repeated corrections |

**Routing rules:**

1. Default route = GPT-5.4 mini for text + vision.
2. If output fails JSON validation twice → escalate to next tier (only for that agent run).
3. If task is text-only and budget-constrained → route to DeepSeek-V3 via the same abstraction.
4. Embeddings always use the embedding model; never run the generation model for retrieval.
5. Log `model_used`, tokens, and cost on every AgentRun — this is your cost observability.

---

## 14. Competitive Landscape: Amazon & Flipkart Seller Agents

### What exists today

Neither Amazon nor Flipkart sells a single turnkey "AI Listing Agent." Instead, both marketplaces expose APIs (Amazon SP-API, Flipkart Seller API) that third parties build automation tools on top of. The "agents" sellers actually use are these third-party tools.

| Player | Platform | What it does | Weakness |
|---|---|---|---|
| Helium 10 | Amazon | Keyword research, listing optimization, PPC, repricing | Amazon-only, ~$100+/mo, rule-based |
| Jungle Scout | Amazon | Product research, keyword, sales estimates | Amazon-only, no generative content |
| Sellics / Perpetua | Amazon | PPC automation, profit analytics | Enterprise-priced |
| GoFynd | Flipkart | Catalog, pricing, order mgmt for Flipkart sellers | Flipkart-only, rule-based |
| Unicommerce | Multi | Order/inventory sync across marketplaces | Logistics-focus, thin AI layer |
| EasyEcom | Multi | Inventory, order, returns sync | Operations-focus, not listing intelligence |

### What these agents actually do

1. **Listing optimization** — regenerate titles/descriptions/bullets/keywords from templates or basic keyword extraction.
2. **Dynamic pricing** — repricing rules ("if competitor drops 5%, drop 3%") against scraped competitor data.
3. **Inventory management** — stock tracking, restock alerts, FBA/Flipkart Fulfillment sync.
4. **Advertising automation** — Sponsored Products/Listings bid & budget adjustments.
5. **Order processing** — labels, tracking, customer notifications.
6. **Review/feedback management** — monitor reviews, flag negatives, request reviews.

### How they work

- **Data sources:** marketplace APIs (SP-API, Flipkart Seller API), web scraping of competitor listings, price-history feeds (Keepa, CamelCamelCamel).
- **Automation:** mostly **rule-based** (static thresholds/templates); some basic ML for keyword extraction and sentiment; **very little generative AI** for actual content creation.
- **Scope:** almost all are **single-platform** (Amazon XOR Flipkart). Multi-marketplace tools (Unicommerce, EasyEcom) exist but focus on order/inventory ops, not listing intelligence.

### Their structural weaknesses

| Weakness | Our wedge |
|---|---|
| Single-platform lock-in | One interface across Amazon + Flipkart (MVP) and beyond |
| Rule-based, templated content | Generative AI for dynamic titles, descriptions, attributes, images |
| No localization (regional language, festival nuance) | Indian-language + culture-aware content (indic models) |
| Enterprise pricing ($100+/mo) | Freemium / pay-per-listing for small D2C brands |
| Fragmented: sellers run 3–4 tools | Unified dashboard: listing + pricing + ads + review in one place |
| Reactive (repricing responds to drops) | Predictive: demand/styling forecasts, proactive restock and price moves |

### Where we win (the wedge)

1. **Multi-platform by construction** — our adapter pattern (Section 6) treats Amazon and Flipkart as first-class peers, not afterthoughts.
2. **Generative, not templated** — AI proposes content (Section 4), then deterministic rules validate; competitors ship static templates.
3. **Localization** — Indian language and festival-aware content is a gap none of the incumbents fill well.
4. **Affordable for D2C** — freemium/pay-per-listing undercuts the $100+/mo enterprise tools.
5. **All-in-one** — listing + pricing + ads + review reduces tool sprawl.
6. **Predictive layer** — demand/restock forecasting on top of the feedback loop (Core Design Principle).

### What to watch

- **Amazon SP-API roadmap** — if Amazon bakes more AI into its own API surface, third-party listing tools (and us) get squeezed.
- **Flipkart marketplace growth** — more sellers → more third-party tools entering the space.
- **Enterprise repricing/PPC tools** may drop price into the SMB tier to compete.

### Sources / links

- Amazon SP-API docs: https://developer-docs.amazon.com/sp-api/docs
- Amazon Seller Central: https://sellercentral.amazon.com/
- Flipkart Seller Hub: https://seller.flipkart.com/
- Flipkart Seller API: https://seller.flipkart.com/api-docs/
- Helium 10: https://www.helium10.com/
- Jungle Scout: https://www.junglescout.com/
- Sellics: https://sellics.com/
- GoFynd: https://gofynd.com/
- Unicommerce: https://www.unicommerce.com/
- EasyEcom: https://www.easyecom.io/
