## Board Pitch

**Thesis (1 sentence):**
We are building the default World Cup prediction experience for Spanish-speaking WhatsApp groups by turning passive tournament participation into a daily social game anchored by culturally-native AI personalities.

**The case:**

1. **Why now:** The World Cup creates a fixed, high-intensity engagement window that cannot be replicated later. At the same time, users have become comfortable interacting with AI-generated content inside consumer products, while distribution increasingly happens inside messaging groups rather than standalone social networks. The opportunity is not to build another prediction engine; it is to create a daily engagement layer around tournament participation before incumbents adapt their products for this audience. We will know within the first week of the tournament whether personality-driven engagement materially increases return usage.

2. **What's defensible:** The moat is not the prediction mechanic. It is the combination of culturally-specific personalities, multilingual content generation, tournament-specific scoring logic, and a growing evaluation dataset built from real user interactions. A competitor can copy the feature set quickly. Replicating the quality layer, personality consistency, language adaptation, and correction history is harder. That said, this is a weak-to-moderate moat today. The current Data Flywheel score is 10/20 and the strategy correctly acknowledges that fine-tuning is not a near-term advantage. Defensibility in the next 12 months depends on compounding quality signals faster than competitors, not on proprietary models.

3. **The economics:** At $4.99 per group per tournament, projected AI cost is approximately $0.30–0.50 per paying group, producing gross margins above 90% on pass revenue. Even under a blended paid-plus-ad model, margins remain attractive at roughly 55–65%. The economic question is not serving cost; it is conversion and retention. The business becomes attractive if we can convert approximately 10% of groups and maintain engagement through multiple tournaments. The primary financial risk is not inference cost. It is acquiring enough active groups to recover infrastructure investment before tournament demand disappears.

**The risks:**

1. **Trust / failure modes:** The most likely reputation-damaging failure is not a factual error; it is culturally inappropriate, mixed-language, or low-quality personality content being surfaced at scale. The system mitigates this through response evaluation, retry logic, human review escalation, and a golden dataset. However, the reliability contract remains partially manual. The current dataset is too small for strong statistical confidence, confidence indicators are not user-facing, and drift detection is not automated. This is acceptable for launch but not for sustained scale.

2. **Scale / governance:** The product does not currently break because of model cost; it breaks because of infrastructure limits and operational maturity. At 10x usage, the current serving architecture becomes a bottleneck, governance processes become manual, and moderation gaps become material. The self-hosted inference path is the critical scale dependency. Governance gaps include the absence of automated evaluations, incomplete GDPR readiness, no age-gating despite youth appeal, limited moderation tooling, and no production-grade escalation framework.

3. **Competitive:** The scenario that forces a kill is not a direct startup competitor. It is failure to demonstrate post-tournament retention before platform or fantasy incumbents incorporate similar functionality. If users do not return for simulations, league play, or future competitions after the World Cup, then the product remains an event-driven novelty rather than a durable business. The core Horizon 2 retention hypotheses are therefore existential, not incremental.

**The ask:**
Fund a six-month validation period with a maximum downside capped at €2,000 in additional losses beyond current commitments, one full-time founder/operator, and focused execution on retention and scale readiness rather than new feature expansion. Success is defined by proving three things: (1) users return daily during the tournament, (2) at least 10% of groups convert to a paid pass, and (3) meaningful post-tournament retention exists through simulations, league support, or adjacent prediction experiences. If funded, exploratory initiatives such as custom personality creation and global leaderboards remain paused until retention is validated.
