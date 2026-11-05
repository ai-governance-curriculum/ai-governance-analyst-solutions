# Exercise 02 — EU AI Act Tier Triage Workflow — Reference Solution

## 1. Solution overview

The EU AI Act (Regulation (EU) 2024/1689) is risk-tiered: the obligations that
apply to an AI system depend on which tier it falls into, not on the technology
used to build it. A governance analyst therefore cannot start writing controls
until the system has been placed in a tier — and that placement has to be
re-done every time the **intended purpose**, the **context of use**, or the
**role** (provider vs deployer vs importer vs distributor, Article 25) changes.

The tiers in order of severity are:

1. **Prohibited** — Article 5 practices (cannot be placed on the market or put
   into service in the EU).
2. **High-risk** — Article 6(1) safety components of products listed in Annex
   I, or Article 6(2) systems whose intended purpose falls under an Annex III
   use case, subject to the Article 6(3) derogation for narrow, non-profiling
   cases.
3. **Limited-risk / transparency** — Article 50 obligations for systems that
   interact with natural persons, generate synthetic audio/image/video/text,
   perform emotion recognition or biometric categorisation, or produce
   deepfakes.
4. **Minimal-risk** — everything else; no mandatory obligations under the Act,
   though voluntary codes of conduct (Article 95) are encouraged.
5. **General-Purpose AI (GPAI) model** — a horizontal category layered on top:
   Articles 53–54 obligations apply to all GPAI model providers, and Articles
   51 and 55 add systemic-risk obligations when the model crosses the training
   compute threshold or is otherwise designated.

This solution provides:

- An ordered **decision tree** (yes/no gates) the analyst runs top-down.
- A **triage record template** that captures the tier, the citation, the role,
  the evidence, and the date — so the record can be re-triaged later.
- **Three worked examples** running the gates end to end.
- A **rubric** a reviewer can use to grade an analyst's triage record.

The learner's output is correct when every triage record (a) applies the gates
in the right order starting at Article 5, (b) cites the Article or Annex that
drove the decision, (c) names the chosen tier explicitly, (d) captures evidence
for any Article 6(3) derogation claim, and (e) is dated so it can be revisited.

## 2. Worked answer or implementation

### 2.1 Decision tree (ordered yes/no gates)

The gates are run in order. The first gate that fires determines the tier for
that use case. Do not re-order them — Article 5 is a market-access prohibition
that overrides every other tier, and the Annex III → high-risk path is only
reachable if Article 5 has already been cleared.

