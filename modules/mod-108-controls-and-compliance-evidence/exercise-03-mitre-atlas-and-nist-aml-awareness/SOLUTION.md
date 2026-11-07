# Exercise 03 — MITRE ATLAS and NIST AML Awareness Drill — Reference Solution

## 1. Solution overview

Adversarial attacks on AI systems have moved from research papers
into incident channels. Two reference taxonomies now carry the
vocabulary a governance analyst needs to read, write, and route
incidents without re-inventing terms:

- **MITRE ATLAS** (Adversarial Threat Landscape for Artificial-
  Intelligence Systems) — a tactics-and-techniques matrix in the
  shape of MITRE ATT&CK, populated with case studies of real
  attacks on ML/AI systems.
- **NIST Adversarial Machine Learning (AML)** — the taxonomy
  published by NIST (NIST AI 100-2e) that organises attacks by
  learning-stage (train-time vs. deploy-time), attacker
  capability, attacker knowledge, and attacker goal. It is the
  normative US-federal AML vocabulary.

The analyst's job is not to run adversarial testing — security and
ML engineering own that — but to:

1. Recognise the vocabulary in a red-team report or incident write-up.
2. Translate between ATLAS tactics/techniques and NIST AML categories
   so the risk register, incident log, and regulator notifications
   read consistently.
3. Spot which **control** the catalogue should carry to mitigate the
   named technique (hand-off to exercise-01 and exercise-05).

This solution provides:

- A short reference of the ATLAS tactics and the NIST AML attack
  families at the level an analyst needs.
- A worked tagging drill for six scenarios: for each, the ATLAS
  tactic (and example technique), the NIST AML classification, the
  likely OWASP LLM / ML Top 10 cross-tag (see exercise-02), and the
  control hand-off.
- A short incident-record schema.
- Decision rationale and a grading rubric.

Scope: this is a reading / drill exercise. The analyst produces the
tags and the routing, not the forensic analysis.

## 2. Worked answer or implementation

### 2.1 MITRE ATLAS — tactics the analyst must recognise

ATLAS is organised as tactics (adversary goals) × techniques
(methods). The current ATLAS matrix expands over time; the tactics
most relevant to a governance-analyst's reading loop are:

| Tactic | What it means for a non-adversarial reader |
|---|---|
| **Reconnaissance** | Attacker is learning about the target ML system — its endpoints, its architecture, its training-data provenance. |
| **Resource Development** | Attacker builds or acquires adversarial capability — adversarial datasets, poisoned models, open-weights artefacts. |
| **Initial Access** | Attacker obtains a foothold with ML-relevance (compromised model hub account, stolen API credentials, supply-chain intrusion). |
| **ML Model Access** | Attacker obtains the means to query or read the model (black-box API, grey-box access, white-box weights). |
| **Execution** | Attacker runs crafted inputs through the ML system (adversarial examples, prompt-injection payloads). |
| **Persistence** | Attacker retains access — poisoned weights, retained prompt-injection corpora, cached backdoor triggers. |
| **Defense Evasion** | Attacker modifies inputs or behaviour to bypass monitoring or detection. |
| **Discovery** | Attacker probes the system to learn decision boundaries, features, model internals. |
| **Collection** | Attacker gathers data from the ML system — training data, embeddings, model outputs. |
| **ML Attack Staging** | Attacker prepares the specific attack (crafted inputs, poisoning payload, prompt-injection document). |
| **Exfiltration** | Attacker extracts data or model artefacts. |
| **Impact** | Attacker produces the end effect — mis-classification, evasion, denial of service, extraction. |

Techniques (e.g. `AML.T0043 Craft Adversarial Data`, `AML.T0020
Poison Training Data`, `AML.T0057 LLM Prompt Injection`, `AML.T0048
External Harms`) live under these tactics. Use the current ATLAS
matrix for the authoritative technique ids; the matrix is updated
over time.

### 2.2 NIST AML — what the analyst needs to know

