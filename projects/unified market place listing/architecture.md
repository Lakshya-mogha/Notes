# Architecture: AI Listing Agent

How the system is built. Data model, module boundaries, contracts, adapters, and stack.

This file describes what the system does, not what is finished. Scope decisions live in `v1.md`. Product intent lives in `product_spec.md`.

Token budget note for agents: this file is meant to be read whole. It is roughly 8k tokens.

---

## System layers

Four layers, each with one responsibility.

| Layer | Responsibility | Components |
| :--- | :--- | :--- |
| Experience | User-facing dashboard | Next.js app for connect, import, review, approve, analytics |
| Intelligence | AI generation and retrieval | LangGraph agents for content, keywords, attributes, images, brand voice |
| Control | Deterministic validation and approval | Rules engine, structured error contracts, human approval gate |
| Execution | Marketplace operations | Amazon SP-API and Flipkart Seller API adapters |

```
┌─────────────────────────────────────────────────────────────┐
│  EXPERIENCE   Next.js dashboard                             │
│               connect, bulk import, review, approve,        │
│               analytics                                     │
├─────────────────────────────────────────────────────────────┤
│  INTELLIGENCE LangGraph agents                              │
│               content, keyword, attribute, image,           │
│               brand voice retrieval                         │
├─────────────────────────────────────────────────────────────┤
│  CONTROL      deterministic rules engine                    │
│               structured error contracts                    │
│               human approval gate                           │
├─────────────────────────────────────────────────────────────┤
│  EXECUTION    Amazon SP-API adapter                         │
│               Flipkart Seller API adapter                   │
│               canonical model to marketplace payload        │
└─────────────────────────────────────────────────────────────┘
```

A request flows down through the layers and results flow back up. No layer skips another. The rules engine sits between the model and the marketplace, and the adapter sits between the rules engine and the network.

## Agent graph

```
START
  │
  ▼
LOAD      product data, marketplace rules for the target category,
          brand context via vector retrieval
  │
  ▼
GENERATE  text agents and image agent run in parallel
  │
  ▼
STRUCTURED LISTING   canonical JSON
  │
  ▼
VALIDATE  rules engine plus vision checks
  │
  ├── FAILED
  │     │
  │     ▼
  │   CORRECTION   the failing agent receives exact field-level errors
  │     │
  │     ▼
  │   retry, bounded at 3 attempts
  │     │
  │     └── still failing after 3 ──► job blocked,
  │                                    exact reason surfaced to the user
  │
  ▼ PASSED
QUALITY CHECK   confidence scoring
  │
  ▼
HUMAN APPROVAL
  │
  ▼
PUBLISH via the marketplace adapter
```

### Retry policy

| Failure type | Handling |
| :--- | :--- |
| Rules engine failure on generated content | Return the error report to the specific agent. Allow up to 3 correction attempts, then block the job and surface the reason |
| Rate limit from a marketplace | Exponential backoff inside the adapter. The agent is not involved |
| Expired auth token | Refresh once and retry the call. If it fails again, escalate to the user and mark the connection as needing attention |
| Partial publish success | Retry only the failed fields. Never re-push a listing that partially succeeded. Idempotency keys on the marketplace side |
| Invalid model output | JSON schema validation and repair before persistence. Two consecutive validation failures escalate that agent run to the higher model tier |

Retry counts are properties of the graph, not of the prompt. Changing them is a config change and a test change, not a rewrite.

### Batch mode

A bulk import fans out into parallel agent runs with bounded concurrency per marketplace, so we stay under rate limits. Each row validates and publishes independently. A failing row does not stop the batch.

When the batch finishes, the user gets a summary with counts and a per-row result table, and can retry individual failed rows with the same input. Every row keeps its own error report, so a retry never requires reconstructing context.

## Modules

Each row is a real boundary in the codebase, with an explicit input and output. Work can proceed in parallel across them.

