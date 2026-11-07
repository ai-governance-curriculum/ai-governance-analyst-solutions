# Exercise 03 — Acceptable-Use Policy First Draft — Reference Solution

## 1. Solution overview

An *AI acceptable-use policy* (AUP) is the staff-facing document that
sets the rules for how people inside the organisation may use AI
systems — both the ones the organisation deploys and the external
services (chat, image, code) that staff might otherwise reach for
unilaterally. It is a policy document, not a technical control; the
controls live elsewhere (identity, DLP, approved-tools list). Its job
is to make the rules legible to a non-technical reader and to give
Legal, HR, and Security a single reference to point to when a concrete
situation lands on a Monday morning.

NIST AI RMF 1.0 **GOVERN 1.1** asks for policies and procedures aligned
to the organisation's risk tolerance; **GOVERN 1.2** asks for the
accountability structures that make those policies stick; **GOVERN 6.1**
asks for third-party risk to be managed. ISO/IEC 42001:2023 Annex A
controls (particularly A.5 on policies) are the control counterpart. On
the regulatory side, EU AI Act Art. 4 ("AI literacy") assumes an
underlying policy baseline; GDPR Art. 5(1)(f) (integrity and
confidentiality) is the lens through which data-handling clauses are
read in European jurisdictions. OWASP Machine Learning Security Top 10
and MITRE ATLAS inform the prohibited-uses section where adversarial
or security-sensitive uses are called out.

This solution provides:

- A **first-draft AI acceptable-use policy** in nine clauses.
- A short **definitions block**.
- A **tiered-use table** (allowed / conditional / prohibited) with
  concrete examples.
- A **disclosure and attribution** standard for AI-assisted output.
- An **incident reporting** route tied to `mod-109`.
- Decision rationale and a grading rubric.

Scope: this is a design exercise. The draft is a *first draft* by
design — it is the version an analyst produces for Legal, HR, and
Security to red-team. It is explicit about what the analyst decided
and what the analyst left to counsel.

## 2. Worked answer or implementation

### 2.1 Reference organisation (local exercise context)

> **Organisation**: a mid-sized multi-jurisdictional employer
> (fictional) with staff in the US (including NYC and California),
> the EU, and the UK. Regulated activities include employment
> decisions, customer communications, and financial customer
> service. The organisation already operates a general technology
> acceptable-use policy; this document is the AI-specific
> supplement to it.

### 2.2 The policy — first draft

