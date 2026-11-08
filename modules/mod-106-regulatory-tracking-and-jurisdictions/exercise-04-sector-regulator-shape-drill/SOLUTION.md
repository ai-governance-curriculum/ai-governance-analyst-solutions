# Exercise 04 — Sector Regulator Shape Drill — Reference Solution

## 1. Solution overview

"Sector regulator shape" is a working vocabulary for the question an
analyst has to answer on every new system: **which sector regulator
reaches this use case, and what shape do its duties take?** A credit-
decision model lives in one regulator's world; a medical-imaging
triage tool in a different one; an employment-screening tool in a
third. The system itself does not change — the duties attached to it
do.

A "shape" has five components:

1. **Jurisdictional and sectoral trigger** — who the regulator
   reaches.
2. **Primary duty posture** — pre-market authorisation, supervisory
   oversight, enforcement-only, or guidance-only.
3. **Key artefacts** — what the regulator expects to see.
4. **Incident / disclosure duties** — what the regulator expects to
   be told, and when.
5. **Enforcement mechanism** — what the regulator does when things
   go wrong.

This solution provides:

- A side-by-side shape table for six representative sector
  regulators that touch AI systems.
- A worked "which regulator(s) reach this" pass for four systems.
- Decision rationale, validation steps, and a grading rubric.

The drill names US regulators where US sector supervision is unusually
dense (banking, healthcare, employment, consumer protection). It is
not jurisdictionally exclusive — sector regulators in other
jurisdictions follow roughly the same shape vocabulary, with different
statutes.

## 2. Worked answer or implementation

### 2.1 Six regulator shapes

