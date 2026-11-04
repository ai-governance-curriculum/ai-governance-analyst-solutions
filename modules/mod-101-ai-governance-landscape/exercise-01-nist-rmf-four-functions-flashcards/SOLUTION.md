# Exercise 01 — NIST AI RMF Four Functions Flashcards — Reference Solution

## 1. Solution overview

The NIST AI Risk Management Framework (AI RMF 1.0) organises AI risk work into
four functions: **Govern**, **Map**, **Measure**, and **Manage**. Govern is
cross-cutting and operates continuously; Map, Measure, and Manage form the
iterative risk-management loop that teams run per AI system or use case.

This solution provides:

- A compact flashcard deck (front = cue, back = definition + where it lives in
  the RMF Core) for each of the four functions and their categories.
- Decision rationale explaining why each flashcard is phrased the way it is.
- A rubric a reviewer can use to grade a learner's own deck.

The learner's output is correct when every card (a) names the function or
category accurately, (b) states its purpose in one sentence, and (c) gives a
concrete artefact or activity a governance analyst would produce or read for
that category.

## 2. Worked answer or implementation

### 2.1 Function-level flashcards

| Front (cue) | Back (definition + analyst artefact) |
| --- | --- |
| **GOVERN** | Cultivate a culture of risk management across the organisation. Sets policies, roles, accountability, and the environment the other three functions run in. _Analyst artefact_: AI policy, RACI, AI inventory governance, council charter. |
| **MAP** | Establish the context of a specific AI system and identify risks tied to that context. _Analyst artefact_: intake form, system card draft, stakeholder and impact map, use-case classification. |
| **MEASURE** | Analyse, assess, benchmark, and track the risks identified in Map using quantitative and qualitative methods. _Analyst artefact_: test plan, eval summary, metric thresholds, residual-risk statement. |
| **MANAGE** | Prioritise and act on risks — accept, mitigate, transfer, or avoid — and monitor effectiveness over time. _Analyst artefact_: risk-treatment plan, go/no-go memo, monitoring plan, incident trigger list. |

### 2.2 Category-level flashcards (Govern)

| Front | Back |
| --- | --- |
| **Govern 1** | Policies, processes, procedures, and practices for AI risk are in place, transparent, and implemented effectively. _Artefact_: AI policy, acceptable-use rules, standards crosswalk. |
| **Govern 2** | Accountability structures are in place so that the right individuals and teams are empowered, responsible, and trained for AI risk. _Artefact_: RACI, role charters, training logs. |
| **Govern 3** | Workforce diversity, equity, inclusion, and accessibility are considered in the AI lifecycle. _Artefact_: team composition notes for review bodies, inclusive-review checklist. |
| **Govern 4** | Teams are committed to a culture that considers and communicates AI risk. _Artefact_: risk-awareness training, speak-up channels, red-team participation records. |
| **Govern 5** | Processes for engaging with AI actors — including affected communities — are in place. _Artefact_: stakeholder-engagement plan, external-review log. |
| **Govern 6** | Policies and procedures are in place to address risks and benefits arising from third-party software, data, and other supply-chain considerations. _Artefact_: vendor AI questionnaire, model-card intake for external models, procurement controls. |

### 2.3 Category-level flashcards (Map)

| Front | Back |
| --- | --- |
| **Map 1** | Context is established and understood — mission, legal, regulatory, risk tolerance, intended purpose. _Artefact_: intake form, use-case statement, jurisdictional notes. |
| **Map 2** | Categorisation of the AI system — task, capability, stakeholders, user groups. _Artefact_: system card, functional description. |
| **Map 3** | AI capabilities, targeted usage, goals, and expected benefits and costs compared with status quo are understood. _Artefact_: benefit/harm matrix, alternative-considered memo. |
| **Map 4** | Risks and benefits are mapped for all components of the AI system, including third-party software and data. _Artefact_: component inventory, data-provenance notes. |
| **Map 5** | Impacts to individuals, groups, communities, organisations, and society are characterised. _Artefact_: impact assessment, affected-population list. |

### 2.4 Category-level flashcards (Measure)

| Front | Back |
| --- | --- |
| **Measure 1** | Appropriate methods and metrics are identified and applied. _Artefact_: test plan, metric register with rationale for each metric. |
| **Measure 2** | AI systems are evaluated for trustworthy characteristics — valid & reliable, safe, secure & resilient, accountable & transparent, explainable & interpretable, privacy-enhanced, fair with harmful bias managed. _Artefact_: eval report mapped to the seven characteristics. |
| **Measure 3** | Mechanisms for tracking identified AI risks over time are in place. _Artefact_: risk register updates, drift logs, trend dashboard. |
| **Measure 4** | Feedback about efficacy of measurement is gathered and assessed. _Artefact_: eval-review minutes, metric-change log, independent review notes. |

### 2.5 Category-level flashcards (Manage)