| Module | Responsibility | Input | Output |
| :--- | :--- | :--- | :--- |
| Auth and identity | Invite-only access, account creation, JWT sessions, password handling | Invite token, credentials | User session |
| Marketplace auth | OAuth flows for Amazon and Flipkart, token encryption, refresh scheduling | OAuth codes, stored tokens | Encrypted tokens, connection status |
| Data bridge | Parse CSV and Excel, extract entities from messy text, map to the canonical model | Raw CSV or text | Canonical product JSON |
| AI generation | LangGraph agents for content, keywords, attributes, and images, conditioned on brand context | Canonical JSON, brand context | Drafted listing content |
| Validation | Deterministic rules engine and vision checks, structured error output | Drafted content, images | ErrorReport or pass |
| Execution | Map the canonical model to a marketplace payload, call the API, parse per-field responses | Canonical JSON, validated content | Publication result |
| Media management | S3 storage, image and video handling, signed URLs, CSV artifacts | Images, videos, CSVs | Stored assets, URLs |
| Web UI | Dashboard, onboarding, bulk import, review workspace, analytics | User interaction | UI state, API calls |
| Data and persistence | PostgreSQL schema, migrations, pgvector indexes, repository layer with tenant scoping | Raw data, files | Persistence, retrieval |
| Task orchestration | Redis and ARQ for bulk jobs and agent runs, retries, job status | Task requests | Job status, completion |

The dependency direction matters. The data bridge does not know about marketplaces. The agents do not know about HTTP. The adapters do not know about prompts. Business rules live in the validation module, not scattered across agents.

## Generation responsibilities

| Agent or service | Input | Output | Notes |
| :--- | :--- | :--- | :--- |
| Content agent | Product data, brand voice chunks, marketplace rules | Title, bullets, description | Highest token spend in the system |
| Keyword agent | Category, competitor signals, product attributes | Search terms, backend keywords | Feeds the SEO score |
| Attribute agent | Product data, marketplace category template | Structured attributes for size, color, material, and the rest | Must match the marketplace category schema exactly |
| Image agent | Product images | ImageReport per image per slot | Vision model plus deterministic checks |
| Optimization agent | Performance metrics on live listings | Proposed edits | Runs on a schedule, never in the create path |
| Rules engine | Any generated output | ErrorReport or pass | Deterministic code. Never an LLM |

The rules engine is the final arbiter. No LLM output ships without passing it.

## Data model


## Brand voice

The brand's own rules are chunked, embedded, and retrieved at generation time so the output sounds like the brand rather than like a generic model.

| Artifact | How it is chunked | Example |
| :--- | :--- | :--- |
| Brand voice guide | Per rule or tone clause | Playful, not corporate. Address the reader as "you" |
| Style preferences | Per guideline | No exclamation marks. Bullets start with a verb |
| Glossary | Per term | Say "kurta set", never "kurta outfit" |
| Past approved listings | Per listing field | High-performing titles and bullets from previous work |
| Compliance preferences | Per rule | Never claim "organic" without a certificate on file |

Embeddings use `text-embedding-3-small` and live in pgvector alongside the relational data. At generation time we embed the request context, run a cosine similarity search with top k equal to 8, and inject the retrieved chunks into the prompt as brand voice context.

One database for both relational and vector data keeps the MVP operationally small. pgvector cosine similarity over the `<=>` operator handles top-k retrieval at this scale, which is thousands of brand chunks rather than millions. Move to a dedicated vector store only if latency or scale demands it.

Retrieval returns a contract like this.

```json
{
  "brand_id": "brand_123",
  "retrieval_query": "organic cotton ethnic wear, playful premium voice",
  "retrieved_chunks": [
    {"chunk_id": "bv_01", "type": "tone_rule", "text": "Playful but premium", "score": 0.89},
    {"chunk_id": "bv_12", "type": "glossary", "text": "Say kurta set, not outfit", "score": 0.84}
  ],
  "embedding_model": "text-embedding-3-small",
  "top_k": 8
}
```

## Image compliance

Marketplaces reject listings for image violations quietly and late, so image checks run before human approval, in parallel with text generation.

| Check | Amazon | Flipkart | Method |
| :--- | :--- | :--- | :--- |
| Main image background | Pure white, RGB 255 255 255 | White or light | Pixel analysis confirmed by the vision model |
| Minimum resolution | 1000x1000 for zoom | 500 minimum, 1000 recommended | Deterministic, PIL and EXIF |
| Product fill ratio | At least 85 percent of frame | Not specified | Vision model |
| Text overlays and watermarks | Not allowed on the main image | Restricted | Vision model |
| Logo and branding | Only the product's own logo on the product | Same | Vision model |
| Aspect ratio | 1:1 for main, varies by slot | 1:1 and 3:4 | Deterministic |