| Regulator | Trigger | Duty posture | Key artefacts | Incident / disclosure | Enforcement |
|---|---|---|---|---|---|
| **FDA / CDRH (medical devices)** | Software functions that meet the **device** definition under FFDCA §201(h) and are not excluded by the 21st Century Cures Act §3060 (21 U.S.C. §360j(o)). Software as a Medical Device (SaMD) is in scope. | **Pre-market.** Depending on class: 510(k) substantial-equivalence, De Novo, or PMA; for AI/ML-enabled devices, the Predetermined Change Control Plan (PCCP) pathway (FDA guidance, December 2024). | Pre-market submission (device description, indications, performance testing including real-world performance for AI/ML), labeling, QSR / 21 C.F.R. Part 820 quality system, PCCP for anticipated post-market modifications. | Medical Device Reporting (21 C.F.R. Part 803) within 30 days for events reasonably suggesting the device may have caused or contributed to a death or serious injury; 5-day report for events requiring remedial action. Field corrections and removals (21 C.F.R. Part 806). | Warning letters, 483 observations, injunctions, seizure, consent decrees, civil money penalties, product recalls. |
| **CFPB / Federal banking agencies + ECOA/FCRA** | Decisions in **credit** and **consumer financial services** (ECOA §701(a), 15 U.S.C. §1691(a)); credit reporting and adverse action (FCRA §615, 15 U.S.C. §1681m). Federal banking agencies supervise institutions; CFPB supervises larger banks and certain non-bank entities under 12 U.S.C. §5514. | **Supervisory + enforcement.** CFPB supervision cycles; UDAAP; §1031/1036 of the Consumer Financial Protection Act. CFPB has emphasised (Circular 2023-03, May 2023, and 2022-03, May 2022) that FCRA adverse-action notices must give specific and accurate reasons even when algorithmic models are used. | Model risk management evidence (if an insured depository institution — SR 11-7 flows through to AI); ECOA adverse-action notices with specific principal reasons; FCRA pre-adverse-action and adverse-action notices with accurate reason codes and source identification; UDAAP documentation of fair-dealing. | Suspicious-activity reporting under BSA where relevant; incident disclosure is driven by the supervisory relationship, not by a single statutory incident rule. | Consent orders, civil money penalties, restitution, injunctive relief. Private rights of action under ECOA and FCRA. |
| **EEOC (employment)** | Employment decisions under **Title VII** (42 U.S.C. §2000e), **ADA** (42 U.S.C. §12101 et seq.), **ADEA** (29 U.S.C. §621 et seq.). The Uniform Guidelines on Employee Selection Procedures (29 C.F.R. Part 1607) cover selection procedures used as the basis for an employment decision. | **Enforcement.** Charge-driven investigation and litigation. No pre-market approval. The May 2023 EEOC technical-assistance document on Title VII and AI is explicit that employers remain responsible for adverse-impact outcomes of vendor-supplied algorithms. | Validation studies under 29 C.F.R. §1607.5 (job-relatedness and business necessity); §1607.14 technical standards for criterion-related, content, or construct validity; records under §1607.4. | No pre-emptive duty to self-report; records must be kept and produced on charge. State and local regimes (NYC LL 144, Illinois) add disclosure duties that EEOC does not. | Conciliation, EEOC-filed litigation, parallel private Title VII suits. Monetary and injunctive relief. |
| **FTC (consumer protection)** | **Unfair or deceptive acts or practices** affecting commerce (FTC Act §5, 15 U.S.C. §45). Sector-specific rules: **COPPA** (children under 13), **GLBA Safeguards**, **Equal Credit Opportunity Act** overlap with CFPB on non-depository credit. | **Enforcement + rulemaking.** Consent orders with structural AI remedies ("algorithmic disgorgement"). Business guidance (April 2021 "Aiming for truth, fairness, and equity in your company's use of AI"; April 2023 statement on AI and competition). | Substantiation evidence for AI performance claims; data-handling disclosures; fairness-related substantiation where fairness is marketed. | Health Breach Notification Rule (16 C.F.R. Part 318) for health apps not covered by HIPAA. Reporting via consent-order compliance. | §5 consent orders with structural remedies including deletion of data and models; civil penalties for order violations (15 U.S.C. §45(l)); redress. |
| **OCC / FRB / FDIC — SR 11-7 model risk management** | Models used by federally regulated banks for business decisions. SR 11-7 (April 2011) was issued by the FRB; the OCC's OCC Bulletin 2011-12 is the parallel instrument; the FDIC adopted it in 2017 (FIL-22-2017). Supervisory expectation. | **Supervisory.** Not a rule; a supervisory framework. Independent model validation, effective-challenge, model inventory, and governance are expected of insured depository institutions. In 2021 the agencies issued the "Interagency Statement on the Use of Alternative Data in Credit Underwriting" flagging specific AI/ML concerns. | Model inventory; validation reports; monitoring; model change logs; effective-challenge evidence. | No dedicated incident report; findings surface in the supervisory cycle. Matters Requiring Attention (MRAs) and Matters Requiring Immediate Attention (MRIAs) are the mechanism. | Supervisory actions; consent orders; restrictions on activities until remediation. |
| **HHS / OCR — HIPAA** | Protected health information (PHI) processed by covered entities and business associates (45 C.F.R. Parts 160, 162, 164). | **Enforcement + guidance.** Privacy, Security, and Breach Notification Rules. The Section 1557 final rule (May 2024) addresses patient-care decision-support tools in covered health programmes receiving federal financial assistance. | Administrative, physical, and technical safeguards (45 C.F.R. §164.308–312); risk analyses; breach-notification evidence; business associate agreements; Section 1557 non-discrimination evidence for decision-support tools. | Breach notification under 45 C.F.R. §§164.400–414: individual notice without unreasonable delay and no later than 60 days; HHS notice (annual aggregated for <500 individuals; within 60 days for ≥500); media notice for 500+ in a state or jurisdiction. | HHS/OCR civil money penalties; corrective action plans; resolution agreements; criminal penalties where applicable. |

A seventh shape worth naming for completeness:

- **Sectoral EU supervisors.** Under the EU AI Act Art. 74, product-
  safety and sector regulators act as market-surveillance
  authorities for AI systems embedded in regulated products (e.g.
  medical devices under Regulation (EU) 2017/745). For financial-
  services AI, Art. 74(6) names the competent authorities under the
  relevant financial-services Union law.

### 2.2 Worked "which regulator(s) reach this" — four systems

Each system is run through the six shapes above. Multiple regulators
can reach one system; the drill names each with the duty that
attaches.

**`sys-0012-resume-ranker` — AI resume ranking for US and EU
positions.**
- EEOC: yes. Selection procedure under 29 C.F.R. §1607; employer
  responsible for adverse impact; validation study required if the
  four-fifths screen is triggered.
- CFPB / banking: no (not credit).
- FTC: yes if the vendor makes marketing claims about bias or
  performance; FTC Act §5 substantiation.
- FDA: no.
- SR 11-7: no (not a bank model for a bank credit decision).
- HIPAA: no.
- EU AI Act (cross-ref `mod-106` exercise-02): high-risk Annex III(4).
- Non-sector add-ons: NYC LL 144 (AEDT); Illinois HB 3773 (IHRA); and
  — if any applicant is Colorado-located — Colorado SB 24-205.

