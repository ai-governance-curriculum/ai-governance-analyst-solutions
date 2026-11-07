---
exercise_id: exercise-02-incident-record-composition
module: mod-109-incident-response-and-continuous-monitoring
artifact: SOLUTION.md
---

# Exercise 02 — Incident Record Composition — Reference Solution

## 1. Solution overview

The incident record is the governance artefact an auditor, regulator,
or new on-call reader uses to understand what happened, how it was
detected, how it was contained, who did what, and how the system was
changed afterwards. It sits on top of — not instead of — the
engineering post-mortem. The engineering post-mortem is honest about
cause and fix; the governance incident record is honest about **risk,
owner, obligation, and change**, and it is the artefact that gets
attached to a serious-incident notification if the regulatory clock
runs.

A good record is boring and complete. Every field is populated,
every claim cites its evidence, every action has an owner (role) and
a date, and the classification from exercise-01 is carried forward
verbatim so the two artefacts cross-reference. Done badly, the
record is a narrative paragraph that an auditor cannot verify.

This solution provides:

- A full record schema (YAML) with every required field, mapped to
  NIST AI RMF MANAGE 4.1 and MANAGE 4.3, ISO/IEC 42001:2023 clause 10,
  and the EU AI Act Art. 73 serious-incident report fields where
  relevant.
- A fully worked record for the prompt-injection vignette from
  exercise-01 (incident `inc-2026-11-05-001-support-copilot-leak`).
- A separate "notification-ready" extract — the subset of fields a
  market-surveillance authority or regulator is likely to be given
  under Art. 73 or GDPR Art. 33, with Legal as the sender.
- Decision rationale, validation, grading rubric, and the common
  failure modes to review for.

Scope: the record is a **governance** record. Engineering RCA
(five-whys, system diagrams, code diffs) is linked, not reproduced.
Legal analysis is linked, not reproduced.

## 2. Worked answer or implementation

### 2.1 Required fields — the record schema

```yaml
incident_record:
  # --- Identification ---
  incident_id: string               # same id as the exercise-01 classification
  title: string                     # 10 words or fewer, no customer names
  systems:
    - system_id: string             # FK to mod-102 inventory
      role_in_incident: enum        # "primary" | "contributing" | "downstream-affected"
  classification_ref: string        # path or id of the exercise-01 record

  # --- Timeline ---
  timeline_timezone: string         # IANA, e.g. "Europe/Zurich"
  timeline:
    - at: datetime
      actor: string                 # role of the actor ("support agent", "detection monitor", ...)
      event: string                 # one sentence; what happened or was observed
      evidence_ref: string|null     # link to log entry, screenshot, ticket id

  detected_by: enum                 # "monitor" | "customer-report" | "employee-report" |
                                    # "red-team" | "third-party-researcher" | "regulator" | "other"
  detection_notes: string           # 2–3 sentences; which signal fired or who reported

  # --- Impact ---
  impact:
    users_affected_known_count: integer|null
    users_affected_estimate_range: string|null  # "1-10", "11-100", ...
    data_categories_touched: [enum]             # "none" | "contact-info" | "financial" |
                                                # "health" | "special-category-gdpr-art-9" |
                                                # "children" | "other"
    harm_types:
      - harm_type: enum             # "financial-loss" | "data-exposure" | "unfair-treatment" |
                                    # "physical-safety" | "psychological" | "reputational" |
                                    # "operational-disruption" | "none-observed"
        narrative: string           # 1–2 sentences, no customer-identifying language
    reversible: enum                # "yes" | "partially" | "no" | "unknown"

  # --- Severity & regulatory flags (carried from classification) ---
  severity:
    level: enum                     # "sev-1" | "sev-2" | "sev-3" | "sev-4"
    rubric_ref: string              # link to local severity rubric
    rationale: string
    revised_from: enum|null         # if upgraded/downgraded, previous level
    revised_at: datetime|null

  regulatory_flags:
    - regime: string                # e.g. "eu-ai-act-art-73"
      status: enum                  # "not-triggered" | "triggered-timer-running" |
                                    # "notification-filed" | "escalated-to-legal"
      filed_at: datetime|null
      filed_to: string|null         # authority name
      legal_owner: string           # role of the clock-owner

  # --- Containment, mitigation, remediation ---
  containment_actions:
    - at: datetime
      action: string                # one sentence
      owner: string                 # role, not person
      evidence_ref: string|null
  mitigation_state: enum            # "not-contained" | "contained" | "fully-mitigated"
  rollback_or_disable:
    action: enum                    # "disabled" | "rolled-back" | "rate-limited" |
                                    # "bypassed-to-human" | "no-change"
    at: datetime|null
    owner: string|null

  # --- Root cause (summary, not analysis) ---
  root_cause_summary: string        # 3–5 sentences; cite the engineering RCA link
  rca_link: string                  # link to engineering post-mortem
  contributing_factors: [string]

  # --- Change register & follow-ups ---
  follow_up_actions:
    - id: string                    # "fua-001"
      summary: string
      type: enum                    # "control-add" | "control-change" | "monitor-add" |
                                    # "model-retrain" | "model-retire" |
                                    # "policy-change" | "training" | "vendor-remediation"
      owner: string                 # role
      due: date
      evidence_required: string     # artefact that closes it
      status: enum                  # "open" | "in-progress" | "closed" | "deferred"
      mapped_controls: [string]     # FK to control catalogue (mod-108)

  # --- Communications ---
  communications:
    internal:
      - at: datetime
        audience: string            # "AI council", "model risk committee", "exec", "all-employees"
        summary: string
        owner: string
    external:
      - at: datetime|null
        audience: string            # "affected users", "regulator-X", "press", "customers-all"
        summary: string
        owner: string               # usually Legal or Communications, never Analyst solo

  # --- Cross-links ---
  cross_links:
    nist_ai_rmf:
      manage: [string]              # at minimum ["MANAGE 4.1", "MANAGE 4.3"]
      measure: [string]
    iso_42001: [string]             # e.g. ["A.10 Operational planning and control"]
    related_incidents: [string]     # incident_ids of related prior incidents
    related_risks: [string]         # mod-103 risk ids
    related_obligations: [string]   # mod-106 obligation ids

  # --- Lifecycle ---
  record_status: enum               # "draft" | "in-review" | "approved" | "closed" | "reopened"
  authored_by: string               # role
  reviewed_by: [string]             # roles
  approved_by: string|null          # role
  approved_at: datetime|null
  last_updated_at: datetime
  next_review_date: date            # follow-up review even after closure (default +90d)
```

