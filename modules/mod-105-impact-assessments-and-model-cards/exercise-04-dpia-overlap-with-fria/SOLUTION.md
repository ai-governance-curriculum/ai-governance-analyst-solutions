# Exercise 04 — DPIA Overlap With the FRIA — Reference Solution

## 1. Solution overview

A European deployer of an in-scope high-risk AI system frequently has to
produce **two** impact assessments on the same project:

- a **DPIA** under GDPR Article 35, because personal data is processed
  and the processing is likely to result in high risk; and
- a **FRIA** under EU AI Act Article 27, because the deployer or the
  use case is in scope.

Running these as two disconnected documents duplicates work, causes
them to drift out of sync, and creates reviewer fatigue. The AI Act
itself anticipates the overlap: **Article 27(4)** states that, where
any of the Article 27(1) obligations is already satisfied by the
Article 35 DPIA, the **FRIA complements** the DPIA — the deployer does
not redo work that the DPIA has already done. Conversely, the DPIA's
risk-to-rights-and-freedoms assessment is a superset of some FRIA
content but a subset of others.

This solution delivers:

- A clause-level mapping between GDPR Art. 35(7)(a)–(d) and EU AI Act
  Art. 27(1)(a)–(f).
- A rule for each cell: **reuse**, **extend**, **FRIA-only**, or
  **DPIA-only**.
- A worked example on the `EuroCreditScore` scenario from
  `mod-105/exercise-03`, showing which DPIA sections the FRIA cites by
  reference vs. which the FRIA must write fresh.
- Decision rationale and rubric.

## 2. Worked answer or implementation

### 2.1 Clause-level mapping

| FRIA element (AI Act Art. 27(1)) | DPIA element (GDPR Art. 35(7)) | Overlap verdict | How the deployer should treat it |
| --- | --- | --- | --- |
| **(a)** Description of the deployer's processes using the system, in line with intended purpose. | **(a)** Systematic description of envisaged processing operations and purposes. | **Overlaps but perspective differs.** The DPIA writes the processing from a data-flow standpoint; the FRIA writes the same workflow from a decision standpoint. | **Reuse the system description**; extend with the AI-specific decision flow (input → model → score → action → human path) that the DPIA does not necessarily cover. |
| **(b)** Period of time and frequency of use. | *No direct 35(7) equivalent* — captured indirectly in the DPIA's description of processing and retention. | **FRIA-only element** that the DPIA usually glosses. | **Write fresh in the FRIA.** |
| **(c)** Categories of natural persons and groups likely to be affected. | **(c) / (a)** categories of data subjects. | **Overlaps substantially** but the FRIA extends to persons not in the data (e.g., people indirectly affected by a decline, non-applicants represented in the training set). | **Reuse the DPIA's data-subject categories as a floor**, then **extend** with Charter-grounded groups and indirectly affected persons. |
| **(d)** Specific risks of harm to the categories in (c), taking into account the Art. 13 information from the provider. | **(c)** Risks to the rights and freedoms of data subjects. | **Partial overlap, different frame.** DPIA risks are processing-centric (unlawful access, retention-beyond-necessity, re-identification). FRIA risks are decision-centric (disparate impact, reason-code insufficiency, automation bias). | **Reuse DPIA risks that relate to decisions or discrimination** (there are usually a few); **write fresh** the system-behaviour risks that the DPIA does not cover. Critically, **cite the provider's Art. 13 information** — a DPIA will not have done this. |
| **(e)** Implementation of human-oversight measures, per the instructions for use. | *Loose equivalent* — the DPIA records technical and organisational measures around the processing (35(7)(d)) but not model-level oversight. | **FRIA-dominant.** | **Write fresh.** A reuse-only answer fails Art. 27(1)(e). |
| **(f)** Measures in case of risk materialisation, internal governance, complaint mechanisms. | **(d)** Measures envisaged to address the risks, including safeguards and complaint mechanisms. | **Overlaps for the privacy-incident case**; FRIA extends to non-privacy harms (e.g., statutory-benefit delay, financial exclusion). | **Reuse the DPIA's measures as the starting point**; **extend** to cover non-privacy complaint channels, rollback / stop-use switch, serious-incident reporting under Art. 73. |

### 2.2 Combined-document pattern

The cleanest filing pattern is a **single combined artefact with two
owning authorities**:

```
Project impact assessment — <system>
├── 0. Front matter (shared)
├── 1. System description (shared, extended by FRIA with decision flow)
├── 2. Legal basis & purposes (DPIA-anchored)
├── 3. Necessity & proportionality (DPIA)                 ← GDPR 35(7)(b)
├── 4. Categories of persons / data subjects (shared)
├── 5. Risks
│      5a. Privacy risks (DPIA)                            ← GDPR 35(7)(c)
│      5b. Fundamental-rights risks (FRIA)                 ← AI Act 27(1)(d)
├── 6. Measures
│      6a. Privacy measures (DPIA)                         ← GDPR 35(7)(d)
│      6b. Human-oversight measures (FRIA)                 ← AI Act 27(1)(e)
│      6c. Response, complaints, rollback (shared)         ← AI Act 27(1)(f) + GDPR 35(7)(d)
├── 7. Consultation and sign-off (DPO + Head of Compliance)
└── 8. Notification duties
       8a. Prior consultation with supervisory authority if residual high risk (GDPR 36)
       8b. Market-surveillance notification of FRIA results (AI Act 27(3))
       8c. EU high-risk AI database entry (AI Act 71)
```

### 2.3 Worked example on `EuroCreditScore`

Using the scenario from `mod-105/exercise-03`.

| Section | DPIA content | FRIA addition | How the FRIA cites the DPIA |
| --- | --- | --- | --- |
| **System description** | End-to-end data flow: applicant submits data → joined with bureau data → scored → decision → retention. | Decision-path annotation: approve / refer / decline bands; where a human is or is not in the loop. | FRIA §(a) cites DPIA §1 and appends the decision flow. |
| **Necessity & proportionality** | Bank's legitimate interest in credit decisioning, with proportionality argument on the data categories used. | Not an Art. 27(1) element; left in the DPIA. | FRIA notes "see DPIA §3". |
| **Categories of persons** | Applicants, co-signers, joint applicants. | Adds: thin-file applicants; applicants protected under Charter Art. 21; applicants in rural branches. | FRIA §(c) cites DPIA §4 and extends. |
| **Privacy risks** | Unauthorised access, retention beyond necessity, re-identification from bureau cross-reference. | Not directly a FRIA element, but some overlap with (d). | FRIA cites DPIA §5a; notes the rare items that are both privacy and rights risks (e.g., fairness-sensitive feature leaking into logs). |
| **Rights-impact risks** | A GDPR-tuned DPIA already lists discrimination, Art. 22 automated-decision risk. | Adds model-behaviour risks tied to the provider's Art. 13 limitations (thin-file miscalibration, subgroup disparate impact). | FRIA §(d) adopts the DPIA's discrimination entries and adds model-behaviour entries that cite the vendor's Art. 13 document. |
| **Measures — privacy** | Access controls, encryption, retention schedule, DPO sign-off. | Not a FRIA element; stays in DPIA. | FRIA §(e)–(f) cross-reference. |
| **Measures — human oversight** | *Usually absent from a DPIA.* | Four-eyes on protected-attribute proxy declines; underwriter review on every "refer"; stop-use switch; anti-automation-bias training. | FRIA §(e) writes fresh. |
| **Measures — response / complaints** | Data-subject rights requests under GDPR Articles 15–22; DPO as intake. | Dedicated automated-decision complaint route in the consumer-credit function; Art. 73 serious-incident reporting; rollback trigger. | FRIA §(f) extends DPIA §6c. |
| **Notification duties** | Prior consultation with supervisory authority under GDPR Art. 36 if residual high risk. | Market-surveillance notification under AI Act Art. 27(3); EU database registration under Art. 71. | Both listed in §8; neither obsoletes the other. |

### 2.4 Decision rationale

- **Reuse is explicitly authorised.** Article 27(4) says the FRIA
  **complements** an existing DPIA. The reader is expected to see the
  deployer leverage the DPIA, not re-draft it.
- **Reuse does not mean "all the same".** The FRIA adds elements the
  DPIA does not have: (b) frequency/period, (d) risks grounded in the
  provider's Art. 13 information, (e) human oversight, and parts of
  (f) that cover non-privacy harms.
- **Different ownership, one artefact.** The DPO owns the DPIA; the
  governance / compliance function owns the FRIA. A combined artefact
  with two owning authorities is cleaner than two documents that will
  drift.
- **Different notification channels.** A DPIA may trigger prior
  consultation with the supervisory authority under GDPR Art. 36; a
  FRIA must be notified to the market-surveillance authority under AI
  Act Art. 27(3). Neither path replaces the other.
- **The "categories of persons" extension is where omissions hide.**
  The DPIA reliably names applicants as data subjects, but it does not
  reliably name thin-file applicants, rural applicants, or Charter
  Art. 21 sub-groups. FRIA reviewers look there first.

## 3. Validation steps

1. Confirm a DPIA exists and that it was performed before or alongside
   the FRIA. If the DPIA post-dates the FRIA, the FRIA is operating on
   provisional personal-data facts and will need refresh.