NIST's *Adversarial Machine Learning — A Taxonomy and Terminology
of Attacks and Mitigations* (NIST AI 100-2e; cite the edition used)
classifies attacks across four dimensions:

1. **Learning stage** — `training-time` (poisoning) vs. `inference-
   time` (evasion, extraction, inference).
2. **Attacker capability** — can the attacker influence training
   data? the model? queries?
3. **Attacker knowledge** — `white-box`, `grey-box`, `black-box`.
4. **Attacker goal** — `availability` (denial of service), `integrity`
   (induce wrong outputs), `privacy` (extract training data /
   membership / model), or combinations.

The NIST AML taxonomy also addresses **generative-AI-specific
attacks** — prompt injection (direct and indirect), prompt
extraction, model-poisoning on fine-tunes, and content-provenance
attacks — in the same four-dimension frame.

An analyst does not need to memorise the full taxonomy. The
analyst needs to be able to pick, for any reported incident,
answers to the four dimensions, plus a short phrase that places
the attack in one of three families: **poisoning**, **evasion /
inference-time attack**, or **privacy / extraction**. For
generative AI, add a fourth bucket: **prompt-based attack**
(injection, system-prompt extraction, jailbreak).

### 2.3 Tagging schema for the risk register / incident tracker

```yaml
aml_tag:
  atlas:
    taxonomy: "MITRE-ATLAS"
    version: string              # e.g. "v4.9.0"
    primary_tactic: string       # tactic id/name, e.g. "Impact"
    technique_ids: [string]      # e.g. ["AML.T0057"]
  nist_aml:
    taxonomy: "NIST-AI-100-2e"
    version: string              # e.g. "NIST AI 100-2e2025"
    attack_family: enum          # "poisoning" / "evasion" /
                                 # "privacy-extraction" / "prompt-attack"
    learning_stage: enum         # "training-time" / "inference-time"
    attacker_knowledge: enum     # "white-box" / "grey-box" / "black-box"
    attacker_goal: [enum]        # subset of
                                 # ["availability","integrity","privacy","misuse"]
  owasp_cross_tag: object|null   # see exercise-02 schema
  control_handoff: [string]      # control ids owed to catch / prevent
  tagger: string                 # role
  tagged_date: date
  reviewed_by_security: boolean
  reviewed_date: date|null
```

### 2.4 Worked drill — six scenarios

All vignettes are fictional and used only to exercise the tagging
loop.

#### Scenario A — Evasion via adversarial examples on a fraud model

A traditional fraud-detection classifier starts missing a cluster of
previously-flagged transactions after an attacker iteratively probes
the API and discovers small perturbations in two features that
cross the decision boundary.

- **ATLAS**: tactic **Impact** (end effect: evasion); technique
  **Evade ML Model** (`AML.T0015`) with staging via `AML.T0043
  Craft Adversarial Data` and prior `ML Model Access` through the
  black-box API.
- **NIST AML**: family **evasion**, learning stage
  **inference-time**, attacker knowledge **black-box** (probe-only),
  attacker goal **integrity**.
- **OWASP cross-tag**: OWASP ML Security Top 10 — adversarial
  attack (traditional-ML; not an LLM scenario).
- **Control hand-off**: input-anomaly monitor, periodic adversarial-
  robustness evaluation in the release gate, rate-limit + behavioural
  fingerprinting on the black-box API.

#### Scenario B — Training-data poisoning via a crowdsourced labelling pipeline

A model trained on crowdsourced labels shows degraded performance
on a subgroup after an attacker-controlled annotator seeded a
systematic mislabel pattern for that subgroup over three months.

- **ATLAS**: tactic **Resource Development** / **ML Attack Staging**
  / **Impact**; technique **Poison Training Data** (`AML.T0020`).
- **NIST AML**: family **poisoning** (data), learning stage
  **training-time**, attacker knowledge **grey-box** (knew the
  label taxonomy), attacker goal **integrity** (and plausibly
  **misuse** — downstream unfair outcome).
