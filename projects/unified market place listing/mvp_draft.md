# AI Listing Agent - MVP Draft

## The Goal

Listing products on Amazon and Flipkart is a manual grind. We have to juggle different rules, character limits, and constant rejections for tiny errors. It's slow, it's repetitive, and it's error-prone.

We’re building an **AI Listing Agent** to solve this. It’s a Co-Pilot: the AI does the heavy lifting of drafting, the Rules Engine handles the safety/compliance, and our goal is **auto-approval**—where the system validates the AI's output against strict rules to clear listings automatically whenever possible.

## The MVP Strategy

We aren't trying to build everything at once. We’re going to roll this out in three clear steps:

1.  **The Data Bridge (The CSV Tool):** First, we'll build a tool that takes messy raw data and turns it into perfectly formatted listing files. This gives the user immediate value without needing any API approvals.
2.  **The Connectors:** In parallel, we'll build the actual adapters for the Amazon and Flipkart APIs.
3.  **The Auto-Listing:** Once we get those marketplace approvals, we'll flip the switch to automated listing. At that point, we'll provide the auto-publish and the CSVs simultaneously.

This way, we can start helping people today with the "hard part" (the data) while we work on the "hard plumbing" (the APIs) in the background.

## Onboarding & Auth Flow

### 1. Access Control (Invite-Only)

To maintain a controlled environment during the MVP phase, the platform is **invite-only**.

- **The Entry Point:** Users cannot find a "Sign Up" button. They must receive a unique invite link via email.
- **Account Creation:** Clicking the link takes the user to a "Create Account" page. They provide their email, a password, and the unique invite token (embedded in the URL).
- **Verification:** Once the account is created, the invite token is consumed, and they are redirected to the Onboarding Flow.

### 2. Onboarding Flow

The goal here is to build a high-fidelity profile of the user so the AI can provide better "Brand Voice" context.

**Step 1: Profile Type**

Identify who the user is to tailor the rest of the questions:

- **Agency:** Managing multiple brands for clients.
- **Individual/Freelancer:** Managing their own brand or one-off projects.

**Step 2: User Details**

- **For Individuals:**
  - Username / Company Name
  - Primary Niche (e.g., "Ethnic Wear", "Electronics")
  - Estimated Monthly SKUs
  - How did you hear about us?
- **For Agencies/Freelancers:**
  - Number of brands currently managed
  - Primary industry focus (e.g., "D2C Fashion", "Home Decor")

**Step 3: Brand Context (The "Brain")**

- **Brand Name & Tone:** (e.g., "Playful and Premium" vs "Serious and Technical")
- **Glossary:** Any specific terms they prefer? (e.g., "Kurta Set" instead of "Outfit")
- **Banned Terms:** Any words they absolutely never want to use?
- **Current Pain Points:** What is the biggest issue with your current listing process? (Used for internal feedback).

**Step 4: Marketplace Connection (The "Plumbing")**

Once the profile is set, the user is prompted to connect their accounts:

- **Amazon:** OAuth flow to authorize the SP-API.
- **Flipkart:** OAuth flow to authorize the Seller API.
- *Note: If an account isn't connected yet, they can still use the CSV tool.*

## The Technical Bridge
- **Frontend:** Next.js + Tailwind.
- **Backend:** FastAPI (Python) handling the AI orchestration (LangGraph), Rule Engine, and Database (PostgreSQL + pgvector).
- **Storage:** S3 for images and CSV artifacts.
- **Queue:** Redis + ARQ for handling bulk CSV processing.