| # | Gate (ask yourself) | If **YES** | If **NO** |
| --- | --- | --- | --- |
| **1** | Does any intended or reasonably foreseeable use of the system match an Article 5 prohibited practice? (subliminal manipulation, exploitation of vulnerabilities, social scoring by public authorities, untargeted facial-image scraping, emotion recognition in workplace/education except medical/safety, biometric categorisation inferring protected characteristics, real-time remote biometric identification in public spaces for law enforcement outside the narrow carve-outs, individual predictive policing based solely on profiling.) | Tier = **PROHIBITED**. Stop. Do not place on market or put into service in the EU. | Go to gate 2. |
| **2** | Is the system a **safety component** of a product covered by Union harmonisation legislation listed in **Annex I**, OR is the system itself such a product, AND is it required to undergo a third-party conformity assessment under that legislation? (Article 6(1)) | Tier = **HIGH-RISK** via Article 6(1). Go to the triage record and record the Annex I act (e.g., Machinery Regulation, Medical Devices Regulation, Radio Equipment Directive, etc.). | Go to gate 3. |
| **3** | Does the intended purpose fall under any **Annex III** use case? Annex III areas: (1) biometrics, (2) critical infrastructure, (3) education and vocational training, (4) employment, workers management and access to self-employment, (5) access to and enjoyment of essential private services and essential public services and benefits, (6) law enforcement, (7) migration, asylum and border control management, (8) administration of justice and democratic processes. (Article 6(2)) | Candidate = **HIGH-RISK via Annex III**. Run gate 3a (Article 6(3) derogation) before finalising. | Go to gate 4. |
| **3a** | *Only reached from gate 3.* Does the system fall under one of the Article 6(3) derogation conditions — (a) narrow procedural task, (b) improving the result of a previously completed human activity, (c) detecting decision-making patterns without replacing or influencing the human assessment, or (d) a preparatory task to an assessment relevant to Annex III — **AND** does it not perform profiling of natural persons? | Tier = **NOT HIGH-RISK** (derogation). Record evidence and go to gate 4 to check Article 50 transparency. The provider must still register the system in the EU database (Article 49(2)) and keep documentation justifying the derogation. | Tier = **HIGH-RISK**. Apply Chapter III Section 2 obligations (risk-management system, data governance, technical documentation, logging, transparency to deployers, human oversight, accuracy/robustness/cybersecurity). |
| **4** | Does the system (a) interact directly with natural persons, (b) generate synthetic audio/image/video/text content, (c) perform emotion recognition or biometric categorisation (where not already prohibited under Article 5), or (d) produce deepfakes? (Article 50) | Tier includes **LIMITED-RISK / TRANSPARENCY obligations** under Article 50 — layer them on top of whatever other tier was chosen. Go to gate 5. | Go to gate 5. |
| **5** | Is the system a **general-purpose AI model** as defined in Article 3(63) — a model trained on a large amount of data using self-supervision at scale, that displays significant generality and can competently perform a wide range of distinct tasks? | **GPAI obligations apply** (Articles 53 and 54 for all GPAI model providers: technical documentation, downstream information, copyright policy, training-data summary). Go to gate 5a. | Go to gate 6. |
| **5a** | Does the model meet the **systemic-risk** threshold under Article 51 — either (i) high-impact capabilities evaluated with appropriate tools and benchmarks, presumed when the cumulative training compute exceeds **10^25 FLOP**, or (ii) designation by the Commission under Article 51(1)(b)? | Add **Article 55 systemic-risk GPAI obligations** (model evaluation including adversarial testing, systemic-risk assessment and mitigation, serious-incident reporting to the AI Office, cybersecurity protection). Go to gate 6. | Article 53/54 GPAI obligations only. Go to gate 6. |
| **6** | None of the above fired on a prohibition, high-risk or transparency trigger. | Tier = **MINIMAL-RISK**. No mandatory Act obligations. Consider voluntary codes of conduct under Article 95 and align with ISO/IEC 42001 for an AI management system. | — |

Notes on how to run the gates:

- The gates apply to the **intended purpose** and **reasonably foreseeable
  misuse** as declared by the provider (Article 3(12) and Article 9). Change
  the intended purpose and you re-run the triage.
- The gates do not short-circuit Article 50. Transparency obligations are
  **layered**: a high-risk HR screener that also chats with the candidate is
  both high-risk *and* subject to Article 50(1) disclosure.
- The gates do not depend on whether use is "internal". A company deploying an
  Annex III system on its own employees is a **deployer** and inherits
  deployer obligations under Chapter III Section 3 (Article 26 onwards).

### 2.2 Triage record template

Every triage produces a record. An analyst should not progress to risk
treatment or control design without one.

```yaml
triage_record:
  system_name: <string>
  version_or_release: <string>
  intended_purpose: <one-paragraph statement, matches the system card>
  reasonably_foreseeable_misuse: <bulleted list>
  our_role_under_article_25:
    - provider | deployer | importer | distributor | authorised_representative
  deployment_context:
    jurisdictions: [EU-27, UK, ...]
    user_population: <who interacts with it>
    affected_persons: <who is subject to its outputs>
  gate_results:
    gate_1_article_5: {fired: false, rationale: "...", citation: "Art. 5(1)(?)"}
    gate_2_article_6_1_annex_i: {fired: false, rationale: "...", annex_i_act: null}
    gate_3_annex_iii: {fired: false, rationale: "...", annex_iii_point: null}
    gate_3a_article_6_3_derogation: {claimed: false, basis: null, profiling: false, evidence: null}
    gate_4_article_50_transparency: {fired: false, triggers: []}
    gate_5_gpai: {fired: false, rationale: "..."}
    gate_5a_systemic_risk: {fired: false, basis: null}
  chosen_tier: PROHIBITED | HIGH_RISK | LIMITED_RISK | MINIMAL_RISK
  gpai_overlay: NONE | GPAI_ARTICLE_53_54 | GPAI_SYSTEMIC_RISK_ARTICLE_55
  citations: [Article 5(1)(a), Annex III §4(b), ...]
  triaged_by: <analyst name>
  triaged_on: <ISO-8601 date>
  next_review_trigger:
    - intended_purpose_change
    - new_deployment_jurisdiction
    - material_model_update
    - annex_iii_amendment_via_delegated_act_article_7
  next_review_due_on: <ISO-8601 date>
```

### 2.3 Worked example A — HR candidate screening tool

