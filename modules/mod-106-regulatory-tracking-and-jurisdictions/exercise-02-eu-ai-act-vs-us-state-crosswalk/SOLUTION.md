# Exercise 02 — EU AI Act vs. US State Crosswalk — Reference Solution

## 1. Solution overview

A multinational deployer that touches **both the EU and any number of
US states** cannot pick one regime and let the other fall away. The
EU AI Act (Regulation (EU) 2024/1689) is an omnibus, risk-based law
with extraterritorial reach. The US does not (yet) have a federal
equivalent; instead, a growing patchwork of **state-level** laws
imposes specific duties on narrower slices — hiring tools in New York
City, consequential decisions in Colorado, video-interview analysis in
Illinois, generative-AI disclosures in California and Utah, and so on.

A crosswalk is useful when it answers three questions for a single
system under review:

1. **Does each regime apply?** (jurisdictional nexus)
2. **Where do duties overlap?** — one artefact can satisfy multiple
   obligations if the shapes align.
3. **Where do they diverge?** — separate work streams are required.

This solution provides:

- A dimension-by-dimension crosswalk between the EU AI Act and five
  representative US state regimes.
- A worked applicability pass for one AI-enabled hiring assistant and
  one generative-AI customer-support system.
- Decision rationale and a grading rubric.

The crosswalk is a regulatory map. It is not legal advice; interpretive
calls belong to Legal. Statutes cited below are as of 5 November 2026
and must be re-verified on the primary-source URL before use — AI-law
state activity moves quickly.

## 2. Worked answer or implementation

### 2.1 Regimes compared

| Regime | Citation | Status (as of 2026-11-05) |
|---|---|---|
| **EU AI Act** | Regulation (EU) 2024/1689 | In force 2024-08-01; obligations phase in through 2027-08-02. |
| **Colorado AI Act** | Colorado Senate Bill 24-205 (C.R.S. §§6-1-1701 et seq.) | Signed 2024-05-17; effective 2026-02-01 (re-verify against the Colorado Secretary of State publication before citing — the 2025 special session produced further amendments). |
| **NYC LL 144 (AEDT)** | NYC Admin. Code §§20-870 to 20-874 | Enforcement began 2023-07-05. |
| **Illinois AIVIA + HB 3773** | 820 ILCS 42 (AI Video Interview Act); 775 ILCS 5 as amended by HB 3773 (Illinois Human Rights Act) | AIVIA effective 2020-01-01; HB 3773 effective 2026-01-01. |
| **California SB 942 ("California AI Transparency Act")** | Cal. Bus. & Prof. Code §22757 et seq. | Effective 2026-01-01. |
| **Utah AI Policy Act** | Utah Code §13-2-12 and §13-70 (AI Learning Laboratory) | Effective 2024-05-01. |

Other state laws exist (Texas TRAIGA; Tennessee ELVIS Act on name/voice/
likeness; several biometric-privacy statutes). They are flagged where
relevant but not fully crosswalked here.

### 2.2 Dimension-by-dimension crosswalk

