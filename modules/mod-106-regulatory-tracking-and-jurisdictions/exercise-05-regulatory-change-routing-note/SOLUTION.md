# Exercise 05 — Regulatory-Change Routing Note — Reference Solution

## 1. Solution overview

When a new law, an amendment, a court decision, a regulator guidance
document, or an enforcement action lands, the AI governance analyst's
job is **not** to say whether it applies — that's Legal's call. The
analyst's job is to **route**: translate the change into a one-page
note the right people read, with a clear ask, a clear owner, and a
clear expiry. Without the route, a regulator feed becomes a reading
list; with it, the organisation moves.

A good routing note is short, has a specific ask of a specific role,
and ties every "do this" to an owner on the AI governance team or in a
partner function (Legal, Privacy, Model Risk, Product, Security). Done
badly, the note is "FYI, interesting development" and nothing moves.
Done well, the note creates one or two tickets with owners and dates.

This solution provides:

- A one-page routing-note template with required fields.
- Three worked notes across likely change types (new regulation,
  agency guidance, enforcement action).
- Decision rationale, validation steps, and a grading rubric.

Scope: a routing note is a governance artefact, not a legal
memorandum. Legal analysis is cited, not reproduced. The analyst's
voice is operational.

## 2. Worked answer or implementation

### 2.1 Required fields (the template)

```
Regulatory-Change Routing Note
To:            {primary recipient — role, not team}
Cc:            {secondary recipients, each as a role}
From:          {analyst name and role}
Date:          {YYYY-MM-DD}
Change id:     {stable slug, e.g. "eu-ai-act-art-6-faq-2026-10-15"}
Severity:      {monitor-only | review | act}
Expiry:        {YYYY-MM-DD — when this note stops being authoritative}

1. The change in one sentence.
2. Primary source (link) + authoritative citation.
3. What we think it means operationally, in 3–5 bullets.
   (Operational reading only. Legal analysis is Legal's.)
4. Scope — which of our systems are in scope.
   (Named inventory ids; "none" is a valid, documented answer.)
5. Open questions for {role} — numbered; each with a proposed path
   to resolution.
6. Asks — numbered; each has one owner (role), one due date, and
   one evidence artefact that closes it.
7. Tracker change — record of the regulatory-tracker update
   (regulation.id, obligation.id if new, status change).
8. Next review date.
```

**Three severity levels, used consistently.**

- **monitor-only** — tracker update only; no asks. The change does
  not plausibly reach us today.
- **review** — the change plausibly reaches one or more of our
  systems; Legal, the DPO, or a sector owner is asked to opine.
- **act** — the change definitely reaches us; specific controls,
  artefacts, or deadlines are opened.

### 2.2 Worked note #1 — new regulation (EU AI Act Art. 5 guidance)

For the worked case, assume the European Commission publishes
official Art. 5 prohibited-practices guidance in late 2026 (the
Commission's schedule has been to issue such guidance to clarify
the prohibitions that took effect 2025-02-02 under Regulation (EU)
2024/1689). The analyst sees the publication.

```
Regulatory-Change Routing Note

To:            Head of AI Governance
Cc:            Deputy General Counsel — Technology; Head of Product;
               Head of Talent Operations
From:          AI Governance Analyst
Date:          2026-11-05
Change id:     eu-ai-act-art-5-ec-guidance-2026-10-15
Severity:      review
Expiry:        2027-02-02  (next scheduled regulatory-feed review)

1. The change in one sentence.
   The European Commission has issued guidance on the application of
   the Art. 5 prohibited-practices list of Regulation (EU) 2024/1689.

2. Primary source.
   European Commission, "Guidelines on Article 5 of the AI Act",
   published 2026-10-15. {add URL to primary source once confirmed}
   (Analyst to re-verify the guidance URL and exact publication date
   against the European Commission's digital-strategy site before
   this note is routed; the publication was expected but had not
   resolved at draft time.)

3. What we think it means operationally.
   - The guidance narrows (or extends) the operational reading of
     Art. 5(1)(f) — emotion recognition in the workplace or
     education context — exception for medical or safety reasons.
   - It clarifies the Art. 5(1)(e) ban on untargeted scraping of
     facial images, with worked examples the analyst should map to
     our vendor-supplied components.
   - Art. 5(1)(a)-(b) subliminal and exploitative techniques are
     expanded with worked examples around nudge-style UX.
   - The guidance does not change the February 2025 effective date;
     it clarifies application.
   - Market-surveillance authorities will reference this guidance
     in enforcement; our documentation should anticipate the
     categories named.

4. Scope.
   Candidate systems to review:
   - sys-0012-resume-ranker — Art. 5(1)(f) emotion-recognition
     surface (confirm vendor does not analyse facial expression or
     voice affect in interview playbacks).
   - sys-0088-support-copilot — Art. 5(1)(a) subliminal UX surface
     (the generated voice and rhythm patterns in agent-assist mode).
   - sys-0105-brand-watch — Art. 5(1)(e) untargeted scraping (our
     social-media collection scope, with Legal already engaged on
     this since Q3 2026).

5. Open questions for Legal.
   1. Does sys-0012's vendor-supplied interview-analysis module use
      affect or emotion features? Resolution path: vendor
      questionnaire + Legal review.
   2. Does sys-0105's current collection scope map to Art. 5(1)(e)
      "untargeted scraping" as defined in the guidance?
      Resolution path: compare collection design against the
      guidance's worked example.
   3. Is the "medical or safety reasons" exception available for
      any of our workplace monitoring use cases? Resolution path:
      Legal memo.

6. Asks.
   1. Legal to produce a short applicability note on the three
      candidate systems above. Owner: Deputy GC — Technology. Due:
      2026-11-26. Evidence: short memo filed in /legal/2026/.
   2. Analyst to update the regulatory tracker row for
      `eu-ai-act-2024` with the new obligation entries if applicable.
      Owner: AI Governance Analyst. Due: 2026-11-19. Evidence:
      diff to /tracker/eu-ai-act-2024.yaml.
   3. Head of Talent Operations to confirm the vendor contract for
      sys-0012 forbids affect / emotion analysis without a council
      decision. Owner: Head of Talent Operations. Due: 2026-12-03.
      Evidence: contract clause reference.

7. Tracker change.
   regulation.id: eu-ai-act-2024
   change_log entry added 2026-11-05:
     "EC guidance on Art. 5 published 2026-10-15 — routed."
   No new obligation ids yet (pending Legal applicability note).

8. Next review date.
   2026-11-26 (after Legal note).
```