2. For every FRIA element (a)–(f), tick one of **reuse**, **extend**,
   **FRIA-only**, **DPIA-only**. No element may be left uncategorised.
3. Where the verdict is **reuse**, the FRIA text may be a one-liner
   that cites the DPIA section and quotes nothing — this is the
   intent of Art. 27(4).
4. Where the verdict is **extend**, check that the FRIA adds concrete
   content, not just "and additional fundamental-rights risks".
5. Confirm both notification duties are listed (GDPR Art. 36 trigger
   evaluated; AI Act Art. 27(3) notification scheduled).
6. Peer check: an auditor reading only the FRIA plus the DPIA
   cross-references should be able to tell, for every Art. 27(1)
   element, which document holds the authoritative answer.

## 4. Rubric or review checklist

### Mapping quality (0–3 per FRIA element)

| Score | Criterion |
| --- | --- |
| **3 — strong** | Verdict (reuse / extend / FRIA-only) is correct; citation to the DPIA section is precise; extensions are concrete. |
| **2 — acceptable** | Verdict correct; citation present but non-specific ("see DPIA"). |
| **1 — weak** | Verdict defensible but the FRIA re-writes content the DPIA already covers, bloating the artefact. |
| **0 — fail** | DPIA content is contradicted by the FRIA; or a FRIA-only element (b or e) is claimed as reused. |

### Overall checks (binary, all must pass)

- [ ] DPIA exists and is cited by section.
- [ ] Every FRIA element (a)–(f) has a verdict assigned.
- [ ] Reuse verdicts carry a precise section citation, not "see DPIA".
- [ ] Extensions are concrete, not placeholder text.
- [ ] Human-oversight measures are written fresh, not quoted from the
      DPIA.
- [ ] Complaint and rollback paths cover non-privacy harms, not only
      GDPR rights requests.
- [ ] Both notification duties (GDPR Art. 36 trigger evaluated; AI Act
      Art. 27(3) notification) are listed.
- [ ] Owners are named for both artefacts (DPO for DPIA; governance /
      compliance for FRIA).

## 5. Common mistakes

- **Treating the FRIA as a DPIA with a fresh cover page.** It is not:
  Art. 27(1)(b), (d), and (e) are FRIA-only content in practice.
- **Treating the DPIA as a FRIA with the privacy bits removed.** The
  DPIA has its own statutory structure (Art. 35(7)) and loses legal
  utility if that structure is cannibalised for the FRIA.
- **"See DPIA" everywhere.** A FRIA that cites the DPIA for every
  element is not complementing — it is delegating. Art. 27 requires
  the FRIA to answer its six questions.
- **Skipping Article 27(4).** Analysts who do not know about the reuse
  clause either over-duplicate (two full documents) or under-produce
  (one document that fails one of the regimes).
- **Confusing Art. 36 GDPR consultation with Art. 27(3) AI Act
  notification.** They go to different authorities and carry different
  triggers.
- **Omitting Art. 13 provider information from the FRIA.** The DPIA
  will not surface it, and 27(1)(d) explicitly requires it.
- **Losing track of groups not in the data.** The DPIA sees data
  subjects; the FRIA sees affected persons, which is a wider set.

## 6. References

- Regulation (EU) 2016/679 (GDPR), Articles 22 (automated individual
  decision-making), 35 (DPIA), and 36 (prior consultation).
  <https://eur-lex.europa.eu/eli/reg/2016/679/oj>
- Regulation (EU) 2024/1689 (EU AI Act), Articles 13, 26, 27, 71, and
  73. Article 27(4) is the key reuse clause.
  <https://eur-lex.europa.eu/eli/reg/2024/1689/oj>
- Charter of Fundamental Rights of the European Union — Articles 20
  and 21 as the primary non-discrimination anchors for the FRIA's
  extension over the DPIA's data-subject categories.
  <https://eur-lex.europa.eu/eli/treaty/char_2012/oj>
- Article 29 Working Party, *Guidelines on Data Protection Impact
  Assessment (DPIA)* (WP248 rev.01, adopted 4 October 2017; endorsed
  by the EDPB on 25 May 2018) — reference guidance on DPIA scope and
  the "likely to result in high risk" assessment.
  <https://ec.europa.eu/newsroom/article29/items/611236>
- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 —
  used for cross-walking impact categories between the two regimes.
  <https://www.nist.gov/itl/ai-risk-management-framework>
- OWASP Machine Learning Security Top 10 — security framing that the
  DPIA's risk section typically covers only at the data-processing
  level, which the FRIA extends to model-level risks.
  <https://owasp.org/www-project-machine-learning-security-top-10/>