### 2.2 Worked record — prompt-injection exposure

Carries forward the vignette from exercise-01, incident
`inc-2026-11-05-001-support-copilot-leak`. All dates, user counts,
and vendor references are clearly labelled local exercise context
below; no real-world enforcement action or external incident is
invoked.

```yaml
incident_record:
  incident_id: inc-2026-11-05-001-support-copilot-leak
  title: Support copilot leaked one customer's email via retrieved PDF
  systems:
    - system_id: sys-0088-support-copilot
      role_in_incident: primary
    - system_id: sys-0088-kb-ingestion
      role_in_incident: contributing
  classification_ref: /records/mod-109/exercise-01/inc-2026-11-05-001.yaml

  timeline_timezone: Europe/Zurich
  timeline:
    - at: 2026-11-04T16:02:00+01:00
      actor: vendor-content-publisher (external)
      event: >
        Vendor publishes PDF "Partner onboarding v3.2" to the
        knowledge-base ingestion queue; PDF contains an embedded
        instruction string in a footer text frame.
      evidence_ref: ingest-log/kb/2026-11-04T16-02.json
    - at: 2026-11-04T16:11:00+01:00
      actor: kb-ingestion-pipeline
      event: >
        PDF indexed without content-filter flag; no instruction-
        pattern detection in place at ingest.
      evidence_ref: ingest-log/kb/2026-11-04T16-11.json
    - at: 2026-11-05T09:47:12+01:00
      actor: support agent (role, name redacted)
      event: >
        Customer asks the copilot for account balance; copilot
        retrieves two knowledge-base snippets, one from the vendor
        PDF, and follows the embedded instruction, emitting the
        customer email and a balance figure.
      evidence_ref: chat-transcript/2026-11-05/09-47-12.json
    - at: 2026-11-05T09:49:04+01:00
      actor: support agent
      event: >
        Agent flags the response in the internal "unexpected answer"
        queue; copilot remains available to other agents.
      evidence_ref: ticket/support-flag-2026-11-05-00041
    - at: 2026-11-05T10:12:00+01:00
      actor: detection-monitor
      event: >
        Prompt-injection pattern monitor fires on retrospective scan;
        no further matches in the trailing 24h window.
      evidence_ref: monitor/prompt-injection/2026-11-05T10-12.json
    - at: 2026-11-05T10:28:00+01:00
      actor: on-call ML engineer (role)
      event: >
        Support copilot RAG retrieval is rate-limited to exclude
        the vendor PDF; copilot continues to serve other queries.
      evidence_ref: incident-ticket/inc-2026-11-05-001
    - at: 2026-11-05T11:05:00+01:00
      actor: AI Governance Analyst
      event: >
        Incident classified (exercise-01); Legal engaged for GDPR
        Art. 33 clock assessment; Head of AI Governance notified.
      evidence_ref: /records/mod-109/exercise-01/inc-2026-11-05-001.yaml

  detected_by: employee-report
  detection_notes: >
    The support agent recognised the output as off-policy and used
    the pre-existing flagging queue. The prompt-injection monitor
    confirmed the pattern retrospectively; it had not fired live
    because the ingest-side filter was not in production at the
    time of the incident.

  impact:
    users_affected_known_count: 1
    users_affected_estimate_range: "1-1"
    data_categories_touched: [contact-info, financial]
    harm_types:
      - harm_type: data-exposure
        narrative: >
          One customer's email address and account balance figure
          were emitted in the chat transcript; the receiving party
          was the customer themselves, and the transcript was
          retained only in internal logs.
      - harm_type: operational-disruption
        narrative: >
          Vendor PDF ingestion paused for 18 hours while filters
          were installed.
    reversible: partially

  severity:
    level: sev-2
    rubric_ref: /policies/incident-severity-rubric.md
    rationale: >
      Confirmed single-customer data exposure of identifiable
      information; no evidence of scaled exfiltration.
    revised_from: sev-3
    revised_at: 2026-11-05T11:20:00+01:00

  regulatory_flags:
    - regime: gdpr-art-33
      status: triggered-timer-running
      filed_at: null
      filed_to: null
      legal_owner: Data Protection Officer
    - regime: eu-ai-act-art-73
      status: escalated-to-legal
      filed_at: null
      filed_to: null
      legal_owner: Deputy General Counsel — Technology

  containment_actions:
    - at: 2026-11-05T10:28:00+01:00
      action: >
        Remove the vendor PDF from the retrieval index and
        rate-limit retrievals from the vendor-content namespace.
      owner: On-call ML engineer
      evidence_ref: /change/2026-11-05/retrieval-filter-pr-874
    - at: 2026-11-05T12:40:00+01:00
      action: >
        Deploy ingest-side instruction-pattern scanner for new
        knowledge-base documents; block on match.
      owner: Head of ML Platform
      evidence_ref: /change/2026-11-05/kb-ingest-pr-1120
  mitigation_state: contained
  rollback_or_disable:
    action: rate-limited
    at: 2026-11-05T10:28:00+01:00
    owner: On-call ML engineer

  root_cause_summary: >
    An ingested knowledge-base PDF contained an embedded
    instruction that exploited the copilot's RAG prompt structure.
    The copilot's system prompt did not apply retrieved content
    with an instruction-detection or content-isolation guard. The
    ingest pipeline had no instruction-pattern scanner at the time.
    Detection depended on human review; the prompt-injection
    monitor existed but was configured only for interactive
    user-message patterns, not for retrieved-content patterns.
  rca_link: /engineering/postmortems/2026-11-inc-001.md
  contributing_factors:
    - Vendor content accepted into the index without content
      review.
    - No isolation between retrieved content and system
      instructions in the RAG prompt template.
    - Prompt-injection monitor coverage limited to user turns.

  follow_up_actions:
    - id: fua-001
      summary: >
        Add instruction-pattern scanner to the knowledge-base
        ingest pipeline; block on match; log all blocks for
        review.
      type: monitor-add
      owner: Head of ML Platform
      due: 2026-11-19
      evidence_required: /change/ingest/instruction-scanner-live
      status: in-progress
      mapped_controls: [CTRL-AI-11-rag-ingest-filter]
    - id: fua-002
      summary: >
        Re-template RAG prompt to isolate retrieved content via
        structured delimiters and a system-level instruction
        override.
      type: control-change
      owner: Head of ML Platform
      due: 2026-11-26
      evidence_required: /change/prompt/rag-isolation-template
      status: open
      mapped_controls: [CTRL-AI-12-rag-prompt-isolation]
    - id: fua-003
      summary: >
        Extend prompt-injection monitor to score retrieved-content
        turns; alert on top-k anomaly scores.
      type: monitor-add
      owner: Head of ML Platform
      due: 2026-12-10
      evidence_required: /change/monitor/rag-pi-rules
      status: open
      mapped_controls: [CTRL-AI-10-prompt-injection-monitoring]
    - id: fua-004
      summary: >
        Vendor-content SOP update — content review and signing
        required before ingestion of third-party PDFs.
      type: policy-change
      owner: Head of Vendor Risk
      due: 2026-12-17
      evidence_required: /policy/vendor-content-sop-v2.md
      status: open
      mapped_controls: [CTRL-TP-03-content-review]
    - id: fua-005
      summary: >
        Lessons-learned review with the AI council; record what
        changed (see MANAGE 4.3).
      type: training
      owner: AI Governance Analyst
      due: 2026-12-03
      evidence_required: /council/2026-12/minutes.md
      status: open
      mapped_controls: []

  communications:
    internal:
      - at: 2026-11-05T11:30:00+01:00
        audience: AI council
        summary: >
          Confirmed single-customer exposure; copilot still available;
          follow-up actions proposed; regulatory review in progress.
        owner: AI Governance Analyst
    external:
      - at: null
        audience: affected customer
        summary: >
          Communication will be sent by Legal + Customer Operations;
          not sent at time of record authoring.
        owner: Deputy General Counsel — Technology

  cross_links:
    nist_ai_rmf:
      manage: [MANAGE 4.1, MANAGE 4.2, MANAGE 4.3]
      measure: [MEASURE 2.6, MEASURE 2.7]
    iso_42001:
      - "A.10 Operational planning and control"
      - "A.8 AI system life cycle"
    related_incidents: []
    related_risks: [RISK-sys-0088-rag-prompt-injection]
    related_obligations:
      - eu-ai-act-art-73-serious-incidents
      - gdpr-art-33-breach-notification

  record_status: in-review
  authored_by: AI Governance Analyst
  reviewed_by: [Head of AI Governance, Data Protection Officer]
  approved_by: null
  approved_at: null
  last_updated_at: 2026-11-05T13:00:00+01:00
  next_review_date: 2026-12-10
```