```markdown
# AI Acceptable-Use Policy (AI-AUP) — First Draft

**Owner**: Head of AI Governance.
**Approver**: Executive Sponsor on the recommendation of the
AI Governance Council.
**Legal reviewer**: Deputy General Counsel – Technology.
**Effective**: [to be set on approval].
**Review cadence**: at least annually and on any material change
to an in-force regulation (e.g. new EU AI Act secondary act, new
US-state AI law affecting our employee or customer footprint).

## 1. Purpose

This policy sets the rules for using artificial intelligence
systems at [Organisation]. It supplements, and does not replace,
the general Technology Acceptable-Use Policy, the Data Protection
Policy, the Information Security Policy, and the Code of Conduct.
In case of conflict, the stricter rule applies.

## 2. Scope

This policy applies to:

- all staff (employees, contractors, consultants, interns)
  acting in a work capacity;
- all AI systems used in or for [Organisation], whether
  operated by [Organisation] ("deployer AI"), procured as a
  service ("vendor AI"), or used informally through a personal
  account ("shadow AI");
- staff use on both corporate-owned devices and personal
  devices accessing [Organisation] data or systems.

Customer-facing use of AI by [Organisation] is additionally
governed by the Customer AI Transparency Standard and the
product-specific terms of service; this policy applies to staff
behaviour supporting those products.

## 3. Definitions

- **AI system**: a machine-based system that infers, from inputs
  it receives, outputs such as predictions, content,
  recommendations, or decisions that influence environments
  (the working definition is aligned with the EU AI Act Art. 3(1)
  and NIST AI RMF 1.0's framing).
- **Approved AI tool**: an AI system listed in the Approved AI
  Tools Register maintained by the AI Governance Analyst, for
  which a DPIA / FRIA / AIA (see `mod-105`) has been completed
  and a risk acceptance is in place.
- **Confidential information**: as defined in the Information
  Classification Standard; includes trade secrets,
  pre-public financial information, personal data, protected
  health information, and attorney-client-privileged material.
- **Personal data**: has the meaning given in GDPR Art. 4(1)
  for EU/UK contexts and the applicable US state-law meanings
  (e.g. CCPA/CPRA for California; CDPA for Virginia).
- **High-risk use case**: either (a) a use case within an EU AI
  Act Annex III category, or (b) a use case listed in the
  "prohibited" or "conditional" rows of §6 regardless of
  jurisdiction.
- **Generative AI**: an AI system that produces new content
  (text, image, code, audio, video) in response to inputs.
- **Disclosure of AI assistance**: the act of marking or stating
  that an AI system produced or materially contributed to
  content, in a way a reasonable reader of that content can
  see.

## 4. General principles

Each staff member using AI at work is responsible for:

1. **Fit for purpose.** Using AI only for tasks for which the
   tool was listed as approved on the Approved AI Tools
   Register.
2. **Verification.** Reviewing AI output before it leaves your
   hands — before it is sent, published, acted on, or committed.
3. **Attribution and disclosure.** Disclosing AI assistance on
   outputs that reach another person (see §7).
4. **Data care.** Not entering data into an AI system beyond the
   classification level the tool is approved for (see §5).
5. **Human decision on consequential outcomes.** Not using AI
   output as the sole basis for a decision that has legal,
   financial, safety, employment, or similarly significant
   effect on a person (see §6 and GDPR Art. 22 for the external
   anchor).
6. **Report and don't hide.** Reporting suspected AI-related
   incidents (see §8) rather than deleting or working around
   them.

## 5. Data handling and classification

| Data class            | Use with AI                                                                 |
| :-------------------- | :-------------------------------------------------------------------------- |
| Public                | Any approved tool, no restriction.                                          |
| Internal              | Approved tools only; no personal consumer accounts.                         |
| Confidential          | Approved tools whose Register entry marks them "Confidential OK" only.      |
| Restricted (incl. personal data, PHI, source code with secrets, financial pre-public, privileged) | Approved tools whose Register entry marks them "Restricted OK" and whose DPA permits it; otherwise prohibited. |

Prohibitions regardless of tool:

- **No secrets in prompts.** Credentials, API keys, access
  tokens, or keys of any kind must not be entered into any AI
  system, including approved ones. Treat a secret in a prompt
  as a credential exposure (see Incident Response Policy).
- **No training-data submission without a DPA clause.** Content
  submitted to a vendor AI may not be used by the vendor for
  model training unless the DPA explicitly permits it. Where in
  doubt, assume no.
- **No personal consumer accounts for work.** Use the
  organisation-licensed account; do not sign in with a personal
  account to process work data.

## 6. Tiered use — allowed, conditional, prohibited

The table lists example uses. If a use case is not listed,
apply the General Principles and consult the AI Governance
Analyst. When in doubt, treat it as **conditional**.

| Tier         | Examples                                                                                                                                                                                                                                                                                             | Action required                                                    |
| :----------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------- |
| **Allowed**  | Drafting internal emails or meeting notes from your own notes; summarising a public document; brainstorming; translating public text; explaining a concept; code completion on non-sensitive code using an approved tool.                                                                           | Follow General Principles; disclose per §7.                       |
| **Conditional** | Drafting an external communication that quotes or paraphrases AI output; summarising a confidential document; generating code that will reach production; preparing a hiring-related artefact (job description, outreach message); analysing a customer complaint or a candidate submission.    | Human review before send; disclosure per §7; log use where tooling supports it. |
| **Prohibited** | Making a decision about an identifiable person (hire, reject, promote, demote, terminate, grant/deny credit, grant/deny access to a service) without substantive human review; generating content that impersonates a real person without their consent; producing deepfakes; generating code intended to evade security controls, exfiltrate data, or facilitate unauthorised access (MITRE ATLAS / OWASP ML Top 10 territory); using AI to monitor individual staff productivity beyond what is disclosed in the Employee Handbook; attempting to jailbreak an approved tool to produce prohibited output; using AI to produce legal advice, medical advice, or financial advice held out as authoritative to a third party. | Not permitted. If the business need is real, open a conditional-use review with the AI Governance Analyst. |

Region-specific additions:

- **NYC**: any use that falls within NYC Local Law 144's AEDT
  definition must route through the Approved AI Tools Register
  entry for that tool, which captures the LL 144 bias audit and
  candidate notice obligations.
- **EU/UK**: any use that would make a "solely automated"
  decision with legal or similarly significant effects within
  the meaning of GDPR Art. 22 is prohibited unless an
  Art. 22(2) exception applies and has been confirmed by Legal.
- **California**: any use that collects or processes "sensitive
  personal information" as defined in CPRA requires a Register
  entry that permits that class; see the Privacy Policy.

## 7. Disclosure and attribution

- **Internal content** (emails, memos, notes): disclose AI
  assistance where a reader could reasonably care — any content
  that will be forwarded, cited, or acted on by another person
  beyond the immediate recipient. A one-line note at the top
  ("Drafted with assistance from [tool]; reviewed by [role]")
  meets the bar.
- **External content** (customer-facing, candidate-facing,
  regulator-facing, published): disclose AI assistance per the
  Customer AI Transparency Standard. Do not represent AI output
  as human-authored where authorship is material to the reader.
- **Code**: AI-generated code that reaches production must be
  reviewed by a human engineer on the PR. The commit log must
  show the human reviewer, even if the author field reflects
  the tool-assisted contributor. No "AI did it" merges.
- **Model outputs cited in an analytical document**: cite the
  model name, version, date of query, and the key input
  parameters. Treat the model as a source, not an oracle.

## 8. Incident reporting

Report an AI-related incident immediately — within the same
business day — using the standard Incident Reporting process
(see Incident Response Policy and `mod-109`). Examples:

- Confidential or personal data entered into a non-approved
  tool, or into a tool at a higher classification level than
  it is approved for.
- A tool producing an output that discriminates, defames, or
  reasonably appears to violate a regulation or contract.
- A security-adjacent output: jailbreak success, model
  producing exploitable code, prompt injection by an external
  party, or data extraction (OWASP ML Top 10 / MITRE ATLAS
  patterns).
- An external complaint about the organisation's use of AI.
- Any suspected serious incident under Art. 3(49) of the EU
  AI Act — where applicable to a high-risk system we provide
  or deploy.

Reporting a potential incident is encouraged; retaliating
against a reporter is a separate breach of the Code of Conduct.

## 9. Training and acknowledgement

Every staff member with AI tool access completes the AI Literacy
training (aligned with EU AI Act Art. 4) within 30 days of
onboarding and annually thereafter. Completion is tracked by
People Operations. Managers may not approve AI-tool access
until training is complete for the requester.

## 10. Governance, enforcement, and exceptions

- **Governance**. The AI Governance Council is accountable for
  this policy; the Head of AI Governance owns day-to-day
  interpretation.
- **Enforcement**. Violations are handled under the existing
  disciplinary process. Security incidents follow the Incident
  Response Policy. Where an external obligation (regulator
  notice, candidate notice) is triggered, Legal makes that
  call.
- **Exceptions**. A business owner may request a time-bound
  exception from the AI Governance Analyst. Exceptions are
  decisions, tracked with sunset dates in the decision register
  (see `mod-110` exercise-02 and `mod-103` exercise-05).
- **Review cadence**. This policy is reviewed at least annually
  and on any material regulatory change. The next scheduled
  review date is set in the Approved AI Tools Register alongside
  the policy record.

## 11. Related documents

- Technology Acceptable-Use Policy.
- Data Protection Policy and Privacy Notices.
- Information Security Policy and Incident Response Policy.
- Information Classification Standard.
- Customer AI Transparency Standard.
- Approved AI Tools Register (operational, see `mod-102`).
- Risk Register (see `mod-103`).
- Regulatory Tracker (see `mod-106`).
- Controls Catalogue (see `mod-108`).
```

