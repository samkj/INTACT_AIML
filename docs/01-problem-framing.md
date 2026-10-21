# 1. Problem framing

## The problem I think we are solving

The brief says auditors spend time "finding, reading and interpreting information." The deeper problem is **decision latency and decision risk**. The auditor is not asking, *"Summarize this PDF."* They are asking:

* *"What changed since the last audit, and does it matter?"*
* *"Which previous finding is still open, and is the evidence of closure credible?"*
* *"Where is the highest risk in this audit scope, and why?"*

The real value is **triage under uncertainty**: helping the auditor arrive knowing **where to look first and what to push back on**.

## What I'd want to learn before building (first 2 weeks, before any model work)

- Shadow 3-5 auditors across at least 3 schemes, mixed seniority for a full prep cycle. Where do they actually spend minutes vs. seconds?
- "Tell me about an audit where you were surprised on site." What information existed beforehand? Why was it missed? (This is our best source of ground truth for what "deserves attention" means.)
- What does an auditor currently do when two documents conflict? Do they trust their own memory, a colleague, or the last report?
- What do they already distrust (customer-submitted documents, old notes etc)? What would make them ignore a tool?
- Who decides audit-day budgets? Is prep time paid or squeezed? (Determines whether time saved is a real benefit.)

Questions I need answered:

| Area        | Questions                                                                                                                             |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| Workflow    | How long does prep take, by audit type? What do they read first, and what do they skip? Do they prep online or offline?               |
| Decisions   | What does prep feed: the audit plan, sampling, time allocation, questions? What makes a prep "good"?                                  |
| Trust       | How do they verify auditee evidence today? What would make them stop trusting a tool?                                                 |
| Data        | Where does each piece of information live? How consistent are finding descriptions across clients? What exists only in PDFs or scans? |
| Constraints | What do certification body rules allow a tool to do? What do client confidentiality agreements say? Which languages?                  |

## Assumptions in the brief I'd challenge

| Assumption                                       | Why I doubt it                                                                                       | How I would test it                                                                        |
| ------------------------------------------------ | ---------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| **Fragmented information is the core problem**   | It may be a data integration and UX problem that needs no AI                                         | Measure where prep time goes; test whether a clean structured view alone solves most of it |
| **Auditors arrive not knowing what matters**     | Experienced auditors often know; the pain may be time, not insight                                   | Compare prep by novice and experienced auditors                                            |
| **More context helps**                           | Long summaries add reading time and bury what matters                                                | Test a short brief against a full summary in the pilot                                     |
| **The system should tell auditors what matters** | ISO/IEC 17021-1 requires impartiality and professional judgment; steering creates bias and liability | Interview certification managers and technical reviewers                                   |
| **Historical data is good enough to learn from** | Quality varies by customer; finding text is inconsistent                                             | Profile data from 3 customers before choosing any ML                                       |
| Auditee documents are evidence                   | They are self-reported claims, and can be adversarial                                                | Check how auditors verify them today; treat as untrusted input                             |
| One experience fits every audit                  | Initial audits have no history; schemes and terms differ                                             | Segment research by audit type and scheme                                                  |

## Where AI creates real value

AI belongs where the input is unstructured and the answer needs reading comprehension. Everything with an exact answer stays deterministic.

- Reading unstructured text/scans to extract *claims with a verbatim quote* (e.g. "CAPA for F-2023-004 still not effective"), which then become
  structured, checkable objects.
- Surfacing **conflicts** between the structured record and what documents say.
- Proposing terminology mappings between schemes (a human approves them once; they become deterministic).
- Later: drafting audit questions from a flagged item, clearly labelled as a suggestion.

## Where I would explicitly not use AI

* **Authorization and tenant scoping.** These must be guaranteed, not probable.
* **Dates, statuses, counts, overdue and expiry checks.** Exact answers exist in the data.
* **Choosing what enters the brief.** Transparent rules are auditable and explainable.
* **Conformity decisions or audit conclusions.** These are the auditor's professional judgment.
* **Risk scores.** LLM scores are uncalibrated and hard to defend. If ever, later, with labelled outcomes and traditional ML.
* **Citation checking.** Exact string matching does it perfectly.

## Smallest useful problem to attack first: **Previous findings follow-up**

For one upcoming audit, a brief that lists each previous nonconformity with its status, corrective action and submitted evidence. Each item carries a flagged, cited assessment of whether the evidence appears to address the finding. A separate list shows missing information.

Why this slice:

* **Mandatory.** Auditors must review corrective actions on previous nonconformities during surveillance and recertification.
* **Frequent.** It happens in every surveillance and recertification audit.
* **Narrow.** Inputs are bounded: findings plus their linked evidence.
* **Evaluable.** The next audit's outcome shows whether a nonconformity was really resolved.
* **Safe failure.** A wrong assessment shows up as a labelled suggestion with sources, and the auditor still checks.

