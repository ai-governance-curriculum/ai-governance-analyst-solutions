# Exercise 02 — Council Pre-Read and Minutes — Reference Solution

## 1. Solution overview

An AI governance council works on paper. The pre-read is the document
that lets each council member walk into the meeting ready to decide;
the minutes are the document that records what the council decided and
preserves the accountability trail for an auditor or regulator later.
Both documents are structured artefacts, not free-form notes.

NIST AI RMF 1.0 **GOVERN 2.1** asks for roles and responsibilities to
be documented and **GOVERN 5.2** asks that governance information
reach accountable decision makers in actionable form — the pre-read
is that form. ISO/IEC 42001:2023 Clause 9.3 (management review)
requires inputs and outputs of governance reviews to be retained as
documented information — minutes are that record. The EU AI Act
Art. 17 (quality management system, for high-risk system providers)
and Art. 72 (post-market monitoring) both assume a governance body
with an auditable decision trail.

This solution provides:

- A pre-read **structure and template** with each section explained.
- A **worked pre-read** for a three-item council agenda.
- A **minutes structure and template**.
- The **worked minutes** that correspond to the pre-read above.
- A decision-register row format tied to each decision.
- Decision rationale and a grading rubric.

Scope: this is a design exercise. The templates are a reference;
production pre-reads and minutes should fit the organisation's
minute-taking conventions (board secretary, retention policy, legal
hold rules).

## 2. Worked answer or implementation

### 2.1 Reference council (local exercise context)

> **Body**: AI Governance Council (fictional) — standing cross-
> functional body chartered by the executive committee.
> **Cadence**: monthly, plus ad-hoc for incidents.
> **Core members (role, not person)**: Executive Sponsor (voting
> chair), Head of AI Governance (secretary, non-voting), Chief Risk
> Officer, Deputy General Counsel – Technology, Head of Information
> Security, Head of Talent Operations, Chief Data Officer,
> Independent Member (external, non-executive).
> **Decision rule**: simple majority of voting members with the
> Executive Sponsor's affirmative vote required.
> **Quorum**: four voting members including the Executive Sponsor.
> **Retention**: minutes retained for the longer of seven years or
> the retention period of the underlying artefacts, per records
> policy.

### 2.2 Pre-read — structure

A council pre-read has nine sections. The order is load-bearing:
reading from the top should let a member decide whether to pre-raise
a question without reading the appendices.

1. **Masthead** — meeting date, start time, duration, chair,
   secretary, pre-read version, pre-read sent date.
2. **Agenda** — ordered list of items with type (`decision`,
   `discussion`, `information`), owner, time-box, and the artefact
   reference (e.g. "Item 3 — pre-read §3; appendix 3a").
3. **Decisions required** — one row per decision item stating the
   exact question put to the council, the recommended answer, and
   the owner of the recommendation. A member reading only this
   section should know what vote to expect.
4. **Decision items (detail)** — one block per decision item.
   Each uses the **SIOR** frame: Situation, Impact, Options,
   Recommendation. See Audience 4 in `exercise-01`.
5. **Discussion items (detail)** — items where the council is
   shaping a direction without voting today.
6. **Information items** — items for noting only (metrics,
   incident counts, regulatory horizon).
7. **Open decisions from prior meetings** — list of previously
   approved decisions that have expiry dates or re-review dates
   within the next 90 days.
8. **Appendices** — supporting artefacts (fairness review
   extracts, legal memos, vendor DPAs, system-card excerpts).
9. **Pre-read checklist** — the secretary's one-paragraph note
   confirming the pre-read was distributed at least N business
   days in advance and that quorum is expected.

### 2.3 Pre-read — worked example

**Masthead**

> **Body**: AI Governance Council.
> **Meeting**: 2026-11-16, 14:00–15:30 (90 minutes).
> **Chair**: Executive Sponsor.
> **Secretary**: Head of AI Governance.
> **Pre-read version**: v1.2. **Distributed**: 2026-11-10 (four
> business days prior per charter).
> **Quorum expected**: yes.

**Agenda**