### 2.3 Items the analyst deliberately left to Legal

A first draft is explicit about what has been decided and what has
been *flagged* for counsel. The ones below should appear as a
cover-memo list when the first draft moves into legal review:

- **Jurisdictional carve-outs.** The region-specific additions in
  §6 are the ones we know we need; Legal to confirm whether any
  US-state AI law (e.g. Colorado Consumer Protections for Artificial
  Intelligence; Illinois AI Video Interview Act) requires its own
  clause in our footprint.
- **Works-council / employee-representative consultation.** Where
  we have EU establishments, local employment law may require
  consultation before publishing a policy that affects staff
  monitoring. Legal to confirm the list of countries and the
  cadence.
- **Collective agreements.** Where staff are unionised, the policy
  may need to be notified or negotiated before it takes effect.
- **Confidential-reporting / whistle-blower overlap.** The
  incident-reporting clause interacts with existing whistle-
  blower protections; Legal to confirm the correct routing for
  reports that allege misuse by a named manager.
- **Public-sector contract language.** Where we hold public-sector
  contracts, flow-down obligations (e.g. OMB M-24-10 practice-
  equivalent in the US, EU AI Act public-sector obligations) may
  require additional clauses.
- **Cross-border data-transfer mechanics** are governed by the
  Data Protection Policy; this AUP should not duplicate them.

### 2.4 Decision rationale

