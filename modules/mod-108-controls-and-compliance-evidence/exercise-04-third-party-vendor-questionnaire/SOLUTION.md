# Exercise 04 — Third-Party Vendor Questionnaire — Reference Solution

## 1. Solution overview

Most production AI capability inside an organisation is bought, not
built. Foundation models arrive through API vendors; fine-tuned
components arrive through specialist vendors; feature- and data-
vendors feed the pipeline; downstream SaaS products embed AI with
whatever contracts they attach. From a governance perspective each
of these is an **extension** of the organisation's control surface:
the duty under NIST AI RMF `GOVERN 6.*`, under EU AI Act Art. 25
(obligations along the AI value chain), and under ISO/IEC 42001
clause 8.1 does not stop at the vendor boundary.

A third-party AI vendor questionnaire is the analyst's primary
evidence instrument for closing that extension. Done well, it:

- Lets the organisation satisfy its own obligations when a regulator
  asks "what did you know, and when?";
- Gives procurement and legal the facts they need to shape MSAs,
  DPAs, and AI-specific contractual annexes;
- Surfaces the real deltas between vendors so that selection is
  grounded in evidence, not pitch decks.

This solution provides:

- A tiering model (what level of diligence applies to which vendor).
- A questionnaire template (eight sections, ~45 questions) covering
  governance, data, model, security, evaluation, incidents,
  compliance, and change management.
- Scoring guidance (how to read an answer, what to flag).
- A worked response with annotations for one tier-1 LLM API vendor
  and one tier-2 specialised feature provider.
- Decision rationale and a grading rubric.

Scope: this is a design + implementation exercise. The questionnaire
is the governance analyst's draft; procurement and legal own the
contractual counterpart.

## 2. Worked answer or implementation

### 2.1 Vendor tiering

Not every vendor warrants the full questionnaire. Tier by what the
vendor touches:

| Tier | Trigger | Diligence |
|---|---|---|
| **T1 — Core AI capability** | Vendor supplies the model (LLM API, foundation model, specialised ML model) or the training data. | Full questionnaire; annual re-attestation; right to audit in the MSA. |
| **T2 — AI-supporting service** | Vendor supplies a component embedded in the AI pipeline (vector database, labelling platform, feature store, moderation/guardrail service). | Full questionnaire minus the "model development" section; annual re-attestation. |
| **T3 — AI-embedded SaaS consumed internally** | Vendor supplies a SaaS product with AI features (meeting-summariser, in-CRM assistant, copilots). | Short-form questionnaire (sections 1, 4, 6, 7); re-attestation on major version change. |
| **T4 — Non-AI supplier that may use AI on our data** | Vendor is not pitched as AI but has introduced AI features into their product. | Trigger a T3 pass on the AI feature only; otherwise standard-vendor process. |

Decide the tier **before** sending the questionnaire. A tier
decision is a one-line record in the vendor register with the
rationale.

### 2.2 Questionnaire template

The questionnaire is grouped into eight sections. Each question has
a short text answer and, where asterisked, an evidence pointer.
Vendor must include a signed attestation; the questionnaire is
dated and tied to a specific product version.

#### Section 1 — Vendor identity and scope (all tiers)

1. Legal entity name, registered address, and country of incorporation.
2. Primary product name and current version in scope.
3. Named points of contact: (a) commercial / account; (b) security;
   (c) legal; (d) AI-governance / compliance.
4. Which of the following vendor roles apply under EU AI Act Art. 25?
   `provider` / `deployer` / `importer` / `distributor` /
   `authorised-representative` / `not-applicable` — with rationale.
5. In which jurisdictions do you host production services? List
   regions and the data they hold.

#### Section 2 — AI governance posture (all tiers)

6. Describe your internal AI governance framework. Reference NIST AI
   RMF, ISO/IEC 42001, and any other frameworks you align to. *(Evidence)*
7. Do you maintain an ISO/IEC 42001 AIMS certification or a
   comparable programme? If yes, provide the certificate and the
   current scope statement. If no, provide an equivalent attestation
   of control implementation. *(Evidence)*
8. Who inside your organisation has executive accountability for AI
   policy? (Role, not name.)