### 2.3 Notification-ready extract

When Legal decides a notification is required, the analyst produces
a derived view with only the fields the authority is being given.
The extract is append-only and is produced from the governance
record, not written separately.

```yaml
serious_incident_notification_extract:
  source_record_id: inc-2026-11-05-001-support-copilot-leak
  regime: eu-ai-act-art-73
  authority_contacted: >
    {market-surveillance authority — Legal to name}
  submitter_role: Deputy General Counsel — Technology
  submitted_at: null                 # set when sent by Legal
  content:
    provider_identifier: {organisation legal name — set by Legal}
    system:
      system_id: sys-0088-support-copilot
      intended_purpose: >
        Customer-support agent assistant generating draft
        responses for a human support agent.
      high_risk_classification: >
        Legal to confirm against Reg. (EU) 2024/1689 Annex III;
        provisional: not Annex III high-risk in this scenario.
    incident:
      awareness_date: 2026-11-05
      description: >
        A retrieved internal document contained an embedded
        instruction that caused the system to emit one customer's
        contact and account information during a support
        interaction. Single user affected; no evidence of further
        exposure.
      harm_type: data-exposure
      measures_taken:
        - Removed offending document from retrieval index.
        - Rate-limited retrievals from the vendor-content namespace.
        - Deployed ingest-side instruction-pattern scanner.
      planned_measures:
        - Re-template RAG prompt for retrieved-content isolation.
        - Extend prompt-injection monitor coverage to retrieved-
          content turns.
    attachments:
      - record.yaml
      - rca.md
      - evidence/retrieval-filter-pr-874.diff
```