### 2.3 Worked note #2 — agency guidance (CFPB Circular on adverse-action notices)

Treat the May 2023 CFPB Circular 2023-03 ("Adverse action
notification requirements and the proper use of the CFPB's sample
forms") as the trigger. In reality this is a real CFPB publication;
the note treats it as a change the analyst is routing on first
discovery for a hypothetical organisation that has been slow to
engage with it.

```
Regulatory-Change Routing Note

To:            Chief Compliance Officer
Cc:            Head of Consumer Lending; Head of AI Governance;
               Deputy General Counsel — Consumer Finance
From:          AI Governance Analyst
Date:          2026-11-05
Change id:     cfpb-circular-2023-03-adverse-action
Severity:      act
Expiry:        2027-11-05  (annual review)

1. The change in one sentence.
   The CFPB has clarified that FCRA adverse-action notices must
   identify the specific and accurate reasons for the adverse action,
   including when the creditor uses complex algorithmic models.

2. Primary source.
   CFPB Circular 2023-03, "Adverse action notification requirements
   and the proper use of the CFPB's sample forms", 11 May 2023.
   <https://www.consumerfinance.gov/compliance/circulars/circular-2023-03/>
   See also Circular 2022-03, 26 May 2022, on the ECOA / Reg B
   predecessor duty.

3. What we think it means operationally.
   - "Checklist" reason-code forms that do not match the true
     principal reasons for the decision are inadequate.
   - A reason code that says "model score below threshold" is not
     sufficient on its own; the underlying features that drove the
     adverse action must be reflected.
   - The duty applies to complex / AI/ML models.
   - CFPB will use this as the enforcement reading of FCRA §615
     and ECOA / Reg B §1002.9.

4. Scope.
   In scope today:
   - sys-0034-credit-score — adverse-action pipeline for US
     consumer credit decisions.

5. Open questions for Legal / Compliance.
   1. Our current reason-code taxonomy — does it reflect the
      principal features driving denials, or is it a static mapping?
      Resolution path: sampled audit of 200 denials vs. the model's
      SHAP-style attributions (`mod-104`).
   2. For "model score below threshold" reason codes, what is the
      feature-level evidence we would surface if a consumer
      disputes?
   3. Are the Reg B sample forms we use consistent with the
      Circular's reading?

6. Asks.
   1. Credit Operations to run a sampled audit of 200 recent
      denials, cross-referencing reason codes to top feature
      attributions. Owner: Head of Consumer Lending. Due:
      2026-12-10. Evidence: audit summary in /evidence/2026/.
   2. Compliance to confirm the current reason-code taxonomy
      against Circular 2023-03. Owner: Chief Compliance Officer.
      Due: 2026-12-17. Evidence: CCO memo.
   3. AI Governance Analyst to add a `cfpb-fcra-adverse-action`
      obligation to the regulatory tracker and map it to the
      control catalogue (`mod-108` CTRL-CL-01-adverse-action-notice).
      Due: 2026-11-19.
   4. Legal to confirm whether any changes to disclosures are
      required under state UDAP equivalents. Owner: Deputy GC —
      Consumer Finance. Due: 2026-12-17.

7. Tracker change.
   regulation.id: fcra-15-usc-1681
   New obligation.id: fcra-adverse-action-specific-reasons
     citation: FCRA §615 (15 U.S.C. §1681m); CFPB Circular 2023-03.
     bearer: creditor (deployer).
     obligation_type: disclosure.
     internal_owner: Head of Consumer Lending.
     recurrence: on-adverse-action.
     mapped_controls: [CTRL-CL-01-adverse-action-notice].
     mapped_risk_ids: [RISK-sys-0034-reason-code-accuracy].
     status: in-progress.

8. Next review date.
   2026-12-17 (after audit and CCO memo).
```

### 2.4 Worked note #3 — enforcement action (FTC consent order touching AI)

This note demonstrates the "monitor-only" severity when an
enforcement action reaches another company but reshapes the
regulator's posture in a way the analyst should still log. The
example is generic (not naming any specific respondent) so the note
form is clear.

```
Regulatory-Change Routing Note

To:            Head of AI Governance
Cc:            Head of Trust & Safety; Head of Product; Legal —
               Consumer Protection
From:          AI Governance Analyst
Date:          2026-11-05
Change id:     ftc-order-2026-ai-disgorgement
Severity:      monitor-only
Expiry:        2027-05-05  (six-month review)

1. The change in one sentence.
   The FTC has entered a Section 5 consent order against a third-
   party company requiring deletion of models and training data
   obtained through practices alleged to be deceptive.
   (Analyst to re-verify the specific FTC enforcement action being
   routed against the FTC press-release index before this note is
   issued. The note form is illustrative only.)

2. Primary source.
   Federal Trade Commission, consent order, {case name}, {date},
   Docket No. {}. {add URL}

3. What we think it means operationally.
   - The order continues the "algorithmic disgorgement" remedy
     pattern — the FTC is willing to require destruction of models
     derived from improperly obtained data.
   - Marketing claims about AI performance remain subject to §5
     substantiation; evidence must exist before the claim is made.
   - Scope of the order is specific to the respondent, but the
     posture will be cited in future matters.

4. Scope.
   No system of ours is directly implicated. The order informs the
   substantiation standard we apply to marketing claims about
   sys-0056 and sys-0088.

5. Open questions.
   None for Legal at this time.

6. Asks.
   1. AI Governance Analyst to log the order in the tracker under
      `ftc-act-section-5` with instrument_type: enforcement-action.
      Due: 2026-11-12.
   2. Head of Trust & Safety to add the order to the Q4 council
      reading list for awareness; no action owed. Due: 2026-11-26.

7. Tracker change.
   regulation.id: ftc-act-section-5
   change_log entry added 2026-11-05:
     "Consent order {case name} entered {date}; algorithmic
      disgorgement remedy continued. Monitor-only."
   No new obligation ids.

8. Next review date.
   2027-05-05.
```

### 2.5 Decision rationale

- **Route, do not opine.** The analyst's job is routing. Interpretive
  calls on new instruments belong to Legal, Privacy, Model Risk, or
  the sector owner. The note cites; it does not conclude.
- **Severity is a routing signal, not a legal judgement.**
  `monitor-only` says "log and forget for six months". `review`
  says "please look at this and come back to me". `act` says
  "we owe something specific by a date".
- **Every ask has one owner, one date, one artefact.** Three
  ambiguities turn a note into a thought. One owner, one date, and
  one artefact per ask turn it into a ticket.
- **Named role, not team.** "Legal should take a look" is an
  unauditable aside. "Deputy GC — Technology" is a route.
- **Expiry on every note.** Without an expiry the note hardens into
  a permanent interpretation.
- **Tracker change is required.** A routed change that does not land
  in the tracker (`mod-106` exercise-01) does not persist. The note
  closes with the tracker diff.
- **Scope is per-system.** Named inventory ids are the only
  acceptable form of scope; "all our systems" is not a scope.
- **Severity ladder is self-cancelling.** A `monitor-only` with an
  ask is miscategorised; an `act` without specific asks is also
  miscategorised.

## 3. Validation steps

1. Every routing note has every required field filled or marked
   "none" with a rationale. Missing fields invalidate the route.
2. Severity matches the asks: `monitor-only` has zero `Asks`
   entries (tracker log only); `review` has at least one ask to a
   partner function; `act` has at least one specific artefact and
   date.
3. Every `Ask` entry has exactly one owner, exactly one due date,
   and exactly one evidence artefact.
4. Every "Scope" entry names inventory ids, not categories.
5. Every note closes with a `Tracker change` block that resolves
   against the regulatory tracker schema (`mod-106` exercise-01).
6. Every `Expiry` is set; a note with no expiry is sent back for
   correction.
7. The note is linked from the tracker's `change_log` entry so the
   reverse lookup works (tracker row → routing note).
8. For `act` severity, confirm the control catalogue (`mod-108`)
   has an updated control reference for every new obligation.
9. For `review` severity, confirm a follow-up ticket is open on
   the partner function's queue; without it, the review is lost.

## 4. Rubric or review checklist

Grade the learner's note on six dimensions, 3 points each (total
18). Pass = total ≥ 13 with no 0.