| Dimension | EU AI Act (2024/1689) | Colorado AI Act (SB 24-205) | NYC LL 144 | Illinois (AIVIA + HB 3773) | California SB 942 | Utah AI Policy Act |
|---|---|---|---|---|---|---|
| **Instrument shape** | Omnibus, risk-based; different duties for prohibited, high-risk, and GPAI. | Consequential-decision focus; developer / deployer duties. | Narrow, prescriptive duty on employers using an AEDT in NYC. | AIVIA covers video-interview analysis; HB 3773 extends IHRA to algorithmic hiring. | Generative-AI provenance disclosure. | Disclosure duty for generative AI in regulated-occupation interactions. |
| **Primary bearer** | Provider, deployer, importer, distributor, authorised representative (Art. 2). | Developer and deployer of a "high-risk artificial intelligence system" (SB 24-205 §6-1-1702). | Employer / employment agency (NYC Admin. Code §20-870). | Employer using AI video-interview analysis (820 ILCS 42) or algorithmic hiring tools (775 ILCS 5 via HB 3773). | Covered provider of a generative-AI system (Bus. & Prof. Code §22757). | Supplier interacting with a consumer using generative AI in a regulated occupation. |
| **Jurisdictional nexus** | Placed on EU market **or** output used in the Union by deployers (Art. 2(1)(c)) — extraterritorial. | Applies to developers and deployers "doing business in" Colorado when the system makes consequential decisions concerning Colorado consumers. | Positions located in NYC. | Illinois employers; AIVIA applies when a video interview is used for an Illinois position. | Covered providers "doing business in California" (threshold conditions apply; re-verify the current user / revenue thresholds under SB 942 §22757(a) against the enrolled bill before citing). | Interactions with Utah consumers in regulated occupations. |
| **Core duty** | Prohibitions (Art. 5); risk-management system and QMS for high-risk (Arts. 9, 17); technical documentation (Art. 11); logging (Art. 12); transparency (Arts. 13, 50); human oversight (Art. 14); accuracy, robustness, cybersecurity (Art. 15); conformity assessment (Arts. 43, 47). | Developer: duty of reasonable care to protect against algorithmic discrimination; provide documentation and impact-assessment inputs. Deployer: duty of reasonable care; impact assessment on each high-risk AI system; pre-decision notice and post-decision explanation to consumer; right to correct / appeal. | Independent bias audit within the prior 12 months; public summary; candidate notice at least 10 business days before AEDT use; alternative-process option. | AIVIA: notice to applicant; consent; destruction of videos on request. HB 3773: covers algorithmic hiring tools under the IHRA's unlawful-discrimination framework. | Latent disclosure (invisible watermark) and manifest disclosure (user-facing tool to check if content is AI-generated). | Clear disclosure that a user is interacting with generative AI when asked; in regulated occupations, upfront disclosure. |
| **Risk tiering** | Explicit: prohibited (Art. 5), high-risk (Art. 6 + Annexes I and III), limited-risk / transparency (Art. 50), minimal-risk, and GPAI (Chapter V). | Only "high-risk AI system" (consequential-decision test) is defined; others are out of scope. | Narrow: any AEDT used for the covered employment purpose. | AIVIA: in-scope if video interview analysis is used. HB 3773: in-scope if algorithmic tool is used for employment decisions. | Narrow: generative AI producing covered content. | Narrow: generative-AI interactions with consumers in regulated occupations. |
| **Mandatory assessment** | Fundamental-rights impact assessment (FRIA) for certain deployers of high-risk systems (Art. 27). Risk-management system (Art. 9). | Impact assessment required for each deployment of a high-risk AI system; must be reviewed annually and after intentional and substantial modification. | Independent bias audit by a qualifying auditor; selection and scoring rates and impact ratios across EEO-1 and intersectional categories (6 RCNY §5-301). | AIVIA: no impact assessment mandated. HB 3773: evidence sufficient to address disparate-impact inquiries. | No impact assessment; provenance obligations only. | No impact assessment; disclosure only. |
| **Transparency to affected individuals** | Art. 26 duties on deployers of high-risk; Art. 50 duties on providers/deployers of certain limited-risk systems (chatbots, deepfakes, emotion recognition, biometric categorisation). | Pre-decision notice (that a high-risk AI system will be used); post-decision statement of principal reason(s) and data sources on an adverse decision; appeal and correction rights. | Written notice at least 10 business days before AEDT use; qualifications/characteristics used; data sources and retention on request. | AIVIA: consent and explanation that AI will be used; destruction of videos on request. | Disclosure tool allowing users to check if content is AI-generated; latent disclosure on content. | Mandatory disclosure that the counterparty is interacting with generative AI. |
| **Human oversight** | Mandatory for high-risk systems — natural persons must be able to understand capacities and limits and intervene (Art. 14). | Deployer must have procedures for human review in the appeal process. | Not prescribed; employer may use alternative selection process. | AIVIA: not prescribed. HB 3773: existing anti-discrimination framework implies human-decision checkpoints. | Not prescribed. | Not prescribed (disclosure-focused). |
| **Incident reporting** | Serious-incident reporting to the market-surveillance authority (Art. 73). GPAI providers: systemic-risk serious-incident reporting (Art. 55). | Attorney General notification within 90 days of discovering algorithmic discrimination from a deployed system (threshold conditions apply; re-verify the discovery-to-notice window against the enrolled 2025 amendments before citing). | Not prescribed in LL 144 itself. | HB 3773 incident posture flows through IHRA complaints and EEOC equivalents. | Not prescribed in SB 942. | Not prescribed. |
| **Enforcement** | Market-surveillance authorities; penalties up to 7% of worldwide annual turnover or €35 million (whichever is higher) for Art. 5 violations; up to 3% / €15 million for most other high-risk violations; up to 1% / €7.5 million for supplying incorrect information (Art. 99). | Attorney General enforcement; rulemaking authority. | NYC DCWP civil penalties; each day of non-compliance and each unlawful AEDT use is a separate violation (§20-872). | Illinois Attorney General (AIVIA); Illinois Department of Human Rights and HRC (IHRA under HB 3773). | California Attorney General; private right of action for certain claims (re-verify the private-action scope against the enrolled SB 942 text before citing). | Utah Division of Consumer Protection; civil penalties. |
| **Safe-harbor-style rebuttals** | Conformity with harmonised standards (Art. 40) creates a presumption of conformity. | Rebuttable presumption that a developer / deployer has used reasonable care if they comply with the risk-management programme requirements of the Act. | None. A published audit does not pre-empt Title VII (see `mod-104` exercise-04). | None. | None. | Participation in Utah's "AI Learning Laboratory" with a mitigation agreement can reduce penalties (Utah Code §13-70). |
| **Recordkeeping horizon** | Logs retained by provider / deployer for a period appropriate to the intended purpose, at least 6 months unless law prescribes longer (Art. 12(4), Art. 19 keeping for providers, Art. 26(6) for deployers). | Impact assessments retained at least 3 years after the final deployment of the high-risk system. | Audit evidence retained in support of the annual audit; summary posted on the employer's website. | AIVIA: video retention and destruction on request. | Provenance disclosure duty is on-content and ongoing. | Disclosure must be preserved for the duration of the interaction; investigation record. |