- **OWASP cross-tag**: OWASP LLM Top 10 **LLM04 Data and Model
  Poisoning** if the downstream model is an LLM; OWASP ML Security
  Top 10 data-poisoning category otherwise.
- **Control hand-off**: provenance of labels, duplicate-annotator
  detection, held-out subgroup-specific eval slices, periodic
  retraining on vetted sets.

#### Scenario C — Model-extraction attack via a public API

An attacker makes ~1 M queries against a public image-classification
API, trains a shadow model with the returned labels + confidences,
and ships a "free" version of the service.

- **ATLAS**: tactics **ML Model Access** (black-box API) → **Discovery**
  → **Collection** → **Exfiltration**; technique **Craft
  Adversarial Data** (for probing) and `AML.T0024 Exfiltrate via
  ML Inference API`.
- **NIST AML**: family **privacy-extraction** (model extraction),
  learning stage **inference-time**, attacker knowledge
  **black-box**, attacker goal **privacy** (model IP).
- **OWASP cross-tag**: OWASP ML Security Top 10 model-theft
  category.
- **Control hand-off**: API query-rate monitoring, confidence-score
  masking, legal — terms-of-service / contract — plus a decision
  record on whether to litigate (not an engineering control).

#### Scenario D — Indirect prompt injection via a retrieved document

An attacker publishes a public blog post whose text contains hidden
instructions. The organisation's LLM assistant retrieves that blog
post during normal operation and follows the embedded instructions
(including exfiltrating a short summary of the user's query to an
attacker-controlled endpoint via a crafted link).

- **ATLAS**: tactic **Initial Access** / **Execution** / **Impact**;
  technique **LLM Prompt Injection** (`AML.T0057`) — specifically
  the **indirect** variant.
- **NIST AML**: family **prompt-attack**, learning stage
  **inference-time**, attacker knowledge **black-box** (no model
  internals needed), attacker goal **integrity** (behaviour
  manipulation) + **privacy** (exfiltration).
- **OWASP cross-tag**: OWASP LLM Top 10 **LLM01 Prompt Injection**
  (primary), **LLM02 Sensitive Information Disclosure** (secondary).
- **Control hand-off**: retrieval-source trust tier, output filter
  for link-exfiltration patterns, prompt-injection evaluation in
  the release gate, egress controls on tool-call URLs.

#### Scenario E — Membership-inference attack

A researcher demonstrates that a healthcare model trained on a
rare-disease registry responds to specific crafted queries in a
way that discloses, with high probability, whether a given person
was in the training set.

- **ATLAS**: tactics **ML Model Access** → **Discovery** →
  **Collection**; technique **Infer Training Data Membership**
  (ATLAS technique id per the current matrix).
- **NIST AML**: family **privacy-extraction** (membership
  inference), learning stage **inference-time**, attacker knowledge
  **black-box**, attacker goal **privacy**.
- **OWASP cross-tag**: OWASP ML Security Top 10 membership-
  inference category.
- **Control hand-off**: output-confidence redaction, differential-
  privacy budget review during training, access-tier for sensitive-
  domain models, data-minimisation review (hand-off to the DPIA /
  FRIA — see mod-105).

#### Scenario F — Poisoned fine-tune pulled from a public model hub

A product team pulls a specialised instruction-tuned model from a
public hub. Security's analysis of the released weights finds an
embedded trigger phrase that, when present in a user message,
causes the model to emit a canned "developer mode" response with
no safety mitigations.

- **ATLAS**: tactic **Resource Development** / **Initial Access**;
  technique **Poison Model** / **Supply Chain Compromise of ML
  Dependencies** (per the current ATLAS matrix).
- **NIST AML**: family **poisoning** (model), learning stage
  **training-time** (the backdoor was implanted during fine-tune),
  attacker knowledge **white-box** (attacker controlled the
  fine-tune), attacker goal **integrity** + **misuse**.
- **OWASP cross-tag**: OWASP LLM Top 10 **LLM04 Data and Model
  Poisoning** (primary), **LLM03 Supply Chain** (secondary).