**`sys-0034-credit-score` — consumer credit decisioning for US (and
limited EU presence).**
- EEOC: no (credit, not employment) — but ECOA prohibits discrimination
  in credit, enforced via CFPB / HUD / DOJ.
- CFPB: yes. ECOA adverse-action notices with specific reasons; FCRA
  adverse-action notices; UDAAP exposure on the decline experience.
- Federal banking agencies (if the issuer is a bank): yes. SR 11-7
  model risk management; model inventory, validation, monitoring,
  effective-challenge.
- FTC: yes if the issuer is non-bank (FTC enforces ECOA for
  non-bank creditors).
- FDA / HIPAA / EEOC: no.
- EU AI Act: Annex III(5)(b) creditworthiness, high-risk.
- GDPR Art. 22: in scope (see `mod-106` exercise-03).

**`sys-0056-radiology-triage` — AI radiology triage tool deployed in
a US hospital network.**
- FDA: yes. SaMD under FFDCA §201(h); device classification and
  510(k) / De Novo / PMA pathway; PCCP guidance governs AI/ML
  modification lifecycle.
- HHS / OCR — HIPAA: yes. PHI; safeguards; breach notification; the
  Section 1557 final rule reaches patient-care decision-support.
- CFPB / SR 11-7: no.
- EEOC: no, unless used in clinician performance review (in which
  case Title VII flows back in).
- FTC: yes if the vendor makes marketing claims about clinical
  performance; §5 substantiation.
- EU AI Act (if EU deployed): high-risk under Annex III (medical
  product safety overlay with Regulation (EU) 2017/745 via Art. 74).

**`sys-0088-support-copilot` — generative customer-support assistant
used by human agents.**
- EEOC: no (not hiring).
- CFPB / SR 11-7: no.
- FDA: no.
- HHS / OCR — HIPAA: yes **if** PHI reaches the assistant;
  Business Associate Agreement required, safeguards, breach
  notification.
- FTC: yes. §5 substantiation for marketing claims; GLBA Safeguards
  if financial products are discussed; COPPA if under-13 users are
  in scope.
- EU AI Act: Art. 50 transparency.
- GDPR Art. 22: not triggered on these facts (see `mod-106`
  exercise-03 Case D).

### 2.3 Shape-to-artefact matrix

Which artefact a sector regulator wants to see — matched to the AI
governance programme's existing deliverables.

| Shape | Needs | Where we author it |
|---|---|---|
| **FDA pre-market** | Device description, indications, substantial-equivalence or clinical data, labeling, QSR, PCCP. | Clinical / regulatory affairs; AI governance supplies model card (`mod-105`), risk assessment (`mod-103`), and post-market monitoring plan (`mod-109`). |
| **CFPB / ECOA / FCRA** | Adverse-action notices; specific principal reasons; data sources and corrections. | Credit operations; AI governance supplies model card (reason-code schema) and fair-lending testing output (`mod-104`). |
| **EEOC Title VII** | Validation study; adverse-impact analysis; records. | HR / employment counsel; AI governance supplies bias-audit output (`mod-104`) and model card. |
| **FTC §5** | Substantiation of claims; fairness evidence where marketed. | Marketing / legal; AI governance supplies performance evidence and model card. |
| **SR 11-7** | Model inventory; validation; monitoring; effective challenge. | Model Risk Management; AI governance supplies risk register (`mod-103`), model card, and inventory entry (`mod-102`). |
| **HIPAA + 1557** | Safeguards; breach notification; non-discrimination evidence for decision-support tools. | Security / privacy; AI governance supplies impact assessment (`mod-105`) and incident runbook (`mod-109`). |

### 2.4 Decision rationale

- **Multiple regulators can reach one system.** The "which regulator"
  question is never one answer. A credit model at a bank is both
  CFPB-reachable and SR 11-7-reachable; a medical-imaging tool is
  both FDA-reachable and HIPAA-reachable.
- **Pre-market vs. supervisory vs. enforcement is the first axis.**
  An FDA pre-market pathway cannot be answered with a mitigation
  plan; it requires clearance. A CFPB supervisory posture cannot be
  answered with a one-time submission; it is ongoing. An EEOC
  enforcement posture is answered with evidence that holds up when
  a charge is filed.
- **Artefact reuse is the lever.** A model card (`mod-105`), a bias
  audit (`mod-104`), a risk assessment (`mod-103`), an inventory
  entry (`mod-102`), and a monitoring plan (`mod-109`) are shaped
  once and emit regulator-specific views.
- **Vendor-supplied does not shift responsibility.** EEOC, CFPB, and
  HHS all treat the deploying / issuing / covered entity as
  responsible. The vendor's documentation is evidence, not a defence.