| Criterion | 3 — strong | 2 — acceptable | 0–1 — weak |
|---|---|---|---|
| **Primary source** | Authoritative citation (regulation + date + URL) is present and resolves. | Citation present but imprecise. | Citation missing or paraphrased. |
| **Operational reading** | 3–5 bullets; operational, not interpretive; cites the specific article / section. | Bullets drift into Legal territory. | A single paragraph of Legal opinion. |
| **Scope** | Named inventory ids; "none" is acceptable when the analyst has checked. | Named systems with mixed id / name. | "Various systems" or "the organisation". |
| **Asks** | Each ask has one owner (role), one due date, one evidence artefact. | One axis is missing from one ask. | Asks are collective or dateless. |
| **Severity match** | Severity matches the asks and the scope. | Severity marginal. | `monitor-only` with asks, or `act` without specifics. |
| **Tracker change** | Named `regulation.id`, `obligation.id` where new, and a change-log entry recorded. | Tracker change described but not written. | No tracker change recorded. |

Document-level checks (binary, all must pass):

- [ ] Primary source URL resolves.
- [ ] Every owner is a role, not a person or a team.
- [ ] Every ask has a due date no more than 90 days out.
- [ ] Note has an expiry.
- [ ] The regulatory-tracker row carries this note's id in its
      `change_log`.