- **Control hand-off**: model-provenance policy (only approved
  hubs / signed artefacts), reproducible-build or hash-pinning on
  model weights, mandatory adversarial-trigger evaluation during
  intake (mod-102), vendor-attestation on externally-sourced models
  (exercise-04).

### 2.5 ATLAS ↔ NIST AML ↔ OWASP ↔ control crosswalk — short view

| ATLAS tactic | NIST AML family | OWASP LLM Top 10 (where LLM) | OWASP ML Sec Top 10 (where classical) | Primary control type |
|---|---|---|---|---|
| Execution / Impact (evasion) | evasion | — | adversarial attack | adversarial-robustness eval; input anomaly monitor |
| Resource Development / ML Attack Staging (data) | poisoning (data) | LLM04 | data-poisoning attack | label provenance; duplicate-annotator detection |
| Resource Development / Initial Access (model) | poisoning (model) | LLM04 + LLM03 | model-poisoning; AI supply chain | model-provenance policy; signed artefacts |
| ML Model Access → Collection → Exfiltration (extraction) | privacy-extraction | — | model-theft | query-rate monitoring; confidence masking |
| ML Model Access → Collection (membership inference) | privacy-extraction | — | membership-inference | DP-budget review; output redaction |
| Initial Access / Execution (prompt-based) | prompt-attack | LLM01 + LLM07 | — | retrieval trust tiering; output filters; release-gate eval |

### 2.6 Decision rationale

- **ATLAS gives the attack narrative; NIST AML gives the four-
  dimension taxonomy; OWASP gives the control bucket.** They are
  complementary, not redundant. A mature analyst tags against
  **all three** on serious incidents.
- **Cite ATLAS and NIST AML versions.** Both evolve; the current
  ATLAS matrix carries new techniques (notably in the generative-AI
  layer), and NIST AML has a 2023 edition plus updates. Pinning
  the version in `aml_tag` keeps the register comparable over time.
- **Family before technique.** For routing, "poisoning vs. evasion
  vs. extraction vs. prompt-attack" matters more than which
  specific technique id was used. Techniques are for the forensic
  report; families drive the control hand-off.
- **LLM-specific vs. ML-generic.** Not every ML attack has an LLM
  analogue and not every LLM attack has an ML analogue. Keep
  clean separation and tag accordingly.
- **Control hand-off is where the taxonomy earns its keep.** The
  purpose of the tag is to decide which control in exercise-05's
  crosswalk should be refreshed or added. A tag with no hand-off
  is cosmetic.

## 3. Validation steps

1. For each tagged item, confirm both `atlas` and `nist_aml`
   sub-objects are populated.
2. Confirm `atlas.version` and `nist_aml.version` match the editions
   in use at the organisation.
3. Cross-check the ATLAS technique id against the current ATLAS
   matrix; stale ids (removed in later versions) must be flagged.
4. Confirm `nist_aml.attack_family` is one of the five enumerated
   families (poisoning / evasion / privacy-extraction / prompt-
   attack / none-applicable).
5. Confirm at least one entry in `control_handoff` resolves to an
   existing control id in the catalogue; if not, open a control-
   gap finding.
6. Spot-check two tagged items with a second analyst; family
   agreement should be unanimous on these five families.
7. For every serious-incident tag, confirm `reviewed_by_security`
   is set before use in a regulator notification (EU AI Act Art.
   73, sector equivalents).

## 4. Rubric or review checklist

Grade the submission on six dimensions, 3 points each (total 18).
Pass = total ≥ 13 with no 0.