### 2.3 Where duties overlap (one artefact can satisfy multiple)

- **Impact assessment artefacts.** The EU AI Act Art. 27 FRIA, the
  Colorado Act's annual impact assessment, and the organisation's NIST
  AI RMF `MAP` output can be built from the same evidence base: use
  case, affected population, data sources, performance across
  subgroups, risks and mitigations. The crosswalk advises authoring
  one "master" impact assessment per system and emitting
  regime-specific views from it.
- **Candidate / consumer notice content.** The EU AI Act Art. 26(11)
  deployer notice, the Colorado deployer pre-decision notice, and the
  NYC LL 144 candidate notice can share a single notice template with
  jurisdiction-specific inserts.
- **Bias / disparate-impact evidence.** NYC LL 144 bias-audit outputs
  overlap with EU AI Act Art. 10 data-governance evidence and
  Colorado's impact-assessment fairness narrative. They do **not**
  overlap with the EEOC Uniform Guidelines' validation duties (see
  `mod-104`).
- **Model documentation.** EU AI Act Art. 11 technical documentation
  can be assembled from NIST AI RMF `MAP` outputs, an ISO/IEC 42001
  AIMS record (`mod-108`), a model card (`mod-105`), and system logs
  (Art. 12). Colorado documentation lists are a subset.

### 2.4 Where duties diverge (separate work streams required)

- **Prohibitions.** The EU AI Act Art. 5 bans eight practices
  (manipulative techniques, exploitation of vulnerabilities,
  social-scoring by public authorities, predictive policing based
  solely on profiling, untargeted scraping of facial images, emotion
  recognition in workplaces and schools except for medical or safety
  reasons, biometric categorisation of sensitive attributes,
  real-time remote biometric identification by law enforcement
  subject to narrow exceptions). No US state regime currently
  prohibits these at the federal or state level. A compliant US
  posture is not sufficient to avoid Art. 5.
- **Conformity assessment.** EU AI Act Arts. 43–47 require either an
  internal or notified-body conformity assessment for high-risk
  systems. No US state regime has an analogue. A conformity
  assessment cannot be substituted by a US bias audit.
- **GPAI model obligations.** EU AI Act Chapter V obligations on
  general-purpose AI model providers (Art. 53) and systemic-risk
  GPAI providers (Art. 55) have no US state analogue.
- **Provenance and watermarking.** California SB 942's latent /
  manifest provenance duty is content-focused, not system-focused.
  EU AI Act Art. 50(2) overlaps on generative-output marking but is
  narrower (synthetic audio, image, video, text identified as such).
- **Appeal and correction rights.** Colorado grants a consumer the
  right to appeal an adverse consequential decision and to correct
  the data used. The EU AI Act grants a right to explanation (Art.
  86) for deployers of high-risk systems; it does not grant an
  individual-level correction right independent of GDPR Art. 16.
  These are **separate** rights.

### 2.5 Worked applicability — `sys-0012-resume-ranker`

Resume-ranking AEDT used for positions located in multiple
jurisdictions, including New York City, Colorado, Illinois, and the
EU.

