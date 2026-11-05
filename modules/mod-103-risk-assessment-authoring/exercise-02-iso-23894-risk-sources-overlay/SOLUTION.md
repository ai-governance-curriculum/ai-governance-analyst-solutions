# Exercise 02 — ISO/IEC 23894 Risk Sources Overlay — Reference Solution

## 1. Solution overview

**ISO/IEC 23894:2023 — _Information technology — Artificial intelligence —
Guidance on risk management_** sits on top of the generic ISO 31000 risk
management framework and gives AI-specific guidance. For an analyst, the
practical value is in its **AI-specific risk sources** (set out in an
informative annex) and its AI-specific risk-management objectives
(another informative annex). Teams that have already built a first-draft
risk register against NIST AI RMF 1.0 use ISO/IEC 23894 as an **overlay**
— a cross-check that asks "did your register surface every AI-specific
risk source the standard lists?"

This solution provides:

- A reference listing of the AI-specific risk-source categories ISO/IEC
  23894 calls out, with short analyst-facing framings.
- A worked overlay against the risk register produced in `exercise-01`,
  with gaps identified and new rows added.
- Decision rationale for how the overlay is done.
- A rubric for grading a learner's overlay.

Scope note: the exercise uses the ISO/IEC 23894 risk-source categories as
an analyst checklist. Direct quotation of clause text is intentionally
avoided — the exercise is practitioner overlay, not standards redistribution.
Clause and annex numbers should be verified against a licensed copy before
publication to stakeholders.

## 2. Worked answer or implementation

### 2.1 ISO/IEC 23894 risk-source categories (analyst framing)

The informative risk-source annex organises AI-specific risk sources into
categories that complement (rather than replace) a generic risk taxonomy.
A reference analyst keeps a one-line framing for each, phrased as a
question to ask of the system under assessment.

| # | ISO/IEC 23894 risk-source category | Analyst question |
| --- | --- | --- |
| S1 | Complexity of environment | What makes the deployment environment harder than a classical IT system — open-world inputs, interacting agents, feedback loops? |
| S2 | Lack of transparency and explainability | Which decisions does the system influence that a human reviewer cannot reconstruct? |
| S3 | Level of automation | What decisions or actions does the system take on its own, and what is the human reversal path? |
| S4 | Machine-learning-specific risk sources | Training-data provenance and quality; distribution shift; underspecification; adversarial robustness; reproducibility. |
| S5 | System hardware issues | Compute, accelerators, memory, and the risk that hardware failure modes manifest as model-quality failures. |
| S6 | System software and architecture | Platform, libraries, pipelines; failure modes that propagate from software into model behaviour. |
| S7 | System lifecycle issues | Changes across development, deployment, operation, update, retirement; drift in the artefact against its documentation. |
| S8 | Technology readiness | Maturity of components (research-grade vs. production-grade) and the risk of overestimating what is production-ready. |
| S9 | Human factors / workforce | Skills gaps, hand-offs, operator over- or under-reliance, training. |
| S10 | Interactions with third parties and the value chain | Vendors, data suppliers, downstream users, open-source components. |
| S11 | Societal / ethical considerations | Fairness, autonomy, dignity, environmental and workforce impact. |

<!-- clause-verification: confirm the exact S-category names and their
grouping against a licensed copy of ISO/IEC 23894:2023 Annex B before
distributing this table to stakeholders. The framing questions are
analyst guidance; the authoritative wording lives in the standard. -->

### 2.2 Overlay against `exercise-01` risk register (PolicyAssistant)

Below is the register from `exercise-01` with an added ISO/IEC 23894
risk-source column, followed by gap-closure rows that the overlay
uncovers.

| ID | Risk statement (abbrev.) | NIST Map cat. | AI 600-1 tag | ISO/IEC 23894 source(s) |
| --- | --- | --- | --- | --- |
| R-01 | Model confabulates policy details → wrong action. | Map 4 (model) | Confabulation | S2 Transparency, S4 ML, S8 Readiness |
| R-02 | Prompt injection via retrieved doc → disclosure / behaviour change. | Map 4 (index) | Information security | S1 Env., S4 ML, S6 Software |
| R-03 | Over-permissive retrieval → data-privacy incident. | Map 4 (index) | Data privacy | S6 Software, S10 Third parties |
| R-04 | System used for in-scope-later cases → regulated-advice exposure. | Map 1, Map 5 | Human–AI configuration | S3 Automation, S9 Human factors |
| R-05 | Vendor LLM behaviour change on update → regression. | Map 4 (vendor) | Value chain | S7 Lifecycle, S10 Third parties |
| R-06 | EU AI Act Art. 50 transparency not met. | Map 1 | — | S11 Societal |
| R-07 | Accessibility gap in UI. | Map 5 | — | S11 Societal, S9 Human factors |
| R-08 | Lower factuality in languages added post-launch. | Map 5 | Harmful bias & homogenisation | S4 ML, S11 Societal |
| R-09 | Logs retain conversation PII beyond policy. | Map 4 (logs) | Data privacy | S6 Software, S7 Lifecycle |

### 2.3 Gaps the overlay surfaces (new rows to add)

Reviewing the ISO categories against the existing register, three
categories are under-represented. The analyst adds the following rows:

| ID | New risk statement | Why the overlay surfaced it | Source(s) |
| --- | --- | --- | --- |
| R-10 | Dependence on vendor-managed accelerator pool → regional outage or capacity crunch degrades service for critical employee workflows (e.g., leave intake during open enrolment). | S5 Hardware is not in the AI 600-1 list and did not surface from Map 4 unless the analyst explicitly looked at infrastructure. | S5 Hardware, S10 Third parties |
| R-11 | Reproducibility: evaluation results captured at launch cannot be reproduced after a vendor silent update, because the model version was not pinned/recorded. | S4 ML risk source, reproducibility aspect — a long-tail issue the NIST walkthrough does not force out. | S4 ML, S7 Lifecycle |
| R-12 | Governance artefacts (system card, risk register, evals) drift from the running system after minor changes that bypass the AI change-control process. | S7 Lifecycle — artefact drift is the classic lifecycle failure the standard calls out. | S7 Lifecycle, S9 Human factors |