| # | Type         | Title                                                 | Owner                  | Time | Pre-read refs       |
| - | ------------ | ----------------------------------------------------- | ---------------------- | ---- | ------------------- |
| 1 | information  | Metrics and incident digest, Oct 2026                 | Head of AI Governance  | 10 m | §6, app. 6a         |
| 2 | decision     | Vendor DPA — Vendor K (summarisation API)             | Deputy GC – Technology | 15 m | §4.2, app. 4.2a–b   |
| 3 | decision     | Resume-ranker Q3 bias finding — NYC pause             | Head of Talent Ops     | 30 m | §4.1, app. 4.1a–d   |
| 4 | discussion   | EU AI Act Chapter III readiness — target state Q2 2027 | Head of AI Governance  | 20 m | §5.1, app. 5.1a     |
| 5 | information  | Open decisions review (expiries ≤ 90 days)            | Secretary              | 5  m | §7                  |
| 6 | discussion   | Any other business                                    | Chair                  | 10 m | —                   |

**Decisions required**

| Item | Question put to the council                                                                                                             | Recommended answer | Recommended by           |
| :--- | :-------------------------------------------------------------------------------------------------------------------------------------- | :----------------- | :----------------------- |
| 3    | Approve a 30-day pause of NYC use of the resume-ranker and the Q4 remediation plan; delegate resume-of-service to Head of Talent Ops?  | Approve.           | Head of Talent Operations |
| 2    | Approve the Vendor-K DPA and the proposed data-use restrictions prior to contract execution?                                           | Approve with the three stipulations in §4.2.4. | Deputy GC – Technology  |

**§4.1 — Resume-ranker Q3 bias finding (decision item)**

**Situation.** The Q3 fairness review (see `mod-104`) shows a
selection-rate ratio of 0.74 for one protected subgroup against the
reference group. The widely-cited four-fifths rule threshold is
0.80. Finding is internal; no complaint or regulator inquiry has
been received. The 2026 NYC Local Law 144 independent audit (dated
2026-10-15) did not reveal the finding because it was computed on
an earlier scoring set.

**Impact.**

- Candidates: qualified applicants in the affected subgroup are
  screened out at a higher rate than their peers at the deployed
  threshold.
- Business: a 30-day NYC pause adds ~10 recruiter-hours/week for
  four weeks on NYC roles. Other regions continue.
- Regulatory: NYC LL 144 bias-audit obligation already met on the
  2026 cycle; next audit will absorb the Q3 finding. EU AI Act
  Annex III(4) brings Chapter III obligations into force for the
  EU use from 2026-08-02.

**Options.**

- **A — Pause NYC, remediate, re-audit, resume (recommended).**
  30-day pause; Engineering ships a threshold reset or a group-wise
  calibration layer; LL 144 template re-run before resume; other
  regions remain live with tightened human-in-the-loop.
- **B — Continue all regions, remediate in parallel.** Lowest
  operational cost; higher residual risk; harder to defend to an
  auditor if a complaint is filed during remediation.
- **C — Pause globally.** Lowest residual risk; highest operational
  cost; disproportionate given LL 144 is the only binding
  instrument in force today.

**Recommendation.** Option A. Delegate resume-of-service to the
Head of Talent Operations on evidence that the Q4 slice-table
selection-rate ratio is ≥ 0.85. Expected resume date 2026-12-15.

**Dissent room noted in pre-read.** HR Business Partner recorded in
the working group a concern that candidates currently in the NYC
pipeline must be notified of the pause under 6 RCNY §5-303 — Legal
to confirm at the meeting.

**Appendix refs.** 4.1a Q3 fairness review (full). 4.1b LL 144
audit letter (2026-10-15). 4.1c remediation plan (Engineering
owner). 4.1d risk-register row export.

**§4.2 — Vendor-K summarisation API DPA (decision item)**

**Situation.** Vendor K provides a summarisation API we propose to
embed in `sys-0019-ticket-triage`. The DPA v2.4 is under review.
Open issues: sub-processor list; retention; audit cooperation;
training-use of customer inputs.

**Impact.** The service would process internal support tickets
including, at times, personal data under GDPR (deployer role) and
Virginia CDPA (for Virginia-resident customers).

**Options.** Accept as drafted; accept with stipulations; reject.

**Recommendation.** Accept with three stipulations:

1. "No training on our data" clause strengthened from unilateral
   opt-out to default-off plus contractual breach remedy.
2. Sub-processor list to be published and change-notified at
   30 days.
3. Right to audit (or receive a current SOC 2 Type II + ISO/IEC
   27001 report) at the deployer's request, no more than annually.

**Appendix refs.** 4.2a DPA v2.4 (redlined). 4.2b Vendor K
SOC 2 Type II summary.

**§5.1 — EU AI Act Chapter III readiness (discussion)**