| Front | Back |
| --- | --- |
| **Manage 1** | AI risks based on Map and Measure are prioritised, responded to, and managed. _Artefact_: prioritised risk register, treatment decisions. |
| **Manage 2** | Strategies to maximise AI benefits and minimise negative impacts are planned, prepared, implemented, documented, and informed by input from relevant AI actors. _Artefact_: mitigation plan, communications plan, residual-risk acceptance. |
| **Manage 3** | AI risks and benefits from third-party resources are regularly monitored, and risk controls are applied and documented. _Artefact_: vendor review cadence, supply-chain risk notes. |
| **Manage 4** | Risk treatments, including response and recovery, and communication plans for identified and measured AI risks are documented and monitored. _Artefact_: incident runbook, post-market monitoring plan, change-control log. |

### 2.6 Decision rationale

- **Flashcard back = definition + artefact.** A governance analyst needs to
  connect each category to concrete evidence they will produce or read. A
  bare-definition card only tests memorisation; the artefact column converts it
  into a usable reference.
- **Govern listed first and treated as cross-cutting.** The RMF Core places
  Govern as foundational; Map, Measure, and Manage each presume Govern is in
  place. Analysts who skip Govern tend to produce one-off risk memos that have
  no home in the organisation's policy stack.
- **Measure 2 enumerates the seven trustworthy characteristics by name.** This
  is the main place in the RMF where the trustworthiness taxonomy shows up in
  the Core; keeping it explicit on the card prevents confusion between "RMF
  functions" (four) and "trustworthy characteristics" (seven).
- **Third-party / supply-chain risk gets its own cards (Govern 6, Map 4,
  Manage 3).** Most real analyst work in 2026 touches vendor models and
  external data; the deck highlights this rather than burying it.

## 3. Validation steps

1. Count cards: the deck should contain **4 function cards + 19 category
   cards = 23 cards** total (6 Govern + 5 Map + 4 Measure + 4 Manage).
2. For each category card, confirm the number matches the RMF Core (e.g., Map
   has categories 1–5, not 1–6).
3. For each function card, confirm the one-sentence purpose does **not** drift
   into another function. Red flags:
   - Govern card mentions a specific metric → it has drifted into Measure.
   - Map card mentions mitigations or treatments → it has drifted into Manage.
   - Measure card promises action → it has drifted into Manage.
   - Manage card only lists risks without a decision → it has drifted back
     into Map.
4. Spot-check three category cards against the NIST AI RMF 1.0 Core document
   and the companion AI RMF Playbook text for each category. If the
   definition in the card contradicts the Playbook, revise.
5. Have a peer try to reconstruct the function from only the artefact column;
   if they cannot, the artefact is too generic.

## 4. Rubric or review checklist

Score each card out of 3; a passing deck averages ≥ 2.0 and has no 0s.

| Score | Criterion |
| --- | --- |
| **3 — strong** | Function/category named correctly; one-sentence purpose matches RMF Core; artefact is specific to a governance-analyst workflow. |
| **2 — acceptable** | Function/category named correctly; purpose roughly matches RMF Core; artefact is listed but generic (e.g., "documentation"). |
| **1 — weak** | Function/category named correctly but the purpose paraphrase blurs into another function, OR no artefact given. |
| **0 — fail** | Function/category misidentified, invented category number, or definition contradicts NIST AI RMF 1.0. |

Deck-level checks (binary, all must pass):

- [ ] All four functions present.
- [ ] Govern has six categories, Map five, Measure four, Manage four.
- [ ] No card confuses "functions" with "trustworthy characteristics".
- [ ] At least one card explicitly addresses third-party / supply-chain risk.
- [ ] Deck is usable offline — no card relies on external context to be
      understood.

## 5. Common mistakes

- **Treating the four functions as a waterfall.** Govern runs continuously;
  Map / Measure / Manage iterate. Flashcards that imply a one-time sequence
  misrepresent the framework.
- **Confusing the four functions with the seven trustworthy characteristics.**
  The functions are Govern / Map / Measure / Manage. The characteristics live
  under Measure 2.
- **Using category counts from an older draft.** The official RMF 1.0 Core
  fixes category counts (6 / 5 / 4 / 4); drafts and summaries sometimes list
  different totals. When in doubt, check the current RMF 1.0 document, not a
  secondary summary.
- **Writing artefacts that are really process steps** ("do a risk
  assessment"). An artefact is the output — a document, register, log, or
  decision — that a reviewer can later pick up.
- **Omitting Govern 6 / Map 4 / Manage 3 third-party cards.** Analysts who
  skip these cannot answer "how do we govern a vendor model?" in a council
  meeting.

## 6. References

- NIST, _AI Risk Management Framework (AI RMF 1.0)_, January 2023 — the Core
  (Govern, Map, Measure, Manage) and category list. <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, _AI RMF Playbook_ — expanded suggested actions per category, used to
  validate the artefact column. <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, _AI 600-1: Generative AI Profile_, July 2024 — referenced only for
  context on how the RMF Core is reused in a sector/technology profile. <https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf>
