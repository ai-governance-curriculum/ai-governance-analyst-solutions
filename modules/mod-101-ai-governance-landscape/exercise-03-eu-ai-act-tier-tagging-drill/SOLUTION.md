# Exercise 03 — EU AI Act Tier Tagging Drill — Reference Solution

## 1. Solution overview

Regulation (EU) 2024/1689 (the "EU AI Act") sorts AI systems into risk tiers
with different obligations. A governance analyst must be able to look at a
use-case description and quickly assign the correct tier, cite the clause,
and state the next-step obligation.

The four risk tiers used by analysts are:

1. **Prohibited practices** (Article 5).
2. **High-risk AI systems** (Article 6 + Annexes I & III, obligations in
   Chapter III).
3. **AI systems subject to transparency obligations** (Article 50) — often
   called "limited-risk".
4. **Minimal- or no-risk** (no specific obligations under the Act; voluntary
   codes of conduct).

A parallel regime applies to **general-purpose AI models (GPAI)** (Chapter V),
including a sub-tier for **GPAI models with systemic risk**. GPAI is a model
classification, not a use-case tier — a deployed GPAI-based product is still
tiered by its use case.

This solution provides a decision procedure, a worked tagging on a canonical
set of use cases, decision rationale per case, validation steps, and a rubric.

## 2. Worked answer or implementation

### 2.1 Decision procedure

For each use case, run these steps in order and stop at the first match:

1. **Prohibited?** Does the use case match any Article 5 practice (e.g., social
   scoring by public authorities, exploitative manipulation, untargeted
   facial-image scraping, emotion recognition in workplace or education except
   medical/safety, biometric categorisation inferring sensitive traits,
   real-time remote biometric ID in public spaces for law enforcement outside
   the narrow exceptions, predictive policing based solely on profiling)?
   → **Tier 1 (prohibited)**. Recommendation: stop; do not deploy.
2. **High-risk under Annex I?** Is the AI a safety component of a product
   already covered by Union harmonisation legislation listed in Annex I
   (machinery, medical devices, toys, in-vitro diagnostic, lifts, etc.) that
   requires third-party conformity assessment? → **Tier 2 (high-risk)**.
3. **High-risk under Annex III?** Does it fall in one of the Annex III
   domains — biometrics; critical infrastructure; education and vocational
   training; employment, workers management, self-employment access; access
   to essential private and public services (incl. credit scoring, health
   and life insurance risk assessment, emergency dispatch); law enforcement;
   migration, asylum, border control; administration of justice and
   democratic processes — **and** is not covered by a narrow Article 6(3)
   derogation (narrow procedural task, improvement of prior human activity,
   detection of decision patterns, preparatory task)? → **Tier 2
   (high-risk)**.
4. **Transparency-triggered?** Article 50 — does the system (a) interact with
   natural persons (chatbots), (b) generate synthetic audio/image/video/text
   (incl. deepfakes), (c) perform emotion recognition or biometric
   categorisation (where otherwise permitted)? → **Tier 3 (transparency)**.
5. **Otherwise** → **Tier 4 (minimal/no risk)**.

Then, separately: **is the underlying model a GPAI model?** If yes, GPAI
provider obligations (Article 53) apply; if the GPAI model also crosses the
systemic-risk threshold (Article 51), additional Article 55 obligations apply.

### 2.2 Worked tagging