The extract field names deliberately track the fields called out in
Regulation (EU) 2024/1689 Art. 73 at the general-schema level
(provider identity, system, intended purpose, incident description,
measures taken). Legal owns the final submission and the
authority-specific form.

### 2.4 Decision rationale

- **Record ≠ post-mortem.** The engineering post-mortem (five-whys,
  code diffs, infra graphs) is linked. The governance record names
  the owner, obligation, control change, and regulatory status; it
  is the artefact an auditor reads first.
- **Timeline is in one timezone, with IANA ID.** "09:47" is
  ambiguous across jurisdictions; the Art. 73 notification needs a
  single clock.
- **Role, not person, everywhere.** Mirrors `mod-102`, `mod-106`,
  `mod-108`. People leave; roles persist for audit.
- **`detected_by` is a bounded vocabulary.** Reading "customer
  report" vs "monitor" vs "red-team" vs "regulator" instantly tells
  the reader what the detection stack did and did not catch.
- **Severity carries a `revised_from`.** Incidents regularly change
  severity after triage. Keeping the history prevents the "we never
  thought it was serious" audit finding.
- **Regulatory flags are not Legal's analysis.** The record names
  the regime, the clock state, and the clock-owner. Legal's memo is
  linked, not pasted.
- **Follow-ups have owners, dates, and evidence.** An action without
  an artefact that closes it is wishful thinking.
