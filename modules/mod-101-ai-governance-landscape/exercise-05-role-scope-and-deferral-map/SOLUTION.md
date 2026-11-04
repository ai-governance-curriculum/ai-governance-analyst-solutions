# Exercise 05 — Role Scope and Deferral Map — Reference Solution

## 1. Solution overview

An AI Governance Analyst sits in the middle of a crowded org chart: legal,
security, privacy, model-development, product, risk, compliance, and the AI
council all touch AI systems. The point of a role-scope and deferral map is
to answer three questions crisply:

1. **Owned** — what does the AI Governance Analyst produce and sign off on?
2. **Contributed-to** — where does the analyst add input but someone else
   owns the decision?
3. **Deferred** — where must the analyst explicitly hand off to another
   function, and to whom?

This solution provides a reference map, a RACI-style matrix across the
lifecycle, deferral patterns for common ambiguous situations, validation
steps, and a rubric.

## 2. Worked answer or implementation

### 2.1 The role — scope in one paragraph

The AI Governance Analyst is responsible for the governance evidence trail
of an AI system across its lifecycle: intake and inventory, risk assessment
drafting, impact assessment drafting, regulatory tier classification,
controls traceability, review-packet preparation, and incident-record
composition. The analyst is **not** the accountable decision-maker for
deployment, legal exposure, or technical remediation; the analyst produces
the artefacts those decisions rely on.

### 2.2 Scope map

| Scope band | Typical items | Analyst posture |
| --- | --- | --- |
| **Owned (A/R)** | AI inventory entry; intake questionnaire; risk register entry; impact assessment draft; regulatory-tier classification note; controls mapping to policy/standards; review packet for council; incident record composition. | Author and keep current. |
| **Contributed (C)** | Model card content; eval report interpretation; data-governance artefacts; vendor questionnaire responses; monitoring plan metrics; disclosure statements. | Contribute language and risk framing; someone else signs. |
| **Informed (I)** | Infrastructure security posture; model training runs; detailed privacy-impact assessment outside the AI context; litigation strategy. | Read-only; incorporate conclusions into governance artefacts. |
| **Deferred (D)** | Final legal interpretation of regulation; accept/transfer of residual risk; go/no-go deployment decision; technical fix of a model defect; authority to communicate externally. | Explicitly hand off with a named owner and a dated artefact. |

### 2.3 RACI across the AI lifecycle

Legend: **R** responsible, **A** accountable, **C** consulted, **I**
informed. Rows are lifecycle stages; columns are roles. The AI Governance
Analyst (GA) is the focal role; the map names standard adjacent roles the
analyst most often works with.

| Stage | GA (analyst) | AI System Owner / Product | ML / Model Dev | Security | Privacy / DPO | Legal / Compliance | Risk Officer | AI Council / Exec |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Intake & inventory | **R/A** | C | I | I | I | I | I | I |
| Use-case definition | C | **R/A** | C | I | I | C | I | I |
| Regulatory tier tag | **R** | C | I | I | C | **A** | I | I |
| Risk assessment | **R** | C | C | C | C | C | **A** | I |
| Impact assessment (societal / rights) | **R** | C | C | I | C | C | **A** | C |
| Data-protection impact assessment (DPIA) | C | C | I | C | **R/A** | C | C | I |
| Threat model / adversarial testing plan | C | C | C | **R/A** | I | I | C | I |
| Eval plan | C | C | **R/A** | C | I | I | C | I |
| Controls mapping to framework | **R/A** | I | C | C | C | C | C | I |
| Review packet composition | **R/A** | C | C | C | C | C | C | C |
| Go / no-go decision | C | C | I | C | C | C | C | **R/A** |
| Residual-risk acceptance | **R** (drafts) | C | I | I | C | C | **A** | I |
| External disclosure / transparency text | **R** (drafts) | C | I | I | C | **A** | I | C |
| Monitoring plan | **R** | C | C | C | C | I | C | I |
| Incident triage | C | C | C | **R** | C | C | **A** | I |
| Incident record composition | **R/A** | C | C | C | C | C | C | I |
| Post-incident review | **R** | C | C | C | C | C | **A** | C |
| Decommissioning record | **R/A** | C | I | I | C | I | C | I |

### 2.4 Deferral patterns

The analyst's judgement call is almost always "who should own this?" not
"what is the answer?" The patterns below name the hand-off.

| Situation | Defer to | Analyst's remaining job |
| --- | --- | --- |
| A stakeholder asks whether a specific use case violates the EU AI Act. | **Legal / Compliance**. | Tag the tier on the record, document the question, and record the legal opinion as an artefact. Do not issue a legal opinion. |
| A privacy harm is identified during impact assessment. | **Privacy / DPO**. | Open a DPIA request; keep the AI impact assessment pointing to the DPIA ID. |
| A new adversarial attack vector is identified during review. | **Security**. | Record the vector in the risk register and request a threat model update; do not design mitigations alone. |
| A model exhibits performance regressions. | **ML / Model Development**. | Capture eval evidence and exit criteria; do not rewrite tests or re-tune. |
| Residual risk needs acceptance after mitigations. | **Risk Officer / Business owner** (per policy). | Prepare the residual-risk statement; obtain dated acceptance. |
| An incident may be reportable to a regulator. | **Legal / Compliance**, with **Communications** in support. | Compose the incident record; do not file or communicate externally. |
| A council member asks the analyst to recommend deployment. | **AI Council / Executive sponsor**. | Present evidence; recommend only within the analyst's defined authority (if any); do not approve. |
| A journalist or customer asks about AI behaviour. | **Communications**, with **Legal**. | Supply factual briefing notes; do not respond publicly without authorisation. |
| A vendor refuses to answer the AI due-diligence questionnaire. | **Procurement / Vendor Risk**. | Document gaps; do not approve the vendor on insufficient evidence. |