9. How do you handle AI-specific risks in your enterprise risk
   register? Share a redacted sample of a risk entry. *(Evidence)*
10. How often is your AI policy reviewed, and when was it last
    reviewed? *(Evidence)*

#### Section 3 — Data governance (T1, T2)

11. For training and fine-tuning:
    a. What is the provenance of the training data? Public web?
       Licensed third-party? Customer data? Synthetic?
    b. Do you use any data under opt-out or licensing restrictions?
       Which?
    c. Do you honour published robots.txt and `ai.txt` signals? From
       what date?
12. Do you train on customer prompts, completions, embeddings, or
    tool-call traces by default? Describe the opt-in / opt-out
    posture per plan tier. *(Evidence: contract language)*
13. What is your data-retention policy for customer prompts and
    completions? Per plan tier.
14. Describe your deletion workflow — what is the maximum time
    between a customer deletion request and purge from all stores,
    including embeddings and caches?
15. How do you handle special-category data (GDPR Art. 9), minors'
    data, and regulated-sector data (health, financial, biometric)?
16. Do you comply with GDPR Art. 28 processor obligations where
    applicable? Provide the current DPA. *(Evidence)*
17. For personal-data inputs, is cross-border data transfer in
    scope? Which transfer mechanism applies (SCCs, adequacy, BCRs)?
    *(Evidence)*

#### Section 4 — Model development and documentation (T1, T2)

18. Provide the latest model card or system card for the in-scope
    product. *(Evidence)*
19. What capability and safety evaluations are run on each release?
    Share an abstract of the last release-gate eval report. *(Evidence)*
20. Which benchmarks and red-team activities are used? How are
    results published? Note where published results are
    independently reproduced (and where they are not).
21. How are safety mitigations layered (pre-training filters, fine-
    tune filters, post-processing, system-prompt policy, moderation
    API)?
22. Describe your fine-tuning offering (if any) and which of the
    customer's duties shift when the customer fine-tunes.
23. For any high-risk use case under EU AI Act Annex III, can you
    provide the technical documentation required by Art. 11 to
    support the deployer's duty? *(Evidence sample)*

#### Section 5 — Security and adversarial ML (all tiers)

24. Which AI-security framework do you reference (OWASP LLM Top 10,
    OWASP ML Security Top 10, MITRE ATLAS, NIST AML)? How do your
    controls map?
25. Describe your approach to **prompt injection** (direct and
    indirect) and system-prompt leakage.
26. Describe your approach to **training-data and model
    poisoning**, including supply-chain integrity on model weights
    and dependencies.
27. Describe your approach to **model extraction**,
    **membership-inference**, and **data-extraction** attacks.
28. What rate-limiting, abuse-detection, and egress controls apply
    to customer API traffic?
29. Do you conduct periodic AI-focused red-team exercises? Share
    an executive-summary from the latest exercise. *(Evidence)*
30. Confirm whether you maintain SOC 2 Type II, ISO/IEC 27001, and
    other security certifications relevant to the hosted service.
    *(Evidence)*
31. Describe how customer content is isolated across tenants
    (data, embeddings, caches, logs).

#### Section 6 — Content safety and acceptable use (all tiers)

32. Describe your content policy for the product in scope. What
    categories of content are refused? What categories are
    filtered post-generation?
33. Is there a human-review channel for enforcement decisions
    (refusals, bans)?
34. How are abuse reports from customers triaged and resolved?
    State the SLA.
35. For enterprise customers, do you allow policy overrides? Which
    overrides can we not have?

#### Section 7 — Incidents, monitoring, and change management (all tiers)

36. Provide your incident classification scheme for AI-specific
    incidents. Confirm whether you notify customers of serious
    incidents under EU AI Act Art. 73 and within what window.
37. Describe your change-management process for model updates,
    system-prompt updates, and policy changes. What is the minimum
    notice window to enterprise customers?
38. Do you offer pinned model versions? What is the deprecation
    notice window and the maximum pinned-version lifetime?
39. What monitoring telemetry can the customer receive (abuse rates,
    rate-limit hits, latency, safety-filter hits)?
40. Describe your business-continuity and failover posture for AI
    services.