### 2.4 Decision rationale

- **Overlay, not replacement.** NIST AI RMF 1.0 drives the structure of
  the register (Map / Measure / Manage flow); ISO/IEC 23894 is used to
  stress-test coverage. Teams that try to maintain two parallel registers
  spend more effort on reconciliation than on risk work.
- **Multiple ISO sources per row allowed.** Real-world risks rarely
  split cleanly along one category. The rubric rewards honest mapping
  over single-tagging.
- **Hardware (S5), lifecycle (S7), and workforce (S9) are the usual
  gap-finders.** NIST walkthroughs tend to produce model- and
  data-centric registers; ISO/IEC 23894's wider aperture pulls in
  infrastructure and process risk.
- **Reproducibility gets its own row (R-11).** Treating reproducibility
  as a sub-point of "model risk" hides it. Making it explicit creates a
  Measure 4 obligation (feedback on measurement efficacy).
- **Artefact drift (R-12) is governance risk, but it belongs in the
  risk register.** Governance failures manifest as unmanaged risk;
  keeping them in the same register as technical rows forces the same
  treatment discipline.

## 3. Validation steps

1. Confirm every row in the original register has at least one ISO/IEC
   23894 risk-source tag. Rows that defy tagging usually mean the
   statement is too generic; rewrite before dropping.
2. Confirm every ISO risk-source category is referenced by at least one
   row — or carries an explicit "not applicable, because …" note. The
   overlay is incomplete if a category is silently ignored.
3. For each new row added by the overlay (R-10, R-11, R-12 in the
   worked example), confirm it is a distinct risk, not a re-statement of
   an existing row at a different altitude.
4. Confirm ISO terminology is used verbatim where it matters — in
   particular, keep "risk source" (ISO) separate from "risk" (ISO 31000
   /23894: a risk is the effect of uncertainty on objectives, and a risk
   source has the potential to give rise to risk). Mixing the two makes
   the register incoherent.
5. Verify clause / annex numbers against a licensed copy of ISO/IEC
   23894:2023 before publishing externally. Leave `clause-verification`
   markers in place until that verification is done.

## 4. Rubric or review checklist

Score each row out of 3; overlay passes at average ≥ 2.0 with no 0s.

| Score | Criterion |
| --- | --- |
| **3 — strong** | ISO risk source(s) tagged; mapping is defensible; wording distinguishes risk source from risk. |
| **2 — acceptable** | ISO risk source(s) tagged; mapping plausible; one or two rows could take an additional source. |
| **1 — weak** | ISO tag present but mismatched; or only a single source attached when multiple obviously apply; or confuses risk and risk source. |
| **0 — fail** | Invented risk sources, or tags that contradict ISO/IEC 23894 definitions. |

Overlay-level checks (binary, all must pass):

- [ ] Every original row carries at least one ISO tag.
- [ ] Every ISO risk-source category is either used or explicitly N/A.
- [ ] At least one gap-closure row was added (hardware, lifecycle, or
      workforce categories typically surface at least one).
- [ ] ISO terminology is applied consistently: "risk source" vs "risk".
- [ ] Clause/annex numbers verified against a licensed ISO/IEC
      23894:2023 copy before publication, or marked
      `<!-- clause-verification: ... -->` until verified.

## 5. Common mistakes

- **Treating ISO/IEC 23894 as a stand-alone risk register template.** It
  is guidance, built on ISO 31000. The overlay fits into whatever risk
  framework the organisation already runs — do not maintain a parallel
  "ISO register" and a "NIST register".
- **Mapping every row to a single ISO source for tidiness.** Real risks
  cross categories. The exercise is diagnostic, not taxonomic.
- **Confusing "risk source" with "risk".** A hardware fault is a risk
  source; the risk is "accelerator outage during open enrolment →
  employees cannot reach policy information → benefits deadlines missed".
- **Dropping lifecycle / workforce / hardware categories because "they
  did not apply to the model".** They apply to the system; the overlay's
  value is making that explicit.
- **Citing ISO clause numbers from memory.** Analyst drafts that quote a
  clause number without checking the licensed standard tend to be wrong
  or out of date. Use `<!-- clause-verification -->` until verified.
- **Treating the overlay as one-shot.** The overlay re-runs on the risk
  register at each major change — otherwise it becomes an artefact
  snapshot that no longer reflects the system (R-12).

## 6. References

- ISO/IEC 23894:2023 — _Information technology — Artificial intelligence
  — Guidance on risk management_ (licensed copy required for clause-level
  verification). <https://www.iso.org/standard/77304.html>
- ISO 31000:2018 — _Risk management — Guidelines_ (the generic framework
  ISO/IEC 23894 builds on). <https://www.iso.org/iso-31000-risk-management.html>
- NIST, _AI Risk Management Framework (AI RMF 1.0)_, January 2023 — the
  base register structure the overlay stress-tests. <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, _AI 600-1: Generative AI Profile_, July 2024 — cross-referenced
  to anchor GenAI-specific rows. <https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf>
- OWASP, _Machine Learning Security Top 10_ — complements the ISO/IEC
  23894 ML risk-source category (S4). <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE ATLAS — complements the ISO/IEC 23894 ML risk-source category
  (S4) with adversary-technique vocabulary. <https://atlas.mitre.org/>