| # | Use case description | Tier | Clause anchor | Next-step obligation |
| --- | --- | --- | --- | --- |
| A | HR system that screens and ranks CVs for a hiring team at a European employer. | **Tier 2 — High-risk** | Annex III §4(a) (employment, workers management). | Quality management system, risk management, data governance, logging, human oversight, transparency to deployer, registration in the EU database. |
| B | Public-authority programme that assigns citizens an aggregated "trustworthiness" score based on social behaviour and uses it to decide access to public services. | **Tier 1 — Prohibited** | Article 5(1)(c) social scoring by public authorities. | Do not place on the market; existing deployments must stop. |
| C | Consumer chatbot embedded in a retail website that answers product questions. | **Tier 3 — Transparency** | Article 50(1) — inform users they are interacting with an AI. | Disclose the AI nature in a clear, timely manner; log transparency decision. |
| D | Credit-scoring model used by a bank to decide consumer credit applications in the EU. | **Tier 2 — High-risk** | Annex III §5(b) (creditworthiness / credit score, excluding fraud detection). | Full high-risk provider obligations; conformity assessment before placing on the market. |
| E | Image-filter mobile app that applies stylistic filters (e.g., cartoon effects) to selfies. No biometric categorisation, no deepfake output targeting real people. | **Tier 4 — Minimal risk** | No specific obligation under the Act; voluntary code of conduct encouraged (Art. 95). | Document the classification rationale; monitor for feature changes that could trigger transparency. |
| F | AI model that generates realistic video of real political figures saying things they did not say, for publication online. | **Tier 3 — Transparency (deepfake)** | Article 50(4) — deepfake disclosure obligation. | Clearly label the content as artificially generated/manipulated; preserve the label on repost paths where feasible. |
| G | Real-time facial-recognition system used by police in publicly accessible spaces for routine patrol identification. | **Tier 1 — Prohibited (with narrow exceptions)** | Article 5(1)(h) real-time remote biometric identification in publicly accessible spaces for law enforcement. | Exceptions are narrow (targeted serious-crime search, missing-person, imminent threat); default position is prohibited. Routine patrol does not fit the exception; do not deploy. |
| H | AI triage tool used by a hospital emergency department to prioritise incoming patients. | **Tier 2 — High-risk** | Annex III §5(d) (emergency healthcare dispatch / triage). Also likely a safety component of a medical device under Annex I in some configurations. | High-risk provider obligations; coordinate with the medical-device conformity assessment route. |
| I | Spam filter in a corporate email system that labels inbound messages as spam / not-spam. | **Tier 4 — Minimal risk** | No specific obligation. | Document rationale; keep classification under review as feature set changes. |
| J | Emotion-recognition system used by an employer to monitor call-centre agents' mood during customer calls. | **Tier 1 — Prohibited** | Article 5(1)(f) emotion recognition in workplace or education (outside medical/safety purposes). | Do not deploy for workforce monitoring; redirect to use cases that fall inside the medical/safety carve-out, if applicable. |
| K | A GPAI-based assistant marketed to law firms for general drafting, not tailored to any Annex III use. Deployed via API. The underlying model is widely used and exceeds a systemic-capability threshold. | Product-level: **Tier 3 — Transparency** (chatbot, synthetic content). Model-level: **GPAI with systemic risk** under Chapter V. | Art. 50 at product level; Art. 53 + Art. 55 at model level (provider obligations incl. systemic-risk evaluations, incident reporting, cybersecurity measures). | Transparency labels on product; GPAI provider obligations on the upstream model. |

### 2.3 Decision rationale

- **Tier assignment is per use case, not per model.** The same GPAI model can
  be embedded in a Tier-4 product and a Tier-2 product; obligations attach to
  the actual deployment. Case K illustrates this: the model carries GPAI
  obligations; the product carries transparency obligations because of its
  concrete use.
- **Prohibited beats high-risk beats transparency beats minimal.** The
  procedure stops at the first match because obligations stack top-down;
  misclassifying a Tier-1 case as Tier-2 is the highest-impact error a
  governance analyst can make.
- **Annex III triggers the "safety-component" question too.** If the system
  is also a safety component of a product in Annex I, the high-risk route
  piggybacks on the existing sectoral conformity-assessment regime, not a new
  one. Case H is the textbook example.
- **Article 50 covers four distinct triggers.** Chatbots (user-facing AI),
  emotion recognition / biometric categorisation (where otherwise permitted),
  synthetic content, and deepfakes. Analysts often collapse all of these into
  "chatbot disclosure".