**System.** An internal tool that ranks external candidates for engineering
roles. Reads CVs and application forms, outputs a 0–100 "fit" score and a
shortlist recommendation. Hiring manager makes the final decision but almost
always follows the shortlist.

**Role under Article 25.** We built it ourselves for our own use → we are both
**provider** (we developed it and placed it into service under our own name)
and **deployer**.

**Gates.**

| Gate | Result | Rationale |
| --- | --- | --- |
| 1. Article 5 | NO | Not subliminal, not exploitation of vulnerabilities, not social scoring by a public authority, not biometric. Scoring job applicants is not an Article 5 prohibition. |
| 2. Article 6(1) / Annex I | NO | Not a safety component of a regulated product. |
| 3. Annex III | YES | Annex III §4 ("Employment, workers management and access to self-employment") point (a) covers AI systems intended to be used for the recruitment or selection of natural persons, in particular to place targeted job advertisements, to analyse and filter job applications, and to evaluate candidates. |
| 3a. Article 6(3) derogation | NO | We perform profiling of natural persons (ranking applicants). Article 6(3) last paragraph expressly excludes systems that perform profiling. Derogation unavailable. |
| 4. Article 50 | PARTIAL | If the tool chats with the candidate (e.g., chatbot intake), Article 50(1) disclosure applies. If it only reads CVs silently, no interaction trigger, but deployer disclosure under Article 26(11) to the candidate as an affected person still applies. |
| 5. GPAI | NO | Not a GPAI model; may *use* a GPAI model under the hood, in which case the GPAI provider has its own Article 53/54 duties. |

**Chosen tier.** **HIGH-RISK** via Annex III §4(a). Article 50 layered on if
there is a chat interface. GPAI overlay NONE for the tool itself.

**Key obligations triggered as provider.** Risk-management system (Art. 9),
data governance for training/validation/test sets including bias detection
(Art. 10), technical documentation (Art. 11 + Annex IV), automatic logging
(Art. 12), transparency and instructions for deployers (Art. 13), human
oversight (Art. 14), accuracy / robustness / cybersecurity (Art. 15),
conformity assessment under Art. 43, CE marking, EU database registration
(Art. 49), post-market monitoring (Art. 72).

**Key obligations triggered as deployer.** Use according to instructions
(Art. 26(1)), human oversight assignment (Art. 26(2)), input-data relevance
(Art. 26(4)), logging retention (Art. 26(6)), **fundamental rights impact
assessment** (Art. 27 — applies to deployers that are bodies governed by public
law or private operators providing public services, and specifically to Annex
III §5(b)-(c) and others; check applicability), inform candidates as affected
persons (Art. 26(11)).

### 2.4 Worked example B — Customer-service chatbot

**System.** A chatbot on the company's public website that answers product
questions, built on top of a third-party GPAI model via API. Does not make
decisions about the user; routes to a human agent for anything transactional.

**Role.** We are the **deployer** of the chatbot. The GPAI model vendor is the
GPAI model **provider**. We are the **provider of the AI system** built on top
of their model (per Article 25(1)(a), we place the chatbot on the market under
our own name, even though the underlying model is third-party).

**Gates.**

| Gate | Result | Rationale |
| --- | --- | --- |
| 1. Article 5 | NO | No prohibited practice. |
| 2. Article 6(1) / Annex I | NO | Not a safety component. |
| 3. Annex III | NO | Customer support for product questions does not match any Annex III use case. Not biometrics, not critical infrastructure, not education, not employment, not essential services (routine product help is not an "essential service" in the Annex III §5 sense — that targets things like public assistance, credit scoring, health/life insurance risk assessment, emergency dispatch). |
| 3a. Article 6(3) | N/A | Gate 3 did not fire. |
| 4. Article 50 | YES | Article 50(1): providers shall ensure that AI systems intended to interact directly with natural persons are designed and developed in such a way that the natural persons concerned are informed that they are interacting with an AI system. If the bot generates synthetic text as public-interest content, Article 50(4) also applies. |
| 5. GPAI | NO (for the chatbot). | The chatbot is an AI *system*, not a GPAI *model*. The upstream model provider carries Article 53/54 obligations. |

**Chosen tier.** **LIMITED-RISK / TRANSPARENCY** (Article 50). GPAI overlay
NONE for us; the model provider has its own GPAI obligations.