### 2.5 Decision rationale

- **A/R lives with the governance artefact, not the decision.** The analyst
  is accountable for the quality of the risk register, impact assessment,
  and review packet — not for the business decision those artefacts inform.
  Treating the analyst as the decision-maker invites role creep and
  over-ruling of risk and legal.
- **Regulatory-tier tagging is R for the analyst but A for Legal /
  Compliance.** The analyst can classify fluently, but the legal
  interpretation of the regulation sits with Legal. The split keeps speed
  without losing authority.
- **Residual-risk acceptance is drafted (R) by the analyst but accepted (A)
  by Risk / the business owner.** The analyst never accepts risk on behalf
  of the organisation.
- **Incident composition and post-incident review are analyst-owned, but
  incident triage is security-owned.** This mirrors how most mature orgs
  draw the line: triage is technical and time-sensitive; the governance
  record is the analyst's write-up after the fact.
- **External communication is always deferred.** Even when the analyst
  drafts the language, release goes through Legal and Communications.

## 3. Validation steps

1. Walk the RACI column-by-column: every role should have at least one **A**
   somewhere in the lifecycle. If a role has no accountability anywhere, the
   map is incomplete.
2. Walk each row: exactly one **A** per row. Two A's means the decision will
   stall; zero A's means the decision will fall to the analyst by default,
   which is role creep.
3. For every "Deferred" item in §2.4, confirm the receiving role is named
   (not just "someone else") and the analyst's residual job is defined.
4. Check that the map does not place the analyst as A on any of: legal
   interpretation, residual-risk acceptance, go/no-go deployment, external
   disclosure, technical fix. If any of these are A on the analyst row,
   revise.
5. Sanity-check against local org structure: if your organisation does not
   have, say, a Risk Officer role, the A for residual-risk acceptance has
   to land somewhere real (usually a Business Owner or Executive Sponsor).
   The map should show the actual name, not the generic role.

## 4. Rubric or review checklist

Score each lifecycle stage out of 3; passing average ≥ 2.2 with no
Accountability column left blank.

| Score | Criterion |
| --- | --- |
| **3 — strong** | Correct R and A, with named roles that exist in the org; deferral is explicit when required. |
| **2 — acceptable** | Correct R and A; C/I slightly over- or under-assigned. |
| **1 — weak** | A assigned to the wrong role (e.g., analyst on go/no-go) or missing. |
| **0 — fail** | Multiple A's on the same row, or analyst listed as A for legal interpretation, residual-risk acceptance, go/no-go, or technical remediation. |

Deck-level checks:

- [ ] Analyst is R/A for governance artefacts (intake, risk register, impact
      assessment, controls map, review packet, incident record).
- [ ] Analyst is **not** A for go/no-go, residual-risk acceptance, legal
      interpretation, or external communication.
- [ ] Every "Deferred" item names a specific receiving role and the analyst's
      residual job.
- [ ] At least one row explicitly includes the AI Council or executive
      sponsor as A (go/no-go is the usual one).
- [ ] The map survives the "who signs?" test on every row.

## 5. Common mistakes

- **Analyst as approver.** The most frequent error is placing the analyst
  as A for residual-risk acceptance or deployment. The analyst produces
  the evidence; someone with authority accepts.
- **Analyst as external spokesperson.** Even when the analyst drafts
  disclosure text, release is Legal + Communications. The map should make
  this explicit.
- **"Everyone is consulted, no one is accountable."** Adding C to every
  cell is comforting and useless. Each row needs one A.
- **Pretending the DPO role doesn't exist.** When personal data is
  involved, DPIA ownership sits with Privacy/DPO. AI impact assessment
  and DPIA are distinct documents that reference each other.
- **No deferral pattern for ambiguous situations.** Without an explicit
  "who do I escalate to?" the analyst becomes the default decision-maker.
  The §2.4 patterns are the escape valve.
- **Role names that do not exist in the org.** Using textbook role names
  (e.g., "Risk Officer") when the org has only a "Head of Audit" leaves
  the A cell orphaned. The map should use the real titles the organisation
  uses.

## 6. References

- NIST, _AI Risk Management Framework 1.0_ — Govern function, specifically
  Govern 2 (accountability structures) and the suggested actions on roles
  and responsibilities. <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, _AI RMF Playbook_ — expanded suggested actions on role definition
  and third-party supplier accountability. <https://www.nist.gov/itl/ai-risk-management-framework>
- ISO/IEC 42001:2023, _AI Management System_ — Clause 5.3 (roles,
  responsibilities, authorities) and Clause 7.2 (competence).
- Regulation (EU) 2024/1689 (AI Act) — definitions of **provider**,
  **deployer**, **importer**, **distributor** and their obligations are the
  legal substrate behind the "deferred" column. Articles 3 and 25.