```
Upload to S3
   │
   ▼
Deterministic checks   resolution, format, aspect ratio
   │
   ▼
Vision model review    background, fill, text, watermark
   │
   ▼
Structured ImageReport per image per marketplace
   │
   ├── pass ──► attach to the listing
   └── fail or warn ──► surface the issue and a suggested fix
```

Split the checks deliberately. Resolution, format, and aspect ratio are exact and cheap, so they run as code with no model call. Background, fill, and text detection need judgment, so they run through the vision model. Running a vision model on an 800x800 image wastes a call to learn something PIL already knows.

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
      "detail": "Background is RGB 248 250 249, not pure white",
      "suggestion": "Replace the background with pure white or run background removal"
    },
    {
      "type": "resolution",
      "severity": "error",
      "detail": "Image is 800x800, minimum for zoom is 1000x1000",
      "suggestion": "Re-export at 1000x1000"
    },
    {
      "type": "text_overlay",
      "severity": "warning",
      "detail": "Small text detected in the corner",
      "suggestion": "Move text to a secondary image slot"
    }
  ]
}
```

## Error contract

Every failure, whether from the rules engine, the vision model, or a marketplace API, returns the same envelope. The correction agent reads this structure directly, so it stays machine-readable at every boundary.

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
      "message": "Title is 221 characters, Amazon limit is 200",
      "value_submitted": "Organic Cotton Kurta Set ...",
      "suggestion": "Shorten to 200 characters or fewer and move the detail into bullets",
      "source": "rules_engine",
      "retryable": true,
      "marketplace": "amazon"
    },
    {
      "field": "bullets[2]",
      "error_code": "BANNED_TERM",
      "message": "Contains 'best', which is restricted on Flipkart",
      "value_submitted": "Best quality fabric",
      "suggestion": "Replace with a factual claim such as 'Premium quality fabric'",
      "source": "rules_engine",
      "retryable": true,
      "marketplace": "flipkart"
    },
    {
      "field": "main_image",
      "error_code": "API_REJECTED_PARTIAL",
      "message": "Amazon accepted the listing but rejected the main image for background color",
      "value_submitted": "s3://images/img_123.jpg",
      "suggestion": "Re-run through the image pipeline with background removal",
      "source": "amazon_adapter",
      "retryable": true,
      "marketplace": "amazon",
      "api_error_detail": "Amazon SP-API: IMAGE_NOT_COMPLIANT"
    }
  ]
}
```

| Source | Example codes | Retryable |
| :--- | :--- | :--- |
| `rules_engine` | `FIELD_TOO_LONG`, `BANNED_TERM`, `MISSING_ATTRIBUTE`, `CATEGORY_MISMATCH` | Yes, the agent can correct it |
| `vision_model` | `BACKGROUND_OFF`, `RESOLUTION_LOW`, `TEXT_DETECTED`, `WATERMARK` | Yes, the image can be reprocessed |
| `amazon_adapter` | `API_REJECTED_PARTIAL`, `RATE_LIMITED`, `AUTH_EXPIRED`, `SKU_CONFLICT` | Depends. Rate limit and auth are retryable, SKU conflict needs the user |
| `flipkart_adapter` | `API_REJECTED_PARTIAL`, `RATE_LIMITED`, `AUTH_EXPIRED`, `CATEGORY_NOT_MAPPED` | Same logic |

The `field` value uses a dotted path so the correction agent can write back to the exact location. `bullets[2]` is a valid target. So is `variants[0].attributes.color`. Keep the path syntax consistent across the rules engine, the vision checks, and the adapter error mapping, or the agent will receive errors it cannot place.

## Marketplace adapter pattern

Every marketplace implements one interface. Amazon and Flipkart are peers with equally complete implementations, never one platform plus an afterthought.

```python
class MarketplaceAdapter(ABC):
    async def get_listing(self, sku: str) -> ListingResult
    async def create_listing(self, listing: CanonicalListing) -> ListingResult
    async def update_listing(self, listing: CanonicalListing) -> ListingResult
    async def publish_listing(self, listing: CanonicalListing) -> ListingResult
    async def get_performance(
        self, listing_id: str, from_date: date, to_date: date
    ) -> PerformanceResult
```

Rules for every adapter.