**Key obligations triggered.** A clear, accessible disclosure at the start of
the interaction that the user is talking to an AI, in a form accessible to
persons with disabilities (Art. 50(1) and Art. 50(5)). Downstream, keep
provider-supplied information from the GPAI model provider (per Art. 53(1)(b))
on file so that if the use case expands, re-triage is possible.

### 2.5 Worked example C — Internal code-completion assistant

**System.** An internal IDE plugin that suggests code to our own engineers.
Fine-tuned in-house on top of an open-weights GPAI model (training compute of
the base model is below 10^25 FLOP and not Commission-designated).

**Role.** We are the **deployer** of the assistant. Because we fine-tuned the
base model and offer it internally, we need to check whether fine-tuning makes
us a **downstream provider** under Article 25(1)(c) — substantial modification
of an AI system — and whether it makes us a **GPAI model provider** for the
fine-tuned model under the Commission's downstream-modifier guidance. Treat
the modifier-threshold question as an open compliance item: document the
fine-tune compute, data volume, and intended-purpose delta, and reclassify
ourselves as a downstream GPAI model provider whenever the modification is
non-trivial relative to the base model until the Commission's guidance under
Article 96 settles the threshold.

**Gates.**

| Gate | Result | Rationale |
| --- | --- | --- |
| 1. Article 5 | NO | Suggesting code to developers is not a prohibited practice. |
| 2. Article 6(1) / Annex I | NO | Not a safety component. |
| 3. Annex III | NO | Internal developer-productivity tool is not in any Annex III category. It is not "employment, workers management" in the Annex III §4 sense (that targets recruitment, task allocation, performance monitoring and evaluation decisions, promotion/termination decisions). A code completion that merely proposes text does not allocate tasks, monitor performance or make employment decisions. If it later gets wired into performance review, re-triage. |
| 3a. Article 6(3) | N/A | Gate 3 did not fire. |
| 4. Article 50 | PARTIAL | Developers know they are using an AI assistant, so Article 50(1) is satisfied in substance. If the plugin surfaces generated commit messages or code comments that go public, Article 50(4) synthetic-content marking may apply. |
| 5. GPAI | DEPENDS | The *base model* is a GPAI model; its original provider holds Article 53/54 duties. Our fine-tuned model may or may not be a new GPAI model depending on the scale of modification — document the fine-tune and treat ourselves as a GPAI provider for that fine-tune if the Commission guidance says so. |
| 5a. Systemic risk | NO | Compute is below 10^25 FLOP and no Commission designation. |

**Chosen tier.** **MINIMAL-RISK** for the system as deployed internally.
**GPAI overlay** for the fine-tuned model is Article 53/54 if we are treated
as a downstream GPAI model provider; otherwise NONE.

### 2.6 Decision rationale — why the gates are in this order

- **Article 5 first.** The Act bans these practices outright under Article 5;
  no downstream obligation set can cure a prohibited practice. Running Annex
  III first and discovering a prohibition later means you have already wasted
  conformity-assessment effort on a system that cannot be placed on the market.
- **Annex I before Annex III.** Article 6(1) high-risk is a *product-safety*
  path that piggybacks on existing harmonisation law; Article 6(2) / Annex III
  is the standalone AI-use-case path. The two are not mutually exclusive, but
  Annex I systems have an existing conformity-assessment pipeline that Annex
  III-only systems do not, and you need to know which applies before you pick
  the assessment route under Article 43.
- **Article 6(3) derogation is a sub-gate, not a top-level tier.** It only
  exists inside the Annex III branch, and it is narrow: the system must fall
  into one of four task types (narrow procedural, improving human output,
  pattern detection without replacement, preparatory task) AND must not
  profile natural persons. The derogation is reviewable by the authorities
  and the provider must keep the assessment on file (Article 6(4)) and
  register in the EU database (Article 49(2)).
- **Article 50 layers.** Transparency is not an alternative to high-risk; a
  system can be both. Treating Article 50 as an "else" branch after high-risk
  is a common error that under-protects users.
- **GPAI is horizontal.** A GPAI model can sit inside a prohibited system
  (then Article 5 still wins), a high-risk system, a limited-risk system, or
  a minimal-risk system. The GPAI obligations (Articles 53-55) attach to the
  **model provider**, not the system — so an analyst mapping a system has to
  track both "what tier is my system?" and "what GPAI duties does my upstream
  model provider owe me, and do I now owe them if I fine-tuned?".
- **Role (Article 25) changes the obligations, not the tier.** The tier is a
  property of the system and its use case. The role determines whether you
  carry provider duties (Chapter III Section 2), deployer duties (Section 3),
  importer/distributor duties (Section 4), or authorised-representative duties
  (Article 22). A given company often wears more than one hat.