- **"First draft" is the correct product.** An AUP that lands
  fully-formed from the analyst bypasses the people who must own
  it after it is published. The draft's job is to focus Legal,
  HR, and Security on real choices, not to litigate every edge.
- **Scope covers "shadow AI" explicitly.** Unapproved personal-
  account use is the common failure mode. A policy that regulates
  only the approved tools misses the actual risk surface.
- **Data classification is the hinge.** Most incidents trace to a
  data class → tool-approval mismatch. Keying §5 to the existing
  classification standard reuses an owner rather than inventing one.
- **Tiered-use table by example.** Policies without concrete
  examples do not change behaviour. Each row is an example a
  staff member can recognise.
- **Prohibition list includes the adversarial uses.** OWASP ML
  Top 10 (ML03 — Model Inversion; ML05 — Model Theft; ML08 —
  Model Skewing) and MITRE ATLAS tactics inform what a policy
  must put out of bounds even if no one has asked yet.
- **Disclosure defaults to transparency.** NIST AI RMF 1.0
  GOVERN 5.1 and MEASURE 2.8 (transparency and explainability)
  frame the default toward disclosing AI assistance where it
  matters to a reader.
- **GDPR Art. 22 is the external anchor for consequential
  decisions.** The clause does not cite Art. 22 as a magic word;
  it uses the shape of the rule (no solely-automated
  consequential decisions without an exception) to set the
  internal rule.
- **No duplication with the Data Protection Policy.** The AUP
  points to the DPP for cross-border transfer and lawful basis;
  owning two copies of those rules invites drift.
- **Exceptions are decisions in the decision register.** This
  closes the loop with `mod-110` exercise-02 and `mod-103`
  exercise-05 — any exception is recorded, owned, and sunset.
- **Training linked to EU AI Act Art. 4.** Art. 4 (AI literacy)
  binds providers and deployers of AI systems to ensure staff
  have an appropriate level of literacy. A policy that does not
  back into training is incomplete.
- **Incident reporting routes through `mod-109`.** We already
  have an incident-response policy and a monitoring surface —
  the AUP points to them rather than inventing a parallel
  process.
- **Role-named owners.** The policy names the Head of AI
  Governance, the AI Governance Council, the Deputy GC –
  Technology — not individuals.

## 3. Validation steps

1. **Classification mapping.** Confirm the data classes in §5
   match the organisation's Information Classification Standard
   verbatim. A mismatch here creates ambiguity on every row.
2. **Approved Tools Register.** Confirm the Register exists in a
   location staff can reach and that every tool listed carries a
   DPIA / FRIA / AIA link (`mod-105`) and a risk acceptance
   (`mod-103`).
3. **Legal review.** The six items in §2.3 of this solution are
   submitted to Legal with the draft and resolved before
   approval.
4. **HR review.** HR confirms the monitoring, training, and
   enforcement clauses align with the Employee Handbook and
   relevant collective agreements.
5. **Security review.** Information Security confirms the
   prohibited uses align with the Security Policy, that the
   incident route works, and that DLP coverage reflects the
   data-class → tool mapping.
6. **Works-council / consultation.** Where required, complete
   consultation before the effective date.
7. **Training rollout.** AI Literacy training is live and
   trackable before the AUP takes effect.
8. **Register of exceptions.** Confirm the decision register
   (see `mod-110` exercise-02) has an exception category
   configured and that exceptions have a default sunset.
9. **Annual review date in the regulatory tracker.** Ensure
   the AUP's next-review date is set and tracked in the
   regulatory tracker (see `mod-106`) so a regulatory change
   automatically prompts a review.

## 4. Rubric or review checklist

Grade the learner's submission on six dimensions, 3 points each
(total 18). Pass = total ≥ 13 with no 0.

| Criterion | 3 — strong | 2 — acceptable | 0–1 — weak |
|---|---|---|---|
| **Scope** | Covers staff, approved tools, vendor AI, shadow AI, personal devices. | Covers approved tools and shadow AI; missing personal-device call-out. | Only covers approved internal tools. |
| **Data handling** | Data-class table keyed to the organisation's classification standard; explicit prohibitions on secrets and training-data submission. | Data-class table present; secrets rule missing. | Vague "handle data carefully" wording. |
| **Tiered-use examples** | Allowed / conditional / prohibited each have concrete examples including adversarial uses; region-specific additions present. | Three tiers present with examples; no region-specific additions. | No tiers; policy as a general statement. |
| **Disclosure and verification** | Verification and disclosure obligations for internal, external, code, and cited model output; code-review requirement explicit. | Disclosure obligation present but code-review requirement missing. | Disclosure handled as "where appropriate." |
| **Governance and enforcement** | Owner, approver, Council, review cadence, exception process, decision-register link all present. | Owner and review cadence present; exception process informal. | No owner; no exception handling. |
| **External anchors** | Cites NIST AI RMF, ISO/IEC 42001 Annex A, EU AI Act Art. 4, GDPR Art. 22, OWASP ML Top 10, MITRE ATLAS appropriately. | Cites two or three of the above. | No external anchors; policy is standalone. |