#### Section 8 — Compliance posture (all tiers)

41. For the EU AI Act: specify your role (provider / deployer / GPAI
    provider / authorised representative), the Articles that apply,
    and the evidence you can supply to our conformity assessment or
    deployer FRIA. *(Evidence)*
42. For NIST AI RMF alignment: cross-reference your controls to the
    `GOVERN / MAP / MEASURE / MANAGE` functions. *(Evidence)*
43. For NYC LL 144, Colorado SB 24-205, Illinois HB 3773, California
    SB 942, Utah AI Policy Act (and other state-level AI rules):
    describe applicability to your product and the evidence you can
    supply.
44. Describe your approach to regulated-sector overlays (HIPAA,
    GLBA, PCI-DSS, FedRAMP) where in scope.
45. Confirm your willingness to execute an AI Addendum covering (a)
    data-use limitations, (b) attestation refresh, (c) notice of
    material model changes, (d) cooperation on regulator inquiries.

### 2.3 Scoring and flagging guidance

For each answer, record:

- **Answered?** `yes` / `partial` / `no` / `declined`.
- **Evidence provided?** `yes` / `no` / `on-request`.
- **Risk read** (per-answer):
  - **🟢 Acceptable** — answer is complete, evidence received, no
    red flags.
  - **🟡 Attention** — answer is partial, evidence deferred, or
    policy is weaker than the organisation's posture; follow-up
    required.
  - **🔴 Material** — answer discloses a hard-to-accept posture
    (no isolation, training on customer prompts by default without
    plan-tier opt-out, no pinned model versions, no incident SLA).

Hard red flags — any one is a council-level escalation:

- Vendor trains on customer prompts without a contractually-
  enforceable opt-out at the tier being purchased.
- Vendor cannot name a deprecation / notice window on model
  changes.
- Vendor declines to execute an AI-specific addendum.
- Vendor declines to share release-gate eval abstracts under NDA.
- Vendor claims EU AI Act Art. 25 is "not applicable" without
  rationale.
- Vendor's data-retention window on prompts exceeds 30 days with no
  customer-tier override.

### 2.4 Worked response — Vendor X, tier-1 LLM API

Representative extracts (fictional, used only to illustrate scoring).

- Q4 (role under Art. 25): "Provider of a general-purpose AI model
  with systemic risk under Art. 51; can also act as provider of
  high-risk AI systems when in scope." — 🟢 acceptable;
  self-classification matches the regulation.
- Q11a (training-data provenance): "Mix of licensed data, public
  web, and limited synthetic data; we do not publish a full dataset
  manifest." — 🟡 attention; follow-up to obtain at least the
  category weights and the licensing attestation.
- Q12 (training on customer prompts): "Not by default on the
  Enterprise plan; on Pro and Free tiers, opt-out available." — 🟡
  attention; confirm contractual language for our Enterprise tier
  before go-live.
- Q25 (prompt injection): "Multi-layer approach: input tagging,
  sandbox tool-use, retrieval-source trust tiers, output filters;
  quarterly red-team exercises against injection scenarios." — 🟢
  acceptable; request last exercise abstract.
- Q31 (tenant isolation): "Prompts and completions isolated per
  customer namespace; embeddings not shared across tenants; logs
  encrypted with per-tenant keys." — 🟢 acceptable; request
  independent attestation referenced in SOC 2.
- Q36 (incident notification): "Serious-incident notice within 15
  calendar days of confirmed classification, aligned to EU AI Act
  Art. 73 draft reporting template." — 🟢 acceptable; contractually
  pin the 15-day window.
- Q38 (pinned model versions): "Pinning supported on Enterprise;
  minimum 12-month lifetime after deprecation announcement." — 🟢
  acceptable; verify in the order form.

Overall posture: tier-1 vendor acceptable with four 🟡 follow-ups
to be closed before go-live. Scored contract: AI Addendum required.

### 2.5 Worked response — Vendor Y, tier-2 labelling platform

- Q4: "Not applicable — we do not develop or distribute AI
  systems." — 🟡 attention; vendor is nonetheless material to our
  training-data provenance per Art. 25 chain; keep in scope.
