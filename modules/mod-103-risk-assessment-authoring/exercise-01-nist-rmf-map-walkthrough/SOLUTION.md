# Exercise 01 — NIST AI RMF MAP Walkthrough — Reference Solution

## 1. Solution overview

NIST AI RMF 1.0 organises risk work into four functions — **Govern, Map,
Measure, Manage**. The **Map** function is where an analyst anchors a
first-draft risk assessment: it establishes the context of a specific AI
system and names the risks that context creates. Its five categories are:

- **Map 1** — Context is established and understood.
- **Map 2** — Categorisation of the AI system.
- **Map 3** — AI capabilities, targeted usage, goals, and expected benefits
  and costs compared with status quo are understood.
- **Map 4** — Risks and benefits are mapped for all components, including
  third-party software and data.
- **Map 5** — Impacts to individuals, groups, communities, organisations,
  and society are characterised.

This exercise walks a single AI system — an internal **retrieval-augmented
HR policy assistant** — through all five Map categories end-to-end, so the
learner practises the hand-off from intake to first-draft risk register.

This solution provides:

- A reference walkthrough with content for each Map category.
- A derived first-draft risk register that the walkthrough produces.
- Decision rationale explaining why each entry lands where it does.
- A rubric for grading a learner's own walkthrough.

## 2. Worked answer or implementation

### 2.1 The system under assessment (local exercise context)

> **System name**: PolicyAssistant (internal)
> **Owner**: People Operations, co-owned with IT Platform
> **Modality**: Text-only RAG assistant over the employee handbook, HR
> policies, benefits documents, and manager playbooks.
> **Model**: Vendor-hosted general-purpose LLM accessed via enterprise
> tenancy; no fine-tuning; single-tenant retrieval index.
> **Users**: All employees (~6,000) and people managers (~400).
> **Out-of-scope**: giving individual legal, medical, tax, or disciplinary
> advice; writing performance reviews.
> **Business goal**: reduce HR ticket volume on policy lookups by a target
> amount agreed with HR leadership (actual target redacted to keep the
> exercise jurisdiction-neutral).

This is a reference scenario built for the exercise, not a real system.

### 2.2 Map 1 — Context

Map 1 asks the analyst to pin down mission, legal/regulatory context, risk
tolerance, and intended purpose. The RMF Core splits this into sub-cats
1.1–1.6; the analyst's write-up should cover each.

| Sub-category | What the analyst writes for PolicyAssistant |
| --- | --- |
| **1.1 Intended purpose & use** | Answer employee questions about HR policy by retrieving from an approved corpus and summarising. Not a decision-maker; not a confidential-advice channel. |
| **1.2 Context of use** | Internal, authenticated users only. Desktop and mobile internal apps. English-language launch with later locale expansion gated by eval. |
| **1.3 Mission / business value** | Reduce ticket load on HR; faster time-to-answer for employees; free HR staff for case work. |
| **1.4 Legal / regulatory context** | Employment law applies to anything described as advice (so the system must not give advice); EU AI Act Art. 50 transparency obligation if used in EU locations; workplace data-privacy obligations (e.g., GDPR, state privacy laws). |
| **1.5 Risk tolerance** | Low tolerance for confidential-data leakage, confabulated policy, or advice framing. Moderate tolerance for retrieval misses that return "I don't know". |
| **1.6 Interdisciplinary input** | HR policy owners, Employment Counsel, Privacy, Security, Accessibility, Works Council / employee representatives where present. |

**Deliverable from Map 1**: a one-page context memo that opens the risk
assessment and is linked from the AI inventory record.

### 2.3 Map 2 — Categorisation

Map 2 categorises the system: what it is, who it affects, and which risk
tiers it falls into.

| Dimension | Categorisation |
| --- | --- |
| **Task type** | Information retrieval + summarisation / Q&A. |
| **Generative?** | Yes — see GenAI Profile risks in §2.5. |
| **Decision role** | Advisory to a human; no automated adverse action. |
| **Autonomy** | None. The model does not take actions on behalf of the user. |
| **Population affected** | Internal employees (workers), not external customers. |
| **Internal risk tier (reference values; map to local policy)** | Moderate — information system with employee impact, no automated adverse decisions. |
| **EU AI Act posture (where applicable)** | Not Annex III high-risk by itself. If extended to make employment decisions (hiring, promotion, termination) it would become **Annex III** high-risk — this stays flagged as an in-scope-later concern. Article 50 transparency applies to the chatbot nature. |

**Deliverable from Map 2**: a short categorisation box that feeds the risk
tier assignment and the regulator-facing summary.

### 2.4 Map 3 — Capabilities, goals, benefits, and costs vs status quo

Map 3 compares the AI option against the status quo. The honest comparison
is often the most useful page in the whole assessment.

