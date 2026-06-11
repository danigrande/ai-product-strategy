# Cost Curve & Pricing Strategy

## Cost Model

Current MVP

| Cost Category | Per-User/Month | Notes |
|--------------|----------------|-------|
| Inference (primary model) | 0$  |  Llama-3.1-8B-Instant Free tier on Groq |
| Inference (cascading/triage) | 0$ | Llama-3.1-8B-Instant Free tier  on Groq  |
| Infrastructure | 0$ | Free tier Render |
| Data/storage | 0$ | MongoDB Atlas Free tier |
| Human-in-the-loop | 2000$ | Very difficult to tell, the cost will decrease with volume of users |
| **Total AI COGS** | 2000$ | Very dependable on scalability |

If went "enterprise" pricing - Assuming 100k users

| Cost Category | Per-User/Month | Notes |
|--------------|----------------|-------|
| Inference (primary model) | 500$  |  $0.0005 x100.000 users Llama-3.1-8B-Instant |
| Inference (cascading/triage) | 500$ | $0.0005 x100.000 users Llama-3.1-8B-Instant  |
| Infrastructure | 499/mo$ | Render |
| Data/storage | $387.62/month | MongoDB Atlas |
| Human-in-the-loop | 10.000$ | 5 people for support @2000 USD per person |
| **Total AI COGS** | 11.886,62$ | Very dependable on scalability |

## Cascading Strategy
<!-- Cheap model → frontier model routing logic -->

**Triage model:**
**Frontier model:**
**Routing rule:**
**Expected cascade ratio:**

## Pricing Model

**Current pricing:**
**Proposed AI pricing:**
**Model:** seat-based / usage-based / outcome-based / hybrid

## Stress Tests

| Scenario | Impact on Margin | Response |
|----------|-----------------|----------|
| Inference costs 3x | | |
| Heaviest segment doubles | | |
| Model provider raises prices 50% | | |

## Board One-Pager
<!-- Before/After: Old SaaS revenue vs. AI usage revenue for your product -->

**Before (traditional SaaS):**
**After (AI-enabled):**
**Net margin shift:**