Prepare the Council to decide, at the January 2027 meeting, which
of our six Annex III candidate systems will be in Chapter III
compliance mode by the 2026-08-02 effective date for the EU use.
No vote today. Working group to come back with: (a) slate of
in-scope systems; (b) gap map against Arts. 8–15; (c) resourcing.

**§6 — Information: metrics and incident digest (Oct 2026)**

- Systems in inventory: 47 (↑ 2 since Sep). High-risk: 9.
- Open obligations tracked: 118 (23 due within 90 days).
- Incidents opened in Oct: 3 (2 low, 1 medium). Zero reportable
  under any in-force instrument.
- LL 144 audit cycle: complete. EU AI Act gap register: 68% closed.
- Fairness reviews completed this month: 4 of 4 scheduled.

**§7 — Open decisions nearing review (≤ 90 days)**

| Decision ID | Subject                                   | Expiry     | Owner                  | Action                        |
| :---------- | :---------------------------------------- | :--------- | :--------------------- | :---------------------------- |
| DEC-2026-014 | Risk acceptance — `sys-0028` (low band)  | 2027-01-08 | System owner           | Owner to confirm or escalate. |
| DEC-2026-021 | Vendor-J contract renewal (data scope)   | 2027-01-20 | Deputy GC – Technology | Renewal paper for Jan meeting. |

**§8 — Appendices**: 4.1a–d, 4.2a–b, 5.1a, 6a.

**§9 — Secretary's checklist**: Distribution ≥ 3 business days —
met (four). Quorum confirmed. Independent Member to attend remote.
Decisions required marked at the top of agenda. Appendix access
control: internal — Governance Council distribution list.

### 2.4 Minutes — structure

Minutes mirror the pre-read but record **what happened**. Eight
sections, each load-bearing.

1. **Masthead** — body, meeting date/time/location (remote or in
   person), chair, secretary, attendees (voting / non-voting /
   guests), quorum met at time, minutes version, approval status
   ("draft" / "approved on <date>").
2. **Attendance** — list, with absences and reasons. Independent
   Member attendance tracked explicitly.
3. **Prior-meeting approvals** — approval of the previous
   meeting's minutes; status of action items from last meeting.
4. **Decisions taken** — for each decision item: the exact
   motion as voted, the vote tally (for / against / abstain),
   named dissents (if the member asks for their name to be
   recorded), conditions and expiry, and the decision ID
   assigned in the decision register.
5. **Discussion items** — summary of the discussion, named
   contributors only where consent is given or where the point
   is load-bearing for a later decision.
6. **Information items** — one-line each; link to the artefact.
7. **Action items** — table with action, owner (role), due date,
   decision-register link.
8. **Next meeting** — date, pre-read due by, closing time.

### 2.5 Minutes — worked example (for the 2026-11-16 meeting)

**Masthead**

> AI Governance Council — Minutes of the meeting of 2026-11-16,
> 14:00–15:30. Held remotely via video conference.
> Chair: Executive Sponsor.
> Secretary: Head of AI Governance.
> Minutes version: v1.0 (draft).
> Approval status: draft, submitted for approval at the
> 2026-12-14 meeting.

**Attendance**

| Role                           | Attended | Voting | Notes                        |
| ------------------------------ | :------: | :----: | ---------------------------- |
| Executive Sponsor              | Y        | Y      | Chair.                       |
| Head of AI Governance          | Y        | —      | Secretary.                   |
| Chief Risk Officer             | Y        | Y      |                              |
| Deputy GC – Technology         | Y        | Y      |                              |
| Head of Information Security   | Y        | Y      |                              |
| Head of Talent Operations      | Y        | Y      |                              |
| Chief Data Officer             | N        | Y      | Apology sent 2026-11-14.     |
| Independent Member             | Y        | Y      | Attended remotely.           |
| HR Business Partner (guest)    | Y        | —      | Item 3 only.                 |

**Quorum**: six voting members present at start — quorum met.

**Prior minutes.** Minutes of 2026-10-19 approved as circulated.

**Action items from last meeting**: 5 opened, 4 closed, 1 carried
(DEC-2026-014 revalidation — on track).

**Item 3 — Resume-ranker Q3 bias finding (decision)**

Head of Talent Operations presented the finding and the
recommendation per pre-read §4.1. Engineering lead summarised the
remediation plan. Deputy GC – Technology confirmed that 6 RCNY
§5-303 requires notice to candidates currently in the NYC pipeline
where their application status will change; HR Business Partner
confirmed that an ATS banner and a recruiter-to-candidate email
template were ready.