| Dimension | Status quo (HR portal + tickets) | PolicyAssistant |
| --- | --- | --- |
| Time to answer common policy questions | Minutes–days (ticket queue) | Seconds |
| Consistency across business units | Variable (different HR BPs) | More uniform — same corpus |
| Risk of wrong answer | Low per-ticket (HR BP writes it) | Non-zero per answer (confabulation) |
| Audit trail | Ticket system, rich | Conversation log + retrieved sources — requires explicit logging |
| Accessibility | Screen-reader support in HR portal | Must be designed in from day one |
| New risks introduced | — | Confabulation, prompt injection via docs, vendor dependency, cross-tenant leak worries |
| Dropped risks | — | Lower risk of HR BP paraphrasing policy inconsistently |

**Deliverable from Map 3**: a benefit/harm matrix and a short "alternatives
considered" paragraph (do-nothing, better search, LLM-free FAQ bot, this
system).

### 2.5 Map 4 — Risks and benefits across all components

Map 4 is a component sweep: every element that could contribute risk gets
named, including third-party pieces. For PolicyAssistant:

| Component | Risks mapped |
| --- | --- |
| **Vendor LLM** | Confabulation; information security (prompt injection in retrieved content); value-chain integration (vendor change notice, deprecation); data privacy (prompt handling, data residency). |
| **Enterprise tenancy / data processing agreement** | Residency; retention; subprocessors; training-data use — all contract-governed. |
| **Retrieval index & embeddings** | Confidentiality of the corpus (over-permissive retrieval); staleness (removed policies still indexed); poisoning (a doc with hostile text being retrieved verbatim). |
| **Source corpus (HR docs)** | Document-level access control vs. index-level; policy currency; translated copies mismatching master. |
| **Application / UI** | Authn/authz; disclosure of AI nature; accessibility; logging. |
| **Prompt templates** | Jailbreak surface; policy-grounding instructions can be overridden by malicious content. |
| **Human operators (HR authors, prompt engineers, platform SREs)** | Mis-scoping training; poor hand-off between HR and engineering; on-call knowledge of the model vs retrieval failure modes. |
| **Users (employees, managers)** | Over-reliance; using it for in-scope-later cases (medical, legal) despite warnings. |
| **Monitoring & analytics** | PII in logs; retention; who can read conversations. |

**Deliverable from Map 4**: a component inventory (with vendor names where
applicable) and a draft risk register with each item traced to one or more
components.

### 2.6 Map 5 — Impacts

Map 5 makes impacts explicit and specific.

| Affected population | Potential harm | Direction |
| --- | --- | --- |
| Individual employees | Confabulated policy leads to a wrong action (e.g., missing a leave deadline). | Negative — reversible with HR review; low frequency goal. |
| People managers | Over-reliance for case-handling outside scope (discipline, performance). | Negative — potentially severe; mitigated by scope guardrails + training. |
| Employees requiring accommodations | Accessibility gap in the chat UI. | Negative — mitigated with a11y review. |
| Non-English-primary employees | Lower factuality in uncovered languages. | Negative — mitigated by staged locale rollout. |
| Works councils / unions (where applicable) | Not consulted on an employer-deployed system. | Negative — mitigated by engagement in Map 1.6. |
| HR staff | Lower repetitive load → more case work. | Positive. |
| Organisation | Fewer tickets, more consistent policy answers. | Positive. |

**Deliverable from Map 5**: a stakeholder & impact table, which becomes the
impact assessment's core and is linked from any downstream model card.

### 2.7 First-draft risk register derived from the walkthrough

A reference analyst produces a register like this from the Map output. Each
row is phrased as a risk statement ("Cause → Event → Consequence") and
traces back to the Map category that surfaced it.

| ID | Risk statement | Source Map cat. | GenAI Profile tag | Initial rating* |
| --- | --- | --- | --- | --- |
| R-01 | Model confabulates policy details → employee takes wrong action → missed benefit or policy violation. | Map 4 (model) | Confabulation | High L × Mod I |
| R-02 | Hostile content in a retrieved doc → prompt-injection changes system behaviour → sensitive policy or system info disclosed. | Map 4 (index) | Information security | Low L × High I |
| R-03 | Over-permissive retrieval → employee sees documents outside their need-to-know → data-privacy incident. | Map 4 (index, DLP) | Data privacy | Mod L × High I |
| R-04 | System used for in-scope-later cases (medical, disciplinary) → user over-reliance → regulated-advice exposure. | Map 1, Map 5 | Human–AI configuration | Mod L × High I |
| R-05 | Vendor LLM changes behaviour under version update → regression on previously accepted answers. | Map 4 (vendor) | Value chain / component integration | Mod L × Mod I |
| R-06 | Transparency obligations under EU AI Act Art. 50 not met in EU rollout → compliance exposure. | Map 1 | — | Low L × Mod I |
| R-07 | Accessibility gap in chat UI → exclusion of users with disabilities. | Map 5 | — | Mod L × Mod I |
| R-08 | Lower factuality in languages added after initial English launch → unequal service. | Map 5 | Harmful bias & homogenisation | Mod L × Mod I |
| R-09 | Logs retain conversation PII beyond policy → privacy exposure. | Map 4 (logs) | Data privacy | Low L × Mod I |