- [ ] No Legal conclusion is written as an analyst conclusion.

## 5. Common mistakes

- **Interpreting the change.** The note is a route, not a reading.
  Analyst reading of statutory text as a Legal conclusion inverts
  the responsibility.
- **No named owner.** "Legal will take a look" is a hope, not a
  route.
- **No expiry.** The note hardens into unofficial law inside the
  organisation.
- **"Various systems" scope.** The reviewer cannot act on an
  unnamed set. Inventory ids or the analyst has not done the
  work.
- **Severity drift.** `monitor-only` notes with four asks are the
  most common failure. If there are asks, the severity is
  `review` or `act`.
- **No tracker diff.** The route evaporates. The tracker row is
  the persistence layer.
- **Reusing "FYI" as a severity.** It is not one. "FYI" belongs in
  a Slack message, not a routing note.
- **Scope-creep in the note.** The note names what this change
  changes. It does not relitigate previous controls.
- **Treating enforcement actions as binding on us.** An enforcement
  action against a third party informs posture; it does not
  directly bind us. Severity should usually be `monitor-only` or
  `review`.

## 6. References

- Regulation (EU) 2024/1689 — EU AI Act, Art. 5 and Art. 96
  (European Commission guidance). <https://eur-lex.europa.eu/eli/reg/2024/1689/oj>
- Regulation (EU) 2016/679 — GDPR (routed where a change touches
  Art. 22, Art. 35, or Chapter V international transfers). <https://eur-lex.europa.eu/eli/reg/2016/679/oj>
- Fair Credit Reporting Act, 15 U.S.C. §§1681 et seq.; §615 on
  adverse-action notices. <https://www.ecfr.gov/current/title-16/chapter-I/subchapter-F>
- Equal Credit Opportunity Act / Regulation B, 15 U.S.C. §§1691
  et seq.; 12 C.F.R. Part 1002. <https://www.ecfr.gov/current/title-12/chapter-X/part-1002>
- Consumer Financial Protection Bureau, *Circular 2023-03: Adverse
  action notification requirements and the proper use of the
  CFPB's sample forms*, 11 May 2023. <https://www.consumerfinance.gov/compliance/circulars/circular-2023-03/>
- Consumer Financial Protection Bureau, *Circular 2022-03: Adverse
  action notification requirements in connection with credit
  decisions based on complex algorithms*, 26 May 2022. <https://www.consumerfinance.gov/compliance/circulars/circular-2022-03/>
- Federal Trade Commission, business guidance on AI (2021 and
  2023). <https://www.ftc.gov/business-guidance/blog/2021/04/aiming-truth-fairness-equity-your-companys-use-ai>
- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023
  — `GOVERN 1.5` (legal and regulatory accountability) and
  `MANAGE 1.3` (response to risk — route to the accountable
  party). <https://www.nist.gov/itl/ai-risk-management-framework>
- OWASP Machine Learning Security Top 10 — referenced for routing
  notes that describe ML-security incidents. <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE ATLAS — referenced for routing notes describing adversary-
  technique vocabulary in incident disclosures. <https://atlas.mitre.org/>