- **Non-sector overlays stack.** State AI laws, NYC LL 144, Illinois
  HB 3773, Colorado SB 24-205, California SB 942, Utah Code §13-70
  stack on top of the sector shape — they do not substitute.

## 3. Validation steps

1. For each system under review, run through each of the six shapes
   and record a "yes/no, why" for every one. A row with no answer
   is incomplete.
2. For every "yes", name the specific artefact the regulator expects
   and the owner responsible for authoring it.
3. For every "yes", confirm the regulatory tracker (`mod-106`
   exercise-01) has a `regulation` row and at least one `obligation`
   row open, with an owner and a review date.
4. For FDA cases, confirm the SaMD / device determination is
   documented; a software function that does not meet the device
   definition and is excluded under FFDCA §520(o) (21 U.S.C.
   §360j(o)) should be documented as out-of-scope with the
   exclusion basis cited.
5. For CFPB / FCRA cases, confirm every adverse-action reason code
   can be traced to a feature path in the model and that reason
   codes are reviewed quarterly against the top 10 observed decline
   reasons.
6. For EEOC cases, confirm either a validation study exists or a
   routing memo flags its absence as a Title VII-exposure item.
7. For SR 11-7 cases, confirm the model inventory entry includes a
   validation report reference and a monitoring thresholds section.
8. For HIPAA cases, confirm the BAA is in place for every vendor
   that touches PHI and that the breach-notification runbook is
   current.
9. Confirm the drill has checked the **non-sector overlays** (state
   AI laws, GDPR, EU AI Act) separately, not folded into a sector
   shape.

## 4. Rubric or review checklist

Grade the learner's drill answer on five dimensions, 3 points each
(total 15). Pass = total ≥ 11 with no 0.

| Criterion | 3 — strong | 2 — acceptable | 0–1 — weak |
|---|---|---|---|
| **Trigger identification** | Correctly identifies the statutory trigger for each regulator that reaches the system (device definition for FDA; selection procedure for EEOC; consequential-credit for CFPB; PHI for HIPAA; model for SR 11-7; §5 for FTC). | Identifies most triggers. | Collapses triggers into "it's regulated". |
| **Duty posture** | Pre-market / supervisory / enforcement posture named per regulator with its implication for what the organisation owes. | Identifies posture but inconsistently. | Treats all regulators as "enforcement only". |
| **Artefact mapping** | Each "yes" carries a specific artefact and owner; artefact reuse across regulators is called out. | Artefacts named but owners vague. | Artefacts not named. |
| **Vendor responsibility** | Explicit that deployer / employer / covered entity is responsible; vendor evidence is evidence, not defence. | Mentioned once. | Blames the vendor and stops. |
| **Overlay check** | State AI laws, GDPR, and EU AI Act are separately named for every system where they apply. | One overlay missed. | Overlays folded into the sector shape or omitted. |

Document-level checks (binary, all must pass):

- [ ] Every system has every shape row answered (yes/no, why).
- [ ] FDA SaMD determinations cite the device-definition statute.
- [ ] CFPB / ECOA / FCRA rows name the adverse-action notice duty.
- [ ] HIPAA rows name the BAA and the breach-notification runbook.
- [ ] SR 11-7 rows reference the model inventory and validation.
- [ ] The regulatory tracker has an updated row per applicable
      regulator.

## 5. Common mistakes

- **Treating "AI regulation" as one shape.** The EU AI Act is one
  shape; sector regulators are different shapes. Collapsing them
  guarantees miss on one side.
- **Equating "vendor-supplied" with "vendor's problem".** EEOC, CFPB,
  HHS/OCR, and FTC all treat the deploying entity as responsible.
- **Assuming FDA pre-market goes away if the product is an "assist"
  tool.** The device definition is independent of whether the tool
  is advisory. FDA Clinical Decision Support guidance (September
  2022) distinguishes functions excluded under §520(o) from
  functions that remain devices; the determination is substantive.
- **Treating SR 11-7 as a rule.** It is a supervisory letter; it
  binds via the supervisory relationship. The expectation is as
  real as a rule, but the language is "expectation".
- **Forgetting Section 1557 for healthcare decision-support.** The
  May 2024 final rule brings patient-care decision-support into a
  non-discrimination framework.
- **Omitting the state overlays.** A perfect sector-shape answer
  that omits NYC LL 144 or Colorado SB 24-205 still fails the
  overall drill.
- **No artefact owner.** Sector shape without a named role to
  produce the artefact is a plan to not comply.