- **Re-triage on change.** Article 25(1) explicitly states that a deployer,
  importer, distributor or other third party becomes a **provider** if they
  put their name on the system, substantially modify it, or modify the
  intended purpose such that it becomes high-risk. That means a new intended
  purpose can push a minimal-risk tool into high-risk — so the triage record
  must be dated and the re-triage trigger list made explicit.

## 3. Validation steps

A reviewer can apply these checks to any triage record produced by the
workflow. All five must pass for the record to be acceptable.

1. **Gate order check.** The record addresses gate 1 (Article 5) before gate
   3 (Annex III), and the chosen tier is derived from the **first** gate
   that fired, not a later one. A record that selects HIGH-RISK via Annex
   III without first dismissing Article 5 fails this check.
2. **Citation check.** Every gate result cites the Article or Annex that
   drove it (e.g., "Annex III §4(a)", "Article 50(1)", "Article 6(3)(b)").
   Vague citations such as "the Act says" fail.
3. **Derogation evidence check.** If Article 6(3) is claimed, the record
   contains (a) which of the four derogation task types applies, (b) an
   explicit statement that the system does not perform profiling of natural
   persons, and (c) the evidence (system behaviour description) supporting
   both. Missing any of the three → fail.
4. **Role check.** The record names the organisation's role(s) under Article
   25 and, if more than one role applies (e.g., provider *and* deployer),
   lists them separately. A record that elides the role cannot be used to
   pick which obligation section applies.
5. **Date and re-triage trigger check.** The record is dated (ISO-8601) and
   lists at least one re-triage trigger plus a next-review date. A record
   without a date cannot be used to show the triage was current at the time
   of a given deployment decision.

Optional sanity checks (recommended, not required):

- Cross-check the Annex III point number against the current consolidated
  text, not a pre-publication draft; Annex III can be amended by delegated
  act under Article 7.
- If the system uses a third-party GPAI model, confirm that the record names
  the upstream model provider and their stated Article 53(1)(b) information.
- If the system is marketed outside the EU but affects persons in the EU,
  confirm the record has considered Article 2 territorial scope.

## 4. Rubric or review checklist

Score each triage record on the six criteria below, 0–2 per criterion, for a
maximum of 12. A passing record scores **≥ 10 with no zeros**.

| # | Criterion | 0 — fail | 1 — partial | 2 — strong |
| --- | --- | --- | --- | --- |
| R1 | **Gate order** | Starts at Annex III or Article 50; Article 5 not considered. | Article 5 mentioned but not clearly dispatched before later gates. | Gates run in order 1 → 2 → 3 → 3a → 4 → 5 → 5a → 6; chosen tier is the first gate that fired. |
| R2 | **Article / Annex citation** | No citations, or wrong citations. | Some gate results cited, others hand-waved. | Every gate result has a specific Article or Annex citation; Annex III points include the sub-point letter. |
| R3 | **Chosen tier stated explicitly** | Tier not stated, or multiple tiers listed with no selection. | Tier stated but Article 50 overlay or GPAI overlay not addressed. | Exactly one primary tier is named (PROHIBITED / HIGH-RISK / LIMITED-RISK / MINIMAL-RISK), plus overlays for Article 50 and GPAI called out separately. |
| R4 | **Article 6(3) derogation evidence** | Derogation claimed with no evidence, or derogation used despite profiling. | Derogation claimed with task-type stated but profiling check missing. | Derogation claim includes task-type citation, explicit no-profiling statement, and behaviour evidence; or derogation not claimed and this criterion is N/A → score 2. |
| R5 | **Role under Article 25** | No role stated. | Role stated but one of (provider / deployer / importer / distributor / authorised rep) missing when it should apply. | All applicable roles named; downstream-provider trigger (Art. 25(1)(a)-(c)) assessed if we re-badged, substantially modified, or changed intended purpose. |
| R6 | **Dated and re-triage trigger** | No date. | Date present but no re-triage trigger list or next-review date. | ISO-8601 date, named analyst, at least one re-triage trigger, and a next-review date. |

Deck-level checks (binary, all must pass for the triage programme as a whole):

- [ ] The workflow runs Article 5 first on every system without exception.
- [ ] The workflow treats Article 50 as a layer, not an "else" branch.
- [ ] The workflow treats GPAI (Articles 51-55) as a horizontal overlay.
- [ ] The workflow produces a dated record that another analyst can re-open.
- [ ] The workflow is reviewed at least annually, and re-run on intended-purpose change.