\* L = Likelihood, I = Impact; replace with your org's scale. See
`exercise-03` for a worked scoring approach.

### 2.8 Decision rationale

- **One system, all five Map categories.** The exercise's whole point is
  that Map is a pipeline. A learner who jumps from Map 1 straight to a risk
  register misses why the register has those risks and not others.
- **Context memo before categorisation.** Map 2 cannot assign a tier
  without the Map 1 context. Analysts who reverse the order end up fitting
  context to a pre-chosen tier.
- **"Alternatives considered" is kept short on purpose.** Map 3 is where
  governance intersects product discovery. A heavy comparison report is
  product's job; the analyst only needs enough to show the AI option is
  not the only option considered.
- **Component sweep (Map 4) includes humans and prompts.** Analysts who
  only name the model and the data miss operator risk and prompt-template
  risk — two of the easier failure modes.
- **Impacts (Map 5) name specific populations, not "employees".** The
  rubric penalises generic impact rows for this reason.
- **The risk register phrases risks as Cause → Event → Consequence.** Any
  shorter form ("hallucination risk") makes mitigation untestable later in
  Measure / Manage.

## 3. Validation steps

1. Confirm all five Map categories are present with at least one concrete
   sentence per RMF Core sub-category (1.1–1.6, 2.1–2.3, 3.1–3.5, 4.1–4.2,
   5.1–5.2). Use the NIST AI RMF 1.0 Core document to check sub-category
   numbering.
2. Confirm each risk-register row traces back to at least one Map cell.
   Orphan rows mean the walkthrough was skipped; orphan Map cells mean
   risks were dropped.
3. Confirm every risk statement uses the Cause → Event → Consequence form.
4. Cross-check the risk list against the NIST AI 600-1 GenAI risks: if the
   system is generative and no row is tagged with any of the 12 GenAI
   risks, something is missing.
5. Confirm the categorisation box flags any trigger for EU AI Act Annex III
   (even if the current system does not qualify — the learner should note
   "would become high-risk if extended to X").
6. Confirm there is a decision for Works Council / employee-representative
   consultation in jurisdictions where that applies, or an explicit "not
   applicable" with reason.

## 4. Rubric or review checklist

Score each of the five Map categories out of 4; the walkthrough passes at
an average ≥ 3.0 with no 0s.

| Score | Criterion |
| --- | --- |
| **4 — strong** | All sub-categories covered; content specific to this system; traceable to one or more risk-register rows. |
| **3 — acceptable** | All sub-categories covered; content specific but a couple of rows are generic. |
| **2 — weak** | One or more sub-categories missing or treated with a single generic sentence; some cells copy-pasted boilerplate. |
| **1 — poor** | Only headline sub-categories covered; risk register cannot be reconstructed from the walkthrough. |
| **0 — fail** | Sub-category misidentified, numbering invented, or content contradicts the RMF Core. |

Walkthrough-level checks (binary, all must pass):

- [ ] Context memo present (Map 1).
- [ ] Categorisation box present with tier and regulatory flags (Map 2).
- [ ] Benefit/harm matrix and alternatives paragraph present (Map 3).
- [ ] Component inventory including third-party and prompt layer (Map 4).
- [ ] Stakeholder/impact table lists specific populations (Map 5).
- [ ] Risk register uses Cause → Event → Consequence rows.
- [ ] Every risk row cites the Map category that surfaced it.
- [ ] GenAI systems have at least one row tagged with an AI 600-1 risk.

## 5. Common mistakes

- **Skipping Map 1 and going straight to a risk list.** The context memo
  is what makes the whole assessment legible to a reviewer later.
- **Confusing Map 2 (what the system *is*) with Map 3 (what the system
  *does compared to the status quo*).** Both are needed; they are not
  interchangeable.
- **Component sweep that stops at "the model".** Retrieval, prompts,
  humans, and logs each introduce distinct risks. A single-row
  "model risk" entry collapses them and makes mitigation harder.
- **Generic impact statements ("may harm users").** Map 5 asks for named
  populations and directional harm.
- **Using "hallucination" and "confabulation" interchangeably without
  anchoring to NIST AI 600-1.** Pick the NIST term for anything you want
  to track in the register — it makes cross-referencing easier.
- **Not flagging the "would become high-risk if extended to X" case.**
  Many HR-adjacent assistants grow into performance or hiring use. The
  walkthrough should name the trigger even if today's scope does not hit
  it.

## 6. References

- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 — the
  Map function and its five categories with sub-categories.
  <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, *AI RMF Playbook* — expanded suggested actions per Map sub-category
  used to validate what belongs under each. <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, *AI 600-1: Generative AI Profile*, July 2024 — the twelve GenAI
  risks referenced in the risk register. <https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf>
- OWASP, *Machine Learning Security Top 10* — referenced for the
  prompt-injection / adversarial-input category used in R-02. <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE ATLAS — adversary-technique vocabulary complementing the
  information-security row. <https://atlas.mitre.org/>