1. Every method returns a `ListingResult` carrying per-field success and failure. Never a boolean.
2. On partial success, return exactly which fields failed plus the marketplace's raw error string. The rules engine consumes that to decide retry versus block.
3. The adapter owns auth refresh, rate limiting, idempotency, and mapping marketplace errors into the ErrorReport taxonomy.
4. The canonical model to marketplace payload mapping happens inside the adapter. Nothing else touches a raw marketplace API.

Marketplace specifics. Amazon SP-API uses the website authorization workflow over OAuth 2.0 and the listings items endpoints for creation and update. It requires request batching and per-endpoint rate limit handling. Flipkart's Seller API uses v3.0 API key plus signature authentication and requires explicit brand and category mapping to Flipkart's internal taxonomy before a product can be pushed.

## Security and multi-tenancy

| Concern | Design |
| :--- | :--- |
| App authentication | JWT through self-hosted NextAuth |
| Marketplace authentication | OAuth 2.0 for Amazon SP-API and Flipkart Seller API |
| Token storage | AES-256 encrypted at rest |
| Tenant isolation | `tenant_id` required on every query and enforced at the repository layer, not in application code |
| Roles | Owner, operator, reviewer, admin |
| Approval integrity | The generator cannot approve its own listing. Maker-checker separation |
| Auditability | Full audit trail on every action, including rejections and reject reasons |
| Transport | TLS everywhere |
| Secrets | AWS Secrets Manager |

Tenant scoping belongs in the repository layer rather than in each query, because a single missed `where workspace_id = ?` leaks another brand's catalog. Make the unscoped query impossible to write rather than relying on reviewers to catch it.

## Async processing and reliability

| Problem | Solution |
| :--- | :--- |
| Bulk uploads time out if handled synchronously | Redis queue with ARQ for batch jobs, status tracked per job |
| Marketplace APIs are slow | All marketplace calls are async and never block the request cycle |
| Marketplaces reject on rate limits | Exponential backoff with per-marketplace throttling in the adapter |
| Model output is not guaranteed to be valid JSON | Pydantic and JSON schema validation with repair before persistence |
| Network failures mid-publish | Idempotency keys on every marketplace mutation |
| Failed jobs lose their context | Every agent run stores its ErrorReport, and failed jobs are re-runnable with identical input |

## Tech stack

| Layer            | Choice                        | Notes                                                           |
| :--------------- | :---------------------------- | :-------------------------------------------------------------- |
| Frontend         | Next.js, TypeScript, Tailwind | Dashboard, review workspace, analytics                          |
| Auth             | NextAuth, self-hosted         | JWT sessions                                                    |
| Backend          | FastAPI, Python               | Async, typed, OpenAPI docs generated                            |
| AI orchestration | LangGraph                     | Agent graph with bounded retries                                |
| Embeddings       | text-embedding-3-small        | Brand voice retrieval                                           |
| Vector store     | pgvector                      | Same database as the relational data                            |
| Database         | PostgreSQL                    | Managed instance, pgvector extension enabled                    |
| Queue            | Redis with ARQ                | Background jobs                                                 |
| Storage          | S3 compatible                 | Images, CSVs, batch artifacts                                   |
| Observability    | Sentry and OpenTelemetry      | Errors, traces, token and cost logs                             |
| Hosting          | AWS                           | EC2 for app and worker, RDS for Postgres, ElastiCache for Redis |

### Model routing

Pick the cheapest model that clears the quality bar for the task. Log `model_used`, input tokens, output tokens, and cost on every agent run. That log is the cost observability, and it is also how we learn which prompts are worth their tokens.

1. Default route is GPT-5.4 mini for text and vision.
2. If output fails JSON validation twice, escalate that agent run to the higher tier once.
3. Text-only tasks on a constrained budget route to DeepSeek-V3 through the same abstraction.
4. Embeddings always use the embedding model. Never run a generation model for retrieval.

## Conventions agents must follow

1. The rules engine is never an LLM call. If a check can be written as code, it is code.
2. Nothing reaches an adapter without passing validation. There is no bypass flag, not even in tests.
3. Every agent run writes an audit row with model name, token counts, cost, and its error report if it failed.
4. Structured output only. Parse model responses into a Pydantic model before any other code reads them.
5. Errors use the shared envelope. Do not invent a second error shape in a module.
6. Tenant scoping happens in the repository layer, never in a caller.
7. Adapters own their marketplace's quirks. Do not leak an Amazon field name into shared code.