| Criterion | 3 — strong | 2 — acceptable | 0–1 — weak |
|---|---|---|---|
| **ATLAS literacy** | Correct tactic + technique id for each scenario; version cited. | Correct tactics; some technique ids missing. | Tactics named wrong or no ATLAS tags. |
| **NIST AML four-dimension tagging** | Every scenario carries family + learning-stage + attacker-knowledge + attacker-goal, all defensible. | Three of four dimensions populated. | Narrative without taxonomy. |
| **LLM vs. ML routing** | LLM / prompt scenarios cross-tagged to OWASP LLM Top 10; classical-ML scenarios cross-tagged to OWASP ML Security Top 10. | Mostly correct; one misroute. | One list used for everything. |
| **Control hand-off** | Each scenario names at least one control the catalogue should carry, mapped to an existing or proposed control id. | Hand-offs named but not mapped to control ids. | Taxonomy only; no hand-off. |
| **Version pinning** | ATLAS and NIST AML editions pinned per row. | Pinned once at the top of the register. | Not pinned. |
| **Readability for the council** | A one-sentence lay summary accompanies the taxonomy tags. | Tags only; readable but requires a translator. | Dense taxonomy strings only. |

Document-level checks (binary, all must pass):

- [ ] Every row names an ATLAS tactic.
- [ ] Every row names a NIST AML attack family.
- [ ] Every row names at least one control hand-off.
- [ ] Every row names `reviewed_by_security`.

## 5. Common mistakes

- **ATLAS tactic = attacker goal.** Tagging every attack as
  "Impact" collapses the kill chain. The analyst should name the
  tactic that most tightly matches the *mechanism under
  discussion*, not the final effect.
- **NIST AML family picked from the attacker's goal only.** An
  extraction attack whose goal is to reproduce a model is
  privacy-extraction; collapsing "privacy" and "misuse" into one
  bucket makes the family useless.
- **Using OWASP LLM Top 10 for a classical-ML system.** LLM01
  Prompt Injection does not apply to a gradient-boosted fraud
  classifier.
- **Forgetting the supply-chain layer.** Poisoning that rides in
  through a public model hub or a third-party dataset is a supply-
  chain issue and belongs tagged against ATLAS Resource
  Development / Initial Access and OWASP LLM03 (or the ML list's
  AI supply chain) in addition to the poisoning tag.
- **Prompt-injection stories tagged as "misinformation".** A
  misinformation outcome driven by injection is still a prompt-
  attack mechanism; LLM09 at best is a secondary.
- **Overfit to a single technique id.** The analyst gets fixated on
  `AML.T0043` and misses the tactic. The family drives the
  control; the technique drives the forensics.
- **No control hand-off.** The tag is only useful if a control is
  being created or refreshed as a result. Tagging without hand-off
  is a reading exercise, not a governance control.
- **Publishing a serious-incident tag without security review.** The
  analyst's tag is a draft until security confirms.

## 6. References

- MITRE ATLAS — Adversarial Threat Landscape for Artificial-
  Intelligence Systems. Tactics, techniques, and case studies used
  throughout this drill. Cite the ATLAS matrix version on every
  tag.
  <https://atlas.mitre.org/>
- NIST, *Adversarial Machine Learning: A Taxonomy and Terminology
  of Attacks and Mitigations* (NIST AI 100-2e; cite the edition
  in use). <https://csrc.nist.gov/pubs/ai/100/2/e2025/final>
- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 —
  the governance frame the AML taxonomy integrates with (`MAP 4.1`,
  `MEASURE 2.*`).
  <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, *AI RMF Generative AI Profile* (NIST AI 600-1), 2024 —
  generative-AI risk vocabulary complementary to AML prompt-attack
  tagging.
  <https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf>
- OWASP Top 10 for LLM Applications & Gen AI Security Project —
  LLM-side cross-tag.
  <https://genai.owasp.org/llm-top-10/>
- OWASP Machine Learning Security Top 10 — classical-ML cross-tag.
  <https://owasp.org/www-project-machine-learning-security-top-10/>
- Regulation (EU) 2024/1689 — EU AI Act. Art. 15 (robustness,
  accuracy, cybersecurity) and Art. 73 (serious-incident
  reporting) are the regulator-side anchors for AML tagging.
  <https://eur-lex.europa.eu/eli/reg/2024/1689/oj>
