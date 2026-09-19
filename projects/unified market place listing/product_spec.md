# Product Spec: AI Listing Agent

This document defines the product. What it is, who it is for, and what we will not build. It does not describe the implementation, does not name a phase, and carries no version number.

Where code and this document disagree, fix both in the same pull request.

---

## What we are building

An AI listing operator for D2C brands selling on Indian marketplaces. The seller connects their accounts or uploads a spreadsheet. The system drafts marketplace-ready listing content, a deterministic rules engine validates it, a human approves it, and adapters publish it to Amazon and Flipkart.

Today the seller writes every title, bullet, and description by hand, guesses at category rules, and absorbs rejections. We replace that with a pipeline that drafts, checks, and publishes.

## The problem

Listing a product on Amazon and Flipkart is manual work with a cost nobody measures.

1. Every marketplace has its own schema. Amazon wants browse nodes, item type keywords, and roughly two hundred attributes per category. Flipkart wants its own category tree, brand approvals, and handling fee fields. Neither resembles the other.
2. Content has to be rewritten per platform. A title that passes on Flipkart can be rejected by Amazon for length, for a banned superlative, or for a missing mandatory attribute.
3. Volume multiplies the work. A shirt is not one listing. It is a product with size and color variants, each needing a SKU, a price, stock, dimensions, and a weight. A hundred-SKU catalog becomes several hundred listing rows.
4. The feedback is slow. A rejection arrives days later with an unhelpful error string. The operator fixes one field, resubmits, and waits again.

The result is operator time burned per SKU and a first-pass rejection rate that stays high because nothing catches the errors before submission.

## Who uses it

| User | What they do | What they need from us |
| :--- | :--- | :--- |
| D2C brand owner or e-commerce manager | Owns the catalog, is not technical, will not read marketplace rulebooks | A single interface that produces compliant listings without the documentation |
| Listing operator or catalog team | Runs bulk uploads and lives in the tool all day | Speed, consistency, keyboard-friendly review, per-row error visibility |
| Reviewer or approver | Signs off before anything goes live | Trust, auditability, and the ability to reject with feedback |

Agencies manage multiple brands per client, so the product is multi-tenant from the start. A freelancer with one brand uses the same interface with a single workspace.

## Operating principle

AI proposes, deterministic rules validate, humans approve, adapters execute, performance feeds back.

The rules engine is the final arbiter. Nothing an LLM produces reaches a marketplace without passing it. This is the trust model, not a preference. Generation is probabilistic, validation is not, and the seller's account health depends on the difference.

Two consequences follow from that principle and they constrain everything downstream. First, every failure has to come back as a structured, field-level error the correction agent can act on, because a wall of prose cannot be corrected automatically. Second, the human approval step stays in place until the acceptance rate proves the rules engine catches what matters.

## The pipeline

The pipeline runs the same way whether it was triggered by one product or a ten-thousand-row spreadsheet.

1. Connect. The seller authorizes Amazon and Flipkart. Tokens are encrypted at rest and refreshed automatically.
2. Import. A CSV or Excel upload, manual entry, or a catalog sync. Raw rows are parsed into a canonical product model.
3. Generate. Agents draft the title, bullets, description, keywords, and attributes, and inspect the product images. Brand voice rules are retrieved and injected into every prompt.
4. Validate. The deterministic rules engine and the image checks return a pass or a structured failure with field-level reasons.
5. Approve. A human reviews the output. A rejection carries feedback back to the agent, which regenerates only the fields that failed.
6. Publish. The adapter maps the canonical model to the marketplace payload and pushes it, reporting results per field.
7. Learn. Performance data flows back and informs later optimization.

The correction loop is what makes the middle of that pipeline work. A failed validation does not bounce back to the user. It goes to the agent that produced the failing field, and the agent gets the exact reason. Only after repeated failure does the job stop and surface to a human.

## Access and onboarding

Access is invite-only. There is no public signup button.

An invite is created for a recipient and emailed with a unique token. The recipient lands on an account creation page, provides an email and password, and the token is consumed on submission. They are then pushed into onboarding.

Onboarding collects the profile in four steps.

1. Profile type. Agency or individual. This changes the remaining questions and the workspace shape that gets created.
2. User details. Individuals give a display name or company name, a primary niche, an estimated monthly SKU count, and how they heard about us. Agencies and freelancers give the number of brands managed and their industry focus.
3. Brand context. Brand name, tone, preferred glossary terms, banned terms, and current pain points. The pain points field is internal feedback, never a model input.
4. Marketplace connection. The user is prompted to connect Amazon and Flipkart. Connecting is optional. A seller with no connection can still import, generate, and download finished listing files.

The brand context captured in step 3 is the reason onboarding is four steps and not two. It is the material that later makes the generated copy sound like the brand instead of like a model.

## What we deliberately do not build

- A manual field-by-field editor. If the user types every field, it is not an agent.
- Real-time inventory or order synchronization. That is a different product with a different buyer.
- Image retouching. We flag problems and suggest fixes. We do not edit pixels.
- Our own marketplace taxonomy. We read and map theirs.
- Any path that publishes without human approval before the acceptance rate justifies it.

## How we will know it works

The baseline gets measured before launch, or these numbers mean nothing.

| Measure | What it tells us |
| :--- | :--- |
| Minutes per listing, before and after | Whether we removed the manual work or just moved it |
| First-pass acceptance rate on submission | Whether the rules engine catches real rejections |
| Rejections grouped by error code | Which rules are missing and which agent keeps failing |
| SKUs processed per operator per day | The throughput gain that justifies the price |
| Cost per listing generated | Whether model routing is doing its job |

## Why this design holds up

Reliability comes from the rules engine being non-negotiable. The model can propose anything, but nothing ships without passing deterministic checks, and every failure arrives as an actionable field-level error rather than a paragraph.

Marketplace independence comes from a canonical model plus adapters. Amazon and Flipkart are peers with equally complete implementations rather than one platform plus a bolt-on. Adding Myntra or Ajio is a new adapter, not a rewrite.

Defensibility comes from the feedback loop. Brand voice rules, approval history, and performance data accumulate per brand, and they get more useful the longer a brand stays.

Cost stays predictable because generation is event-driven. Tokens are spent when a listing is created or corrected, not continuously.