| Regime | In scope? | Why |
|---|---|---|
| **EU AI Act** | Yes. Annex III(4) — AI systems intended to be used for recruitment or selection, in particular to analyse and filter job applications. **High-risk.** Provider duties under Arts. 9–15; deployer duties under Art. 26 and Art. 27 FRIA. | Output is used in the Union for hiring. |
| **Colorado AI Act** | Yes. Hiring is a consequential decision. Deployer duties: impact assessment, pre-decision notice, post-decision explanation, appeal. | Colorado-located roles and candidates. |
| **NYC LL 144** | Yes. AEDT definition (§20-870) is met. Annual bias audit, candidate notice, public summary. | NYC-located roles. |
| **Illinois HB 3773 / IHRA** | Yes (if Illinois-located roles). Algorithmic hiring tool falls under the amended IHRA. | Illinois-located roles. |
| **Illinois AIVIA** | Likely not. AIVIA applies to video-interview AI analysis, not resume ranking. | No video-interview component. |
| **California SB 942** | No. Generative-AI provenance scope. | Not generative. |
| **Utah AI Policy Act** | No. Regulated-occupation consumer interaction scope. | Not applicable to hiring. |

### 2.6 Worked applicability — `sys-0088-support-copilot`

Generative-AI customer-support assistant surfacing answers for
customers in the EU, Colorado, California, and Utah. Used by human
support agents; not used in any consequential-decision pathway
(returns, refunds, or account closures route through a human agent
with full authority).

| Regime | In scope? | Why |
|---|---|---|
| **EU AI Act** | Transparency obligations, not high-risk. Art. 50(1) requires users to be informed they are interacting with an AI system; Art. 50(2) requires synthetic-content labelling if applicable. | Not in Annex III; GPAI model is a dependency — provider of the base model owes Art. 53 duties, deployer owes Art. 50. |
| **Colorado AI Act** | Not a consequential-decision system on the facts given. If product moved into refund/credit decisions, the applicability changes. | No consequential decision. |
| **California SB 942** | Yes if generated outputs match the covered-content definition and the deployer meets the "covered provider" threshold. Latent and manifest provenance duties. | Generative outputs. |
| **Utah AI Policy Act** | Yes — generative-AI interaction with a Utah consumer. Disclosure duty triggered. In a regulated-occupation scenario (e.g. legal-adjacent advice), upfront disclosure is required. | Direct consumer interaction. |
| **NYC LL 144 / Illinois HB 3773** | No. Not an AEDT; not used in employment decisions. | — |

### 2.7 Decision rationale

- **One master artefact, regime-specific views.** Keep one impact
  assessment and one notice template per system; emit regime-specific
  outputs from it. Separate artefacts per regime guarantee drift.
- **Jurisdictional nexus is a per-system question.** Do not cache
  nexus decisions at the organisation level; a new system that lands
  in California or that routes outputs into the Union changes the
  answer.
- **Prohibitions are absolute.** No US-side mitigations save an Art. 5
  prohibited-practice system. Kill the use case, do not try to design
  around it.
- **Appeal rights cannot be waived.** Colorado's appeal right and the
  EU AI Act's right to explanation for high-risk systems both bind
  the deployer — vendor terms do not extinguish them.
- **Safe-harbor-adjacent presumptions are narrow.** EU harmonised-
  standards presumption (Art. 40) requires actual conformity with
  the harmonised standard; Colorado's risk-management-programme
  presumption is rebuttable. Neither is a shortcut past the duty.

## 3. Validation steps

1. For each system under review, build the applicability table in
   §2.5 or §2.6 and confirm every "Yes" row has an owner, an
   evidence pointer, and a next-review date.
2. Check that overlapping duties (§2.3) map to one master artefact,
   not duplicated artefacts, in the control catalogue (`mod-108`).
3. Check that diverging duties (§2.4) have separate owners and
   separate evidence trails.
4. Verify EU AI Act Art. 5 prohibitions against the system's use
   case; a prohibited use case fails the crosswalk at step 1.
5. Confirm the pre-decision notice, post-decision explanation, and
   appeal pathway are implemented for every Colorado consequential-
   decision system.
6. Confirm that any system using generative output into California
   meets the SB 942 provenance duty on the output path, not only on
   the model.
7. Confirm Utah disclosure is **upfront**, not post-hoc, for
   regulated-occupation interactions.
8. Re-check the primary sources in §2.1 before the memo ships. State
   AI law moves quickly.

## 4. Rubric or review checklist

Grade the crosswalk on six dimensions, 3 points each (total 18).
Pass = total ≥ 13 with no 0.