Document-level checks (binary, all must pass):

- [ ] Policy states its owner role, not a team or a person.
- [ ] Policy names an approver and a Legal reviewer.
- [ ] Review cadence is annual-or-on-material-change and ties
      into `mod-106`.
- [ ] Prohibitions list explicitly covers adversarial uses
      (impersonation, deepfakes, jailbreaks, evasion-oriented
      code generation).
- [ ] Incident reporting points at the existing incident policy
      rather than inventing a parallel route.
- [ ] Exceptions flow into the decision register with a sunset.
- [ ] Training clause is linked to AI literacy (EU AI Act Art. 4).
- [ ] The policy does not promise regulator outcomes or legal
      conclusions.

## 5. Common mistakes

- **Writing a fully-formed policy without flagging the legal
  carve-outs.** A first draft that reads as final leaves Legal
  nowhere to engage and slows the final sign-off.
- **Owning rules that belong in the Data Protection Policy.**
  Cross-border transfer, lawful basis, and data-subject rights
  belong in the DPP. The AUP points to it.
- **Omitting shadow AI.** The policy that covers only approved
  tools ignores the actual risk surface and sends the message
  that personal-account use is acceptable.
- **Prohibitions without adversarial-use coverage.** Impersonation,
  deepfakes, prompt-injection attempts against our own tools,
  jailbreak attempts, and code generation intended to evade
  controls are easy to overlook and hard to add later.
- **No exception process.** Staff will find workarounds if there
  is no path for "I need to do this one time". An exception
  process with a sunset is how the policy stays respected.
- **Vague disclosure rule.** "Disclose where appropriate" is a
  non-rule. Spell out the four surfaces (internal, external,
  code, cited model output) and the default behaviour on each.
- **No link to training.** A policy that assumes staff already
  know what AI is or how it fails fails on contact with reality.
- **Enforcement by HR only.** Security incidents, regulatory
  obligations, and contractual breaches each have their own
  escalation paths; the AUP routes to all three.
- **Owner is "the Committee" or "the Team".** See `mod-103`
  exercise-05 — name the role.
- **Promising regulator outcomes.** A policy that says "this
  complies with the EU AI Act" is overclaiming. Say the policy
  is designed to support the organisation's compliance with the
  relevant obligations.

## 6. References

- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January
  2023 — in particular `GOVERN 1.1`, `GOVERN 1.2`, `GOVERN 5.1`,
  `GOVERN 6.1`, `MEASURE 2.8`, `MANAGE 1.3`.
  <https://www.nist.gov/itl/ai-risk-management-framework>
- ISO/IEC 42001:2023 — *Information technology — Artificial
  intelligence — Management system*, Annex A (controls) and
  Clause 5 (leadership), Clause 7 (support).
  <https://www.iso.org/standard/81230.html>
- Regulation (EU) 2024/1689 (EU AI Act), Art. 3(1) (definition
  of AI system), Art. 4 (AI literacy), Art. 72 (post-market
  monitoring), Annex III (high-risk categories).
  <https://eur-lex.europa.eu/eli/reg/2024/1689/oj>
- Regulation (EU) 2016/679 (GDPR), Art. 5(1)(f) (integrity and
  confidentiality), Art. 22 (automated individual decision-
  making).
  <https://eur-lex.europa.eu/eli/reg/2016/679/oj>
- OWASP Machine Learning Security Top 10 — adversarial and
  security-sensitive use categories for the prohibited-uses
  list.
  <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE ATLAS — adversary-technique framing for the
  prohibited-uses list.
  <https://atlas.mitre.org/>
- NYC Local Law 144 of 2021 — referenced only where NYC AEDT
  use needs a hook into this policy.
  <https://legistar.council.nyc.gov/LegislationDetail.aspx?ID=4344524&GUID=B051915D-A9AC-451E-81F8-6596032FA3F9>
- Local exercise context: `mod-102` (approved tools / intake);
  `mod-103` (risk register and exception handling); `mod-105`
  (DPIA / FRIA / AIA artefacts); `mod-106` (regulatory
  tracker); `mod-108` (controls); `mod-109` (incident response
  and monitoring); `mod-110` exercise-02 (decision register).