Dissent noted: the Independent Member asked to record that Option C
(global pause) warranted a second look; the Council acknowledged
and moved to vote on Option A.

**Motion.** Approve a 30-day pause of NYC use of the resume-ranker
beginning 2026-11-16; approve the Q4 remediation plan with target
delivery 2026-11-20; approve the tightened human-in-the-loop
posture in all other regions for the duration of the pause;
delegate resume-of-service to the Head of Talent Operations on
evidence that the Q4 slice-table selection-rate ratio is ≥ 0.85;
sunset date for this acceptance: 2027-02-16.

**Vote**: For 5, Against 0, Abstain 1 (Independent Member, who
asked to be recorded as preferring Option C).

**Carried.** Decision ID assigned: **DEC-2026-029**.

**Item 2 — Vendor-K DPA (decision)**

Deputy GC – Technology presented three stipulations per pre-read
§4.2. No material questions. The Vendor-K DPA will not be executed
until the stipulations are incorporated in a redlined counter.

**Motion.** Approve the Vendor-K DPA v2.4 conditional on the three
stipulations in pre-read §4.2.4; authorise the Deputy GC –
Technology to execute on counter-sign by Vendor K; re-review in
twelve months or on any sub-processor change exceeding the 30-day
notice obligation, whichever is earlier.

**Vote**: For 6, Against 0, Abstain 0.

**Carried.** Decision ID assigned: **DEC-2026-030**.

**Item 4 — EU AI Act Chapter III readiness (discussion)**

Head of AI Governance presented the pre-read §5.1 framing. The
Council agreed on the slate approach — the working group will
return in January 2027 with a shortlist and gap map. CRO flagged
that the risk-acceptance process for any system not in compliance
mode by 2026-08-02 would need its own council paper. No motion.

**Item 1, 5, 6** — information and AOB.

Noted:

- Metrics and incident digest (Oct 2026) — no reportable incidents.
- Open decisions review: DEC-2026-014 and DEC-2026-021 are both
  on-track; secretary to send revalidation reminders at 30 days.
- No other business.

**Action items**

| ID            | Action                                                                                          | Owner                  | Due         | Linked decision |
| :------------ | :---------------------------------------------------------------------------------------------- | :--------------------- | :---------- | :-------------- |
| ACT-2026-071  | Issue ATS banner and candidate email for the NYC pause.                                        | HR Business Partner    | 2026-11-17  | DEC-2026-029    |
| ACT-2026-072  | Ship remediation (threshold reset or group-wise calibration) and re-run `mod-104` slice table. | Engineering lead       | 2026-11-20  | DEC-2026-029    |
| ACT-2026-073  | Confirm resume-of-service and notify Council secretary.                                        | Head of Talent Ops     | 2026-12-15  | DEC-2026-029    |
| ACT-2026-074  | Submit DPA counter with three stipulations to Vendor K.                                        | Deputy GC – Technology | 2026-11-23  | DEC-2026-030    |
| ACT-2026-075  | Return with Chapter III shortlist and gap map.                                                 | Head of AI Governance  | 2027-01-18  | —               |

**Next meeting**: 2026-12-14, 14:00–15:30. Pre-read due by
2026-12-08 (four business days prior).

**Approval**: minutes to be submitted for approval at the
2026-12-14 meeting.

### 2.6 Decision-register row format

Each council decision produces one row in the decision register.
The register is the auditable record and lives alongside the risk
register (see `mod-103`) and regulatory tracker (see `mod-106`).

```yaml
decision:
  id: DEC-2026-029
  date_taken: 2026-11-16
  body: AI Governance Council
  subject: Resume-ranker Q3 bias finding — NYC 30-day pause and remediation.
  motion_verbatim: |
    Approve a 30-day pause of NYC use of the resume-ranker beginning
    2026-11-16; approve the Q4 remediation plan with target delivery
    2026-11-20; approve the tightened human-in-the-loop posture in
    all other regions for the duration of the pause; delegate
    resume-of-service to the Head of Talent Operations on evidence
    that the Q4 slice-table selection-rate ratio is ≥ 0.85; sunset
    date for this acceptance 2027-02-16.
  vote:
    for: 5
    against: 0
    abstain: 1
    named_dissent:
      - role: Independent Member
        note: Preferred Option C (global pause).
  conditions:
    - Resume-of-service evidence: Q4 slice-table ratio ≥ 0.85.
    - Tightened HITL posture in non-NYC regions during pause.
  sunset_date: 2027-02-16
  linked_risk_ids: [RISK-sys-0012-adverse-impact]
  linked_obligation_ids: [nyc-ll144-annual-bias-audit]
  action_item_ids: [ACT-2026-071, ACT-2026-072, ACT-2026-073]
  evidence:
    - /minutes/2026-11-16-council.pdf
    - /evidence/2026/q3-fairness-review-resume-ranker.pdf
  owner: Head of Talent Operations
  reviewer: AI Governance Analyst
```