- **`mapped_controls` closes the loop to `mod-108`.** Every control
  change should land as a row in the control catalogue.
- **The notification-ready extract is derived.** Avoid two sources
  of truth. The extract is a projection; edits happen on the record.
- **`record_status` and `approved_by` gate publication.** Draft
  records are not referenced externally. Approval is a role (Head of
  AI Governance or DPO, depending on the regime).

## 3. Validation steps

1. Schema check: every required field populated; `timeline_timezone`
   is a valid IANA zone; every `at` is RFC 3339 with offset.
2. Timeline ordering: every `timeline[*].at` is monotonically
   non-decreasing; any out-of-order entry fails CI.
3. Ownership resolution: every `owner` resolves to a role in the
   organisation's role directory; "John" / "the team" / team names
   fail.
4. Evidence resolution: every `evidence_ref` and `rca_link` resolves
   to a URL or path; broken links fail.
5. Regulatory-flag consistency: any flag with `status =
   notification-filed` has `filed_at`, `filed_to`, and `legal_owner`.
6. Severity rubric linkage: `severity.rubric_ref` resolves; revised
   severity entries include `revised_from` and `revised_at`.
7. Cross-link resolution: at least one `nist_ai_rmf.manage` entry
   is present and is a real subcategory (MANAGE 1.1 … MANAGE 4.3).
8. Follow-up actions: for every `follow_up_actions[*]` with `status`
   ∈ {`open`, `in-progress`}, `due` is in the future at the time of
   record approval; any past-due open action blocks approval.
9. Approval gate: `record_status = approved` requires
   `approved_by` and `approved_at` to be set; `last_updated_at`
   must equal or follow `approved_at`.
10. Notification-extract integrity: the extract's `source_record_id`
    equals the record's `incident_id`; any `content.*` field that is
    not derivable from the record flags a review.

## 4. Rubric or review checklist

Grade on six dimensions, 3 points each (total 18). Pass = total
≥ 14 with no 0.

| Criterion                                | 3 — strong                                                                                                                 | 2 — acceptable                                                                 | 0–1 — weak                                                                        |
| ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ | --------------------------------------------------------------------------------- |
| **Timeline quality**                     | Timestamps are RFC 3339 with offset; one IANA zone; every event has an actor (role) and an evidence pointer.               | Mostly populated; one or two missing evidence pointers.                        | Narrative paragraph; no explicit times; actors are "we".                          |
| **Impact fields**                        | Discrete counts or ranges; bounded `harm_types`; `data_categories_touched` populated; `reversible` set.                    | `harm_types` populated but `data_categories_touched` or `reversible` missing.  | Impact is a prose sentence with no structured fields.                             |
| **Severity discipline**                  | `severity.level`, `rubric_ref`, `rationale`, and any revision history populated.                                           | Level and rationale present but no rubric linkage.                             | Level only, no rationale.                                                         |
| **Regulatory-flag routing**              | Each regime named with its article; clock owner is a Legal role; `status` and `filed_*` fields consistent.                 | Each regime named but clock owner not specified.                               | Flags are vague ("GDPR"); no Legal owner; status not tracked.                     |
| **Follow-ups and control mapping**       | Every action has owner (role), due date, evidence artefact, and a `mapped_controls` entry when a control changed.          | Every action has owner and due date; control mapping sparse.                   | Actions are bullet points; no owner or due date.                                  |
| **Cross-links and lifecycle**            | NIST AI RMF `MANAGE` entries present; ISO/IEC 42001 clause linked; `record_status` with `approved_by` gate enforced.       | NIST AI RMF present; ISO/IEC 42001 missing; status gate enforced.              | No cross-links; status left as `draft`.                                           |

