## Plans

| Plan | Price | Positioning (who it's for) | Notes |
|---|---|---|---|
| Free | ₹0 | Trial — hobby sellers validating the tool on one marketplace | 5 listings/mo, Amazon only, no card |
| Starter | ₹999/mo | Solo D2C founders on 1–2 marketplaces | 30 listings/mo, Amazon + Flipkart, CSV import, AI text gen |
| Pro | ₹2,999/mo | Growing brands with 100+ SKU catalogs | 150 listings/mo, + image compliance, priority queue, full analytics |
| Enterprise | Custom (₹9,999+/mo) | Agencies & multi-brand large catalogs | Unlimited, SSO, API, custom adapters, dedicated support |

---

## Function → Plan mapping

| Function | Free | Starter | Pro | Enterprise |
|---|---|---|---|---|
| Marketplaces | Amazon only | Amazon + Flipkart | Amazon + Flipkart | Both + custom adapters |
| Listings / month | 5 | 30 | 150 | Unlimited |
| CSV bulk import | — | ✓ | ✓ | ✓ |
| AI text generation | ✓ (limited) | ✓ | ✓ | ✓ |
| Image compliance pipeline | — | — | ✓ | ✓ |
| Approve & publish | Preview only | ✓ | ✓ | ✓ |
| Analytics | Basic | Basic | Full | Full + API export |
| Support | Community | Email | Priority | Dedicated + SSO |

---

## Our costs (what it takes to build & run this)

| Item | Cost | Notes |
|---|---|---|
| EC2 (dev + worker) | ₹3,750/mo | t3.medium + t3.small |
| RDS PostgreSQL | ₹1,500/mo | db.t3.micro, 20 GB |
| ElastiCache Redis | ₹1,200/mo | For queue |
| S3 + egress | ₹250/mo | Images + CSVs |
| LLM (GPT-5.4 mini) | ₹1,500–4,000/mo | ~2k listing generations + image checks + embeddings |
| **Total (infra + AI)** | **₹8,000–11,000/mo** | Ballpark, MVP phase |

---

## MVP design & approach

### What the MVP is

Amazon + Flipkart — two adapters proving the adapter pattern generalizes. One pipeline: create → generate → validate → approve → publish.

### Scope — in vs out

- In:
  - Amazon SP-API + Flipkart Seller API adapters
  - CSV bulk import
  - AI generation (text)
  - Validation with structured errors
  - Human approval gate
  - Publish
  - combined analytics
- Out (deferred to Phase 2/3):
  - Version history
  - Sales attribution
  - Optimization agent (auto-suggest improvements)
  - Competitive signals
  - Low-risk auto-publish

### The four layers

| Layer | Responsibility | Components |
|---|---|---|
| Experience | User-facing dashboard | Next.js app — connect, import, review, approve, analytics |
| Intelligence | AI generation & retrieval | LangGraph agents — content, keywords, attributes, images, brand-voice |
| Control | Deterministic validation & approval | Rules engine, structured error contracts, human approval gate |
| Execution | Marketplace operations | Amazon SP-API + Flipkart Seller API adapters |

Core loop: **AI proposes → deterministic rules validate → humans approve → adapters execute → performance feeds back.**

### MVP phases (from HLD §12)

| Phase | Deliverables |
|---|---|
| Phase 1 | Amazon + Flipkart adapters, CSV bulk import, generation (text + image), validation with structured errors, approval, publish, basic analytics |
| Phase 2 | Version history, image pipeline refinements, unified analytics dashboard, sales attribution, performance monitoring |
| Phase 3 | Optimization agent (auto-suggest improvements), competitive signals, low-risk auto-publish (confidence threshold + consecutive-approval unlock) |

### MVP operating cost (from HLD §11)

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