### 2.7 Decision rationale

- **Pre-read drives the meeting; minutes record what happened.**
  Members who can decide from the pre-read need the meeting only
  for questions and votes, so the meeting runs in its time-box.
  Minutes exist to prove what the council did, not to re-litigate it.
- **Decisions required at the top of the agenda.** A member running
  short on time should still know what they will be asked to vote
  on. Burying the ask on page 7 is how pre-reads fail.
- **SIOR framing for decision items.** Situation-Impact-Options-
  Recommendation is compact and consistent. Members build pattern
  memory across months of meetings.
- **Motion verbatim in the minutes.** The motion is what the
  council voted on; paraphrase loses the conditions. "Approved the
  pause" does not capture the sunset date, the Q4 evidence
  threshold, or the delegation. Record the motion as voted.
- **Vote tally with named dissents only on request.** NIST AI RMF
  1.0 GOVERN 2.1 asks for accountable, documented roles — dissent
  is part of that record where the member asks. Default to
  recording tallies without names so members speak freely.
- **Role names, not person names, as owners.** Teams and people
  rotate. See the same choice in `mod-103` and `mod-106`.
- **Decision ID + action-item IDs cross-linked.** Each action item
  carries the parent decision; each decision carries the action
  items. Lose one of those links and the trail breaks.
- **Sunset date on every acceptance.** NIST AI RMF 1.0 MANAGE 1.3
  frames acceptance as a decision that should be reviewed; the
  sunset is how the review gets forced.
- **Pre-read lead time written into the charter.** Members who
  receive the pre-read the morning of the meeting cannot be said
  to have decided from it. Four business days is a defensible
  default for a monthly standing council.
- **Appendices, not inlined artefacts.** Keep the pre-read short
  enough to read in one sitting. The full fairness review belongs
  in appendix 4.1a.
- **Open decisions review at every meeting.** A council that does
  not review its own open decisions produces decisions that
  expire silently.

## 3. Validation steps

1. **Pre-read completeness.** Every decision item must contain
   Situation, Impact, Options, and Recommendation plus appendix
   references. Reject items missing any.
2. **Call-to-action.** The "Decisions required" section must
   list the exact question put to the council for each decision
   item; the item detail must match the question.
3. **Lead-time check.** Pre-read distribution timestamp must
   predate the meeting by at least the charter-defined lead time
   (four business days in this reference).
4. **Minutes-to-pre-read reconciliation.** Every decision item
   in the pre-read produces either (a) a decision in the minutes
   with a vote tally and a decision ID, or (b) an explicit
   "withdrawn / deferred" note with reason and new owner.
5. **Motion-verbatim check.** The motion recorded in the minutes
   contains every condition and date that appears in the
   recommendation. A motion that drops the sunset date fails.
6. **Action-item ID link.** Every action item in the minutes
   references a decision ID (or carries an "administrative"
   label for council housekeeping).
7. **Decision-register row created.** Every decision in the
   minutes is written to the decision register with the same ID
   and the motion verbatim.
8. **Sunset-date present.** Every acceptance-style decision
   carries a sunset date. A no-sunset decision should be a
   deliberate, documented choice recorded as such in the motion.
9. **Independent-Member attendance tracked.** If the charter
   requires Independent Member presence for certain votes,
   confirm attendance on those items specifically.
10. **Retention and access.** Minutes stored in the records
    system under the stated retention policy. Appendices follow
    the same retention or longer.

## 4. Rubric or review checklist

Grade the learner's submission on six dimensions, 3 points each
(total 18). Pass = total ≥ 13 with no 0.