| Criterion | 3 — strong | 2 — acceptable | 0–1 — weak |
|---|---|---|---|
| **Instrument shape** | Correctly characterises the EU AI Act as omnibus and risk-based; US state regimes as narrow and subject-specific. | Mostly correct; miscategorises one regime. | Treats one regime as a substitute for the other. |
| **Jurisdictional nexus** | Extraterritorial reach of EU AI Act (Art. 2(1)(c)) named; US "doing business in" tests named per state. | Named for EU but not consistently for US. | Jurisdictional nexus is implicit. |
| **Risk tiering** | EU risk tiers (prohibited, high-risk, limited-risk, GPAI) distinguished from US-state subject-specific coverage. | Risk tiers named but not used in the applicability table. | Omits prohibited practices or GPAI entirely. |
| **Overlap vs. divergence** | Identifies three or more overlaps and three or more divergences, with a specific artefact or duty for each. | Identifies both but at a high level. | Treats overlaps and divergences as interchangeable. |
| **Worked applicability** | Two worked systems (one hiring, one generative) with per-regime applicability and why. | One worked system. | No worked systems. |
| **Primary sources** | Cites the EU AI Act regulation number, the specific state statutes, and dates. | Cites most. | Cites "EU rules" / "Colorado law" generically. |

Document-level checks (binary, all must pass):

- [ ] EU AI Act is cited as Regulation (EU) 2024/1689, not "the EU AI
      Act 2024".
- [ ] Each state regime is cited to its codified reference.
- [ ] Every prohibited practice from Art. 5 is checked against the
      system's use case.
- [ ] The analyst does not claim that any US regime pre-empts the EU
      AI Act, or vice versa.

## 5. Common mistakes

- **Treating NYC LL 144 as a federal rule.** It binds employers for
  NYC-located positions; it does not apply to roles outside NYC.
- **Treating the Colorado AI Act as a generic "US version of the EU
  AI Act".** The Colorado Act is a consequential-decision regime;
  it does not cover GPAI, limited-risk transparency, or prohibited
  practices.
- **Omitting Art. 5 prohibitions.** A US-compliant system can still
  be prohibited in the EU. The crosswalk must check Art. 5 before
  checking Annex III.
- **Treating "doing business in [state]" as equivalent to
  jurisdictional reach under GDPR or the EU AI Act.** The tests are
  different; cite the statute's own nexus language.
- **One master artefact that is *only* a US artefact.** A US-shaped
  impact assessment will not satisfy EU AI Act Art. 27 or Art. 11
  without extension. Author the EU-shape first; it is the
  larger-envelope document.
- **Confusing the EU AI Act Art. 50 transparency duty with the SB 942
  provenance duty.** They overlap on synthetic-content labelling but
  the mechanisms differ.
- **Asserting a Title VII safe harbor from LL 144 or Colorado
  compliance.** Neither displaces federal employment-discrimination
  law; see `mod-104` exercise-04.
- **Freezing the crosswalk.** US-state AI law moves fast. The
  crosswalk must carry a `last_verified_on` date.

## 6. References

- Regulation (EU) 2024/1689 of the European Parliament and of the
  Council of 13 June 2024 laying down harmonised rules on artificial
  intelligence (Artificial Intelligence Act). <https://eur-lex.europa.eu/eli/reg/2024/1689/oj>
- Regulation (EU) 2016/679 — GDPR. <https://eur-lex.europa.eu/eli/reg/2016/679/oj>
- Colorado SB 24-205, "Consumer Protections for Artificial
  Intelligence". <https://leg.colorado.gov/bills/sb24-205>
- NYC Local Law 144 of 2021 — AEDT; NYC Admin. Code §§20-870 to
  20-874. <https://legistar.council.nyc.gov/LegislationDetail.aspx?ID=4344524&GUID=B051915D-A9AC-451E-81F8-6596032FA3F9>
- Illinois AI Video Interview Act (820 ILCS 42). <https://www.ilga.gov/legislation/ilcs/ilcs3.asp?ActID=4015>
- Illinois HB 3773 amending the Illinois Human Rights Act. <https://www.ilga.gov/legislation/BillStatus.asp?DocNum=3773&GAID=17&GA=103>
- California SB 942 — California AI Transparency Act. <https://leginfo.legislature.ca.gov/faces/billNavClient.xhtml?bill_id=202320240SB942>
- Utah Code §§13-2-12 and §§13-70 — Utah AI Policy Act. <https://le.utah.gov/~2024/bills/static/SB0149.html>
- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023. <https://www.nist.gov/itl/ai-risk-management-framework>
- OWASP Machine Learning Security Top 10 — referenced where a
  regime's incident-reporting duty intersects ML-security duties. <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE ATLAS — referenced where ML-security incident vocabulary is
  needed in regulator notifications. <https://atlas.mitre.org/>