Document-level binary checks (all must pass):

- [ ] `incident_id` matches the exercise-01 classification record.
- [ ] `timeline_timezone` is a valid IANA zone.
- [ ] Every `owner` and `authored_by` is a role.
- [ ] No customer PII in free-text narrative fields.
- [ ] `regulatory_flags[*].legal_owner` is set for any
      `triggered-timer-running` or `notification-filed` status.
- [ ] `follow_up_actions[*].evidence_required` is a concrete
      artefact, not "write a plan".

## 5. Common mistakes

- **Writing a narrative where a timeline is needed.** Stories are
  unauditable. Timelines with timestamps, actors, and evidence refs
  are.
- **No timezone or mixed timezones.** Art. 73 reporting and GDPR
  Art. 33 clocks both require a single, named clock.
- **Collapsing severity and regulatory flag.** Severity is internal
  paging; regulatory flag is an external clock. Different fields,
  different owners.
- **"Legal reviewed" with no article cited.** "GDPR" is not a
  citation; "GDPR Art. 33" is. Same for Art. 73 vs Art. 72 of the
  EU AI Act.
- **Follow-ups without evidence_required.** "Update the SOP" is not
  a closable action; "/policy/vendor-content-sop-v2.md published" is.
- **Named individuals in `owner` fields.** People move teams. Audit
  fails.
- **Treating the record as the post-mortem.** The two are different
  artefacts. Linking the RCA from the record is correct; copy-
  pasting it in is not.
- **Reopening a record by overwriting history.** Updates append;
  reopening uses `record_status: reopened` and a new entry in the
  timeline with a clear actor.
- **Submitting the extract instead of the record internally.** The
  extract is for external use. Internal readers get the record.
- **Treating "no regulatory flag triggered" as the absence of a
  decision.** Mark the flag explicitly `not-triggered` with a one-
  sentence rationale; a blank is unreviewable.
- **Approving a record with past-due open actions.** The approval
  gate exists precisely to prevent this. Approve only when all open
  actions have a realistic future `due` date.

## 6. References

- Regulation (EU) 2024/1689 — EU AI Act, Art. 72 (post-market
  monitoring plan) and Art. 73 (reporting of serious incidents by
  providers of high-risk AI systems). Legal owns final
  interpretation and submission. <https://eur-lex.europa.eu/eli/reg/2024/1689/oj>
- Regulation (EU) 2016/679 — GDPR Art. 33 (notification of a
  personal data breach to the supervisory authority) and Art. 34
  (communication to the data subject). <https://eur-lex.europa.eu/eli/reg/2016/679/oj>
- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 —
  `MANAGE 4.1` incident identification and documentation,
  `MANAGE 4.3` continual improvement and lessons learned,
  `MEASURE 2.6`/`MEASURE 2.7` on measurement of robustness and
  trustworthy characteristics. <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, *AI RMF Playbook* — companion actions for the MANAGE
  function, especially incident response. <https://airc.nist.gov/AI_RMF_Knowledge_Base/Playbook>
- ISO/IEC 42001:2023 — *Artificial intelligence management system*,
  Clause 10 Improvement, and Annex A controls A.8 (AI system life
  cycle) and A.10 (operational planning and control). <https://www.iso.org/standard/81230.html>
- OWASP Machine Learning Security Top 10 (ML01–ML10). <https://owasp.org/www-project-machine-learning-security-top-10/>
- OWASP Top 10 for Large Language Model Applications (LLM01–LLM10). <https://owasp.org/www-project-top-10-for-large-language-model-applications/>
- MITRE ATLAS — tactics and techniques used in the incident
  classification carried over from exercise-01. <https://atlas.mitre.org/>