- Q11b (opt-out and licensing): "We accept customer-supplied data
  and do not use it to train internal models." — 🟢 acceptable;
  contractually confirm.
- Q13 (retention): "Annotations retained for the contract term plus
  90 days; raw unlabelled data retained per customer configuration."
  — 🟢 acceptable with plan-tier confirmation.
- Q26 (poisoning): "Annotator reputation scoring, duplicate-
  worker detection, golden-set overlap on 5% of tasks." — 🟢
  acceptable; request sample report.
- Q31 (tenant isolation): "Per-project databases; workers see only
  their assigned tasks." — 🟢 acceptable; verify in SOC 2.
- Q36 (incident notification): "Standard security-incident SLA 72
  hours; no AI-specific SLA." — 🟡 attention; propose AI-specific
  SLA in the AI Addendum.

Overall posture: tier-2 vendor acceptable with two 🟡 follow-ups.

### 2.6 Where this questionnaire ties into the controls

- Every answer flagged 🔴 opens a finding in the control register
  under `CTRL-TP-02-vendor-attestation` (see exercise-01) and a
  corresponding risk-register entry.
- Every tier-1 and tier-2 vendor requires annual re-attestation;
  the control cycle in exercise-01 drives the cadence.
- The questionnaire feeds exercise-05's crosswalk: vendor answers on
  Q7, Q24, Q42 populate the vendor side of the framework mapping.
- The questionnaire feeds mod-105 impact-assessment work: Q11, Q15,
  Q17, Q23 populate the FRIA/AIA vendor-side evidence.
- The questionnaire feeds mod-109 incident-response work: Q36 is
  the notice-window field in the incident-response runbook.

### 2.7 Decision rationale

- **Tier first, questionnaire second.** Sending the full
  questionnaire to every SaaS vendor wastes capacity and buries
  real signal. A tier record one line long makes that decision
  visible.
- **Role under Art. 25 is a question, not an assumption.** Even
  vendors that insist they are "not an AI vendor" may be acting
  as providers along the value chain; the questionnaire forces the
  self-classification on the record.
- **Policy posture + evidence, not just policy posture.** The
  questionnaire asks for evidence pointers. Attestation without
  evidence is a self-assessment.
- **Named technical controls, not generic security.** Prompt
  injection, poisoning, extraction, and tenant isolation are
  AI-specific. A generic SOC 2 does not substitute.
- **Content and change management are a governance concern.**
  Model updates and policy changes can invalidate a FRIA/AIA in
  weeks. The questionnaire makes the notice-window a procurement
  bar.
- **Pin a single "scope version".** The questionnaire is answered
  about one product version. "Our platform" is too broad to
  re-check later.
- **Red flags predefined, not negotiated.** Deciding at the moment
  of a bad answer whether to escalate makes vendor diligence
  political. Predefine the hard flags.
- **Role-based contact fields.** Vendor people rotate. Named
  roles survive.

## 3. Validation steps

1. Confirm the vendor tier is recorded with a one-line rationale
   before the questionnaire is sent.
2. Confirm the questionnaire is dated and tied to a product
   version.
3. For every 🟡 or 🔴 answer, confirm there is a follow-up ticket
   with an owner and a target date.
4. Confirm the signed attestation is present before go-live.
5. For tier-1 and tier-2 vendors, confirm the AI Addendum is in
   place or the procurement block is open.
6. Confirm annual re-attestation is scheduled on the vendor's
   registration row.
7. For high-risk (EU AI Act Annex III) use cases, confirm the
   vendor's answers to Q18–Q23, Q36, and Q41 are sufficient for
   the deployer's Art. 11 / Art. 26 / Art. 27 obligations.
8. Spot-check: a 🟢 Acceptable answer with no evidence pointer is a
   grading mistake; rescore to 🟡.

## 4. Rubric or review checklist

Grade the questionnaire submission on six dimensions, 3 points
each (total 18). Pass = total ≥ 13 with no 0.