- **GPAI is orthogonal to the use-case tier.** A GPAI model can live under
  Chapter V obligations while its downstream use is Tier 4. Treat GPAI as a
  second axis in the tag.

## 3. Validation steps

1. For every "high-risk" tag, write the Annex III sub-paragraph (e.g.,
   "§4(a)") or the Annex I product category. If you cannot, re-classify.
2. For every "prohibited" tag, write the Article 5 sub-paragraph. If none
   fits cleanly, re-classify as high-risk or transparency.
3. For every "transparency" tag, identify which Article 50 trigger applies
   (chatbot / synthetic content / deepfake / emotion recognition /
   biometric categorisation).
4. Check whether any Article 6(3) derogation might downgrade a high-risk tag.
   If used, document the derogation ground; derogations are narrow.
5. Independently, flag whether the underlying model is GPAI, and whether it
   crosses the systemic-risk threshold.
6. Re-read each case assuming it is **also** processing personal data — note
   whether GDPR obligations stack (they usually do).

## 4. Rubric or review checklist

Score each case out of 4; passing average ≥ 3.0 with **no Tier-1 misses**.

| Score | Criterion |
| --- | --- |
| **4 — strong** | Tier correct; specific clause/sub-paragraph cited; next-step obligation named; GPAI question addressed separately if relevant. |
| **3 — acceptable** | Tier correct; broad clause cited (e.g., "Article 5" without sub-letter); next-step obligation named. |
| **2 — weak** | Tier correct but no clause cited, OR tier off by one step with partial rationale. |
| **1 — poor** | Tier wrong by one step and rationale is thin. |
| **0 — fail** | Prohibited case classified as high-risk or lower (**auto-fail** for the deck). |

Deck-level checks:

- [ ] At least one prohibited case is present and correctly flagged.
- [ ] At least one Annex III and one Annex I high-risk case covered.
- [ ] All four Article 50 triggers (chatbot, emotion/biometric,
      synthetic content, deepfake) appear at least once across the deck.
- [ ] GPAI classification handled as a separate axis, not conflated with the
      tier.
- [ ] No case ignores the "safety component of Annex I product" route.

## 5. Common mistakes

- **Collapsing prohibited into high-risk.** Prohibited practices under
  Article 5 cannot be deployed at all, even with safeguards. Adding "human
  oversight" does not rescue them.
- **Treating "limited risk" as a legal category.** The Act does not use that
  phrase. The obligation is transparency (Art. 50). Labels on diagrams should
  match the Act's vocabulary.
- **Ignoring GPAI.** A model can trigger Chapter V obligations even if the
  downstream product is low-risk. Analysts who tag only the product miss
  upstream obligations.
- **Over-using Article 6(3) derogations.** The procedural / preparatory /
  pattern-detection / improvement-of-prior-human-activity derogations are
  narrow. Default remains high-risk; the derogation must be justified in
  writing.
- **Forgetting sectoral law.** For Annex I use cases (medical devices,
  machinery, etc.) the high-risk conformity route runs through the sectoral
  regulator. The AI Act layers on, it does not replace.
- **Treating Union-level vs. national supervisory authorities as
  interchangeable.** The AI Office (Union level) handles GPAI; national
  market-surveillance authorities handle most product-level enforcement.

## 6. References

- Regulation (EU) 2024/1689 of the European Parliament and of the Council
  of 13 June 2024 laying down harmonised rules on artificial intelligence
  (Artificial Intelligence Act) — Articles 5, 6, 50, 51, 53, 55; Annexes I
  and III.
- European Commission, _AI Act_ overview and timeline pages — used for the
  staggered applicability dates. <https://digital-strategy.ec.europa.eu/en/policies/regulatory-framework-ai>
- NIST, _AI Risk Management Framework 1.0_ — cross-reference for risk
  vocabulary used in the rationale. <https://www.nist.gov/itl/ai-risk-management-framework>