| Criterion | 3 — strong | 2 — acceptable | 0–1 — weak |
|---|---|---|---|
| **Pre-read structure** | All nine sections present; decisions-required at the top; SIOR applied to decision items. | 7–8 of 9 sections present. | Free-form narrative with no clear decisions block. |
| **Minutes structure** | All eight sections present; attendance, quorum, prior-minutes, motion verbatim, vote tally, action-item table, next meeting date. | 6–7 of 8 sections present. | Minutes are narrative paragraphs with no vote tallies. |
| **Decision discipline** | Each decision has motion verbatim, tally, sunset, conditions, decision ID, linked risk/obligation IDs. | Each decision has motion and tally; one or two conditions dropped. | Only a narrative description of what the council "agreed." |
| **Audit trail** | Pre-read → minutes → decision register → action items IDs all cross-link. | Most links present; one gap. | No IDs; nothing cross-linked. |
| **Role, not person, named** | Owners are roles; dissents named only by role (unless member consents). | Mostly roles; one accidental person-name. | Person names throughout. |
| **Lead time and retention** | Pre-read lead time confirmed; retention and access-control called out. | Lead time met but retention silent. | No distribution timestamp; no retention note. |

Document-level checks (binary, all must pass):

- [ ] Each decision in the minutes carries a decision ID
      (`DEC-YYYY-NNN`) that resolves in the decision register.
- [ ] Each action item carries an owner role and a due date.
- [ ] Every decision has either a sunset date or an explicit
      "no sunset — [reason]" note in the motion.
- [ ] The motion verbatim matches the pre-read recommendation
      sentence-for-sentence, or the diff is called out in the
      minutes text.
- [ ] The minutes are marked `draft` until approved at the
      following meeting.

## 5. Common mistakes

- **Writing the pre-read as a narrative.** Members scan structured
  documents; they read narratives only when forced. The nine
  sections exist to be scannable.
- **Decision question buried in the detail.** The decisions-
  required block exists so a member knows what to vote on before
  reading the detail. Dropping it costs meeting time.
- **"The council agreed" as a minute entry.** This is not a
  decision; it is a narrative. Record the motion verbatim, the
  tally, and the ID.
- **No sunset on an acceptance.** A risk accepted indefinitely is
  an un-reviewable decision. See NIST AI RMF 1.0 MANAGE 1.3.
- **Dissent omitted.** The council's role includes being a
  record of challenge. If a member asked to be recorded, the
  minutes record them.
- **Pre-read sent the morning of the meeting.** The council has
  not decided from a pre-read it had no time to read; the
  decision is retroactively vulnerable.
- **Minutes re-litigate rather than record.** Long summaries of
  "who said what" create risk if the council's consensus drifts
  later. Record the motion; summarise discussion briefly; name
  dissent on request.
- **No decision register.** The minutes capture one meeting; the
  decision register is the across-meetings view. Without it,
  nobody knows what is accepted today versus last quarter.
- **Appendices inlined.** The pre-read loses its decision frame
  if the full fairness review is pasted into it. Use appendix
  references.
- **Owner is a team.** "Engineering will deliver" is not an
  accountability line. Name a role. See `mod-103` exercise-05.
- **Person-names in a forwarded pre-read or minutes.** Pre-reads
  and minutes get forwarded widely; role names protect members
  from being named out of context.

## 6. References

- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January
  2023 — in particular `GOVERN 2.1` (roles and responsibilities
  documented), `GOVERN 2.3` (executive leadership engaged),
  `GOVERN 4.1` (organisational practices documented),
  `GOVERN 5.2` (information in actionable form to accountable
  decision makers), `MANAGE 1.3` (response decisions documented
  and reviewed).
  <https://www.nist.gov/itl/ai-risk-management-framework>
- ISO/IEC 42001:2023 — *Information technology — Artificial
  intelligence — Management system*, Clause 9.3 (management
  review) and Clause 7.5 (documented information).
  <https://www.iso.org/standard/81230.html>
- Regulation (EU) 2024/1689 (EU AI Act), Art. 17 (quality
  management system for high-risk providers) and Art. 72 (post-
  market monitoring).
  <https://eur-lex.europa.eu/eli/reg/2024/1689/oj>
- NYC Department of Consumer and Worker Protection, *Final
  Rules on Automated Employment Decision Tools*, 6 RCNY ch. 5,
  subch. T — referenced in the worked example only as the
  underlying obligation.
  <https://rules.cityofnewyork.us/rule/automated-employment-decision-tools-updated/>
- OWASP Machine Learning Security Top 10 — referenced where an
  incident digest item surfaces an ML-security risk.
  <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE ATLAS — same, for adversary-technique framing in the
  security section of a council digest.
  <https://atlas.mitre.org/>
- Local exercise context: `mod-103` (risk assessment);
  `mod-104` (fairness review); `mod-106` (regulatory tracker);
  `mod-108` (controls); `mod-109` (incident / monitoring);
  `mod-110` exercise-01 (audience-adapted briefings).