| Criterion | 3 — strong | 2 — acceptable | 0–1 — weak |
|---|---|---|---|
| **Tiering** | Four-tier model with explicit triggers; tier recorded with rationale. | Tiering exists but trigger criteria loose. | Flat questionnaire for every vendor. |
| **Coverage** | All eight sections present; Art. 25, AML, content safety, change management included. | ~80% coverage. | Missing AML, Art. 25, or change management. |
| **Evidence demands** | Evidence pointers required on all starred questions; policy-without-evidence flagged. | Partial evidence requirement. | Attestation only; no evidence. |
| **Scoring & red flags** | 🟢/🟡/🔴 scheme defined; hard-flag list named. | Scoring exists but no red-flag list. | Narrative judgments only. |
| **Worked responses** | Two vendors (tier-1 and tier-2) scored with sample answers and follow-ups. | One vendor shown. | None. |
| **Integration** | Hands off cleanly into the control register (exercise-01), the crosswalk (exercise-05), FRIA/AIA (mod-105), and incident response (mod-109). | Hand-off named but not used. | Standalone; no hand-off. |

Document-level checks (binary, all must pass):

- [ ] Vendor tier is recorded.
- [ ] Questionnaire is dated and version-pinned.
- [ ] Every section has at least one question requiring evidence.
- [ ] Hard-flag list is explicit.
- [ ] Annual re-attestation is scheduled on the vendor row.

## 5. Common mistakes

- **One-size questionnaire for every vendor.** T4 vendors receive
  the full ~45-question pack and real T1 vendors get asked only
  about SOC 2. Tier first.
- **Accepting "proprietary" as an answer on training data.** It
  is acceptable to receive category-level weights under NDA; a
  flat refusal is a 🟡 at minimum.
- **No contractual AI Addendum.** Vendor attestations outside the
  contract are not enforceable. Follow the questionnaire with an
  Addendum.
- **Training-on-customer-prompts buried in the DPA.** The
  questionnaire asks explicitly per plan tier because this is a
  real deal-breaker for regulated customers.
- **No change-notice window.** A silent model swap can
  re-characterise a system under EU AI Act Annex III overnight.
  Fix the notice window in writing.
- **Treating SOC 2 as sufficient evidence of AML controls.** SOC 2
  does not cover prompt-injection, poisoning, or extraction. Ask
  the AI-specific questions.
- **Scoring every answer 🟢 to close the vendor review quickly.**
  Vendor selection becomes vendor theatre; the organisation
  carries the risk anyway.
- **Not tying the questionnaire to a specific product version.**
  "Platform X, latest" means nothing six months later.
- **Omitting Art. 25 vendor-role self-classification.** The
  value-chain clause in the EU AI Act is one of the most-missed
  governance points.

## 6. References

- Regulation (EU) 2024/1689 — EU AI Act. Articles cited throughout:
  Art. 9, 10, 11, 13, 14, 15, 25 (value chain), 26 (deployer
  duties), 27 (FRIA), 43/47 (conformity assessment), 50
  (transparency), 51/55 (GPAI / systemic risk), 73 (serious-
  incident reporting).
  <https://eur-lex.europa.eu/eli/reg/2024/1689/oj>
- Regulation (EU) 2016/679 — GDPR, Arts. 9 (special-category data),
  22 (automated decision-making), 28 (processor), 44–49 (transfers).
  <https://eur-lex.europa.eu/eli/reg/2016/679/oj>
- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 —
  `GOVERN 6.*` on third-party / supply-chain.
  <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, *AI RMF Generative AI Profile* (NIST AI 600-1), 2024 —
  generative-AI-specific supplier risks referenced in sections 5
  and 6.
  <https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf>
- ISO/IEC 42001:2023 — *Information technology — Artificial
  intelligence — Management system*, clause 8.1 (operational
  planning and control), Annex A supplier controls (where
  applicable).
  <https://www.iso.org/standard/81230.html>
- OWASP Top 10 for LLM Applications & Gen AI Security Project —
  section 5 reference.
  <https://genai.owasp.org/llm-top-10/>
- OWASP Machine Learning Security Top 10 — section 5 reference.
  <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE ATLAS — section 5 reference.
  <https://atlas.mitre.org/>
- VeriSwarm Trust Center — practitioner implementation example
  of an AI-vendor trust posture against this questionnaire's
  categories.
  <https://veriswarm.ai/trust>