- **Assuming one regulator per system.** The drill expects the
  analyst to list every regulator that reaches the system — a
  medical-imaging triage tool is FDA **and** HIPAA (**and** EU
  under Art. 74 if deployed in the Union).

## 6. References

- Federal Food, Drug, and Cosmetic Act, §201(h) (21 U.S.C. §321(h)),
  §520(o) (21 U.S.C. §360j(o)); 21st Century Cures Act (Pub. L.
  114-255, 13 December 2016). <https://www.congress.gov/bill/114th-congress/house-bill/34>
- U.S. Food and Drug Administration, *Marketing Submission
  Recommendations for a Predetermined Change Control Plan for
  Artificial Intelligence/Machine Learning (AI/ML)-Enabled Device
  Software Functions*, final guidance, December 2024. <https://www.fda.gov/regulatory-information/search-fda-guidance-documents/marketing-submission-recommendations-predetermined-change-control-plan-artificial-intelligence>
- U.S. Food and Drug Administration, *Clinical Decision Support
  Software*, final guidance, 28 September 2022. <https://www.fda.gov/regulatory-information/search-fda-guidance-documents/clinical-decision-support-software>
- Equal Credit Opportunity Act, 15 U.S.C. §§1691 et seq.; Fair
  Credit Reporting Act, 15 U.S.C. §§1681 et seq. <https://www.ecfr.gov/current/title-12/chapter-X/part-1002>
- Consumer Financial Protection Bureau, *Circular 2023-03: Adverse
  action notification requirements and the proper use of the
  CFPB's sample forms*, 11 May 2023. <https://www.consumerfinance.gov/compliance/circulars/circular-2023-03/>
- Consumer Financial Protection Bureau, *Circular 2022-03: Adverse
  action notification requirements in connection with credit
  decisions based on complex algorithms*, 26 May 2022. <https://www.consumerfinance.gov/compliance/circulars/circular-2022-03/>
- U.S. Equal Employment Opportunity Commission, *Uniform Guidelines
  on Employee Selection Procedures*, 29 C.F.R. Part 1607. <https://www.ecfr.gov/current/title-29/subtitle-B/chapter-XIV/part-1607>
- U.S. EEOC, *Select Issues: Assessing Adverse Impact in Software,
  Algorithms, and Artificial Intelligence Used in Employment
  Selection Procedures Under Title VII*, May 2023. <https://www.eeoc.gov/select-issues-assessing-adverse-impact-software-algorithms-and-artificial-intelligence-used>
- Federal Trade Commission Act, 15 U.S.C. §45. <https://www.ftc.gov/legal-library/browse/statutes/federal-trade-commission-act>
- Federal Trade Commission, *Aiming for truth, fairness, and
  equity in your company's use of AI*, business blog, 19 April 2021. <https://www.ftc.gov/business-guidance/blog/2021/04/aiming-truth-fairness-equity-your-companys-use-ai>
- Board of Governors of the Federal Reserve System, *SR 11-7:
  Guidance on Model Risk Management*, 4 April 2011. <http://web.archive.org/web/20260414150921/https://www.federalreserve.gov/supervisionreg/srletters/sr1107.htm>
- Office of the Comptroller of the Currency, *Bulletin 2011-12:
  Sound Practices for Model Risk Management*. <http://web.archive.org/web/20260416232808/https://www.occ.gov/news-issuances/bulletins/2011/bulletin-2011-12.html>
- Federal Deposit Insurance Corporation, *FIL-22-2017: Adoption of
  Supervisory Guidance on Model Risk Management*. <https://www.fdic.gov/news/financial-institution-letters/2017/fil17022.html>
- HIPAA Privacy, Security, and Breach Notification Rules, 45 C.F.R.
  Parts 160, 162, 164. <https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C>
- HHS Office for Civil Rights, *Nondiscrimination in Health
  Programs and Activities* (Section 1557 final rule), 89 Fed. Reg.
  37522, 6 May 2024. <https://www.federalregister.gov/documents/2024/05/06/2024-08711/nondiscrimination-in-health-programs-and-activities>
- Regulation (EU) 2024/1689 — EU AI Act, Art. 74 on market-
  surveillance authorities and sector regulators. <https://eur-lex.europa.eu/eli/reg/2024/1689/oj>
- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023. <https://www.nist.gov/itl/ai-risk-management-framework>
- OWASP Machine Learning Security Top 10 — referenced for sector
  incident reporting that uses ML-security vocabulary. <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE ATLAS — referenced for adversary-technique vocabulary in
  sector incident reports. <https://atlas.mitre.org/>