## 5. Common mistakes

- **Skipping Article 5 and starting at Annex III.** Analysts who are
  familiar with high-risk obligations default to Annex III because it is
  where most of their work lives. Article 5 prohibitions are rarer but they
  are market-access stop signs; missing one invalidates every downstream
  control.
- **Assuming "internal use" means out of scope.** The Act applies to
  providers *and* deployers (Article 2(1)). A company running an Annex III
  system on its own employees is a deployer with Article 26 obligations,
  and often also the provider under its own name if it developed the
  system in-house (Article 25(1)(a)).
- **Collapsing GPAI into Annex III.** GPAI obligations (Articles 51-55)
  attach to **models**, not to systems, and run in parallel to the system
  tiering. Treating "we use GPT-class model" as automatic high-risk, or
  conversely as automatic minimal-risk, both get the obligations wrong.
- **Missing Article 50 when deploying a chatbot or generative tool.**
  Interactive and generative systems trigger disclosure duties under
  Article 50(1) and synthetic-content-marking duties under Article 50(2)
  and 50(4) even when the system is otherwise minimal-risk. Article 50 is
  easy to forget because it does not require conformity assessment.
- **Claiming the Article 6(3) derogation without evidence — or despite
  profiling.** Article 6(3) is narrow: four task types and an absolute bar
  on profiling of natural persons. A derogation claim with no documented
  task-type mapping, or applied to a system that ranks people, fails on
  review and can be re-classified as high-risk by the authority.
- **Not dating the record and not listing re-triage triggers.** Intended
  purpose drifts. Annex III can be amended via delegated act under
  Article 7. New deployment jurisdictions change the territorial scope
  analysis under Article 2. Without a date and a trigger list, an old
  triage gets re-used past its shelf life.

## 6. References

- **Regulation (EU) 2024/1689 of the European Parliament and of the Council
  of 13 June 2024 (EU AI Act).** Primary source for every gate.
  <https://eur-lex.europa.eu/eli/reg/2024/1689/oj>
  - **Article 3** — definitions, including Article 3(12) intended purpose,
    Article 3(63) general-purpose AI model.
  - **Article 5** — prohibited AI practices. Gate 1.
  - **Article 6** — classification rules for high-risk AI systems;
    Article 6(1) Annex I safety-component path, Article 6(2) Annex III
    use-case path, Article 6(3) derogation and its profiling bar,
    Article 6(4) documentation duty for derogation.
  - **Article 7** — power of the Commission to amend Annex III by delegated
    act; the reason re-triage triggers must include Annex III amendments.
  - **Article 25** — roles along the value chain (provider, deployer,
    importer, distributor, authorised representative) and the conditions
    under which a deployer or other third party becomes a provider.
  - **Article 26** — obligations of deployers of high-risk AI systems.
  - **Article 27** — fundamental rights impact assessment for deployers of
    high-risk AI systems.
  - **Article 43** — conformity assessment routes.
  - **Article 49** — registration in the EU database, including
    derogation-based registration under Article 49(2).
  - **Article 50** — transparency obligations for providers and deployers
    of certain AI systems (interaction, synthetic content, emotion
    recognition / biometric categorisation, deepfakes). Gate 4.
  - **Articles 51-55** — GPAI model provider obligations and systemic-risk
    GPAI obligations, including the Article 51(2) 10^25 FLOP presumption.
    Gates 5 and 5a.
  - **Article 72** — post-market monitoring by providers.
  - **Article 95** — codes of conduct for voluntary application.
  - **Annex I** — Union harmonisation legislation that triggers the Article
    6(1) high-risk path.
  - **Annex III** — the eight high-risk use-case areas. Gate 3.
  - **Annex IV** — technical documentation contents for high-risk systems.
- **NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023** —
  Map 1 (context) is the natural home for the triage record: the EU AI Act
  tier is part of the system's legal and regulatory context.
  <https://www.nist.gov/itl/ai-risk-management-framework>
- **NIST**, *AI RMF Playbook* — suggested actions for Map 1 and Govern 1
  that align with producing and maintaining a tier-triage record.
  <https://www.nist.gov/itl/ai-risk-management-framework>
- **ISO/IEC 42001:2023**, *AI management systems — Requirements* —
  management-system clauses (operational planning and control, Annex A
  controls) under which a triage workflow can be documented and audited.
  <https://www.iso.org/standard/81230.html>
