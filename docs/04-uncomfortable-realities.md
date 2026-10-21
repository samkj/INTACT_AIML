# 4. Deal with the Uncomfortable Realities

My principle would be to ​**make uncertainty visible and keep critical controls deterministic**​. AI can help read and connect documents, but it must not become the source of truth for audit status, permissions, or final conformity decisions.

## Data quality and different terminology

Historical records will vary in completeness and wording. Before relying on them, I would profile a sample from several customers: missing fields, duplicate findings, inconsistent statuses, and broken links to evidence. I would show missing or inconsistent data instead of trying to guess it.

Certification schemes may use different terms for similar concepts. I would start with scheme-specific terminology and explicit mappings reviewed by a domain expert. The LLM could suggest a mapping, but it should not apply uncertain mappings silently. If the mapping is unclear, show the original terms and ask for human review.

## PDFs, scans, and images

Use text extraction for digital documents and OCR for scans or image-based PDFs. Preserve page-level references and, where possible, link back to the original page. Poor OCR quality should lower the system's ability to make an assessment and be shown to the auditor.

If a document cannot be processed, say ​**“Could not read this file”**​, not **“No evidence found.”** For the first version, I would not attempt sophisticated image interpretation unless the pilot shows that important evidence depends on information beyond readable text.

## Little or no historical data

The product should still work for an initial audit, but the experience must adapt. If there are no previous findings, say that there is no prior audit history available; do not generate a history-based assessment. The auditor can still review the current scope and submitted documents, but that is a separate workflow and not part of the first MVP.

## Tenant isolation, permissions, and privacy

This is a hard security boundary, not an AI task.

* Every request must be authorized against the user, customer, audit, and document.
* Enforce tenant scoping in the backend and data-access layer, not only in the UI or LLM prompt.
* Apply the same access checks to search indexes, retrieved passages, caches, logs, and generated results.
* Never retrieve another customer's data and hope the model will ignore it.
* Minimize sensitive information sent to model services, use approved providers and retention settings, and log access appropriately.
* Respect each customer's policy on model training. Customer content must not be used for training unless explicitly permitted.

I would test cross-tenant access explicitly, including attempts to access documents by guessing IDs and to retrieve them through search.

## Provenance and hallucination

Every material AI assessment should link to the original finding and the evidence passages used. Preserve document IDs, page numbers, version/date metadata, and exact quotes. Validate that cited sources exist and quotes match the extracted text before displaying them.

These checks validate provenance, not meaning. A real quote can still be irrelevant or misinterpreted. So the UI must distinguish:

* **Recorded status:** taken from the structured audit system;
* **Evidence observed:** what the cited document says;
* **AI assessment:** whether the material appears to address the finding;
* **Auditor decision:** the final professional judgment.

If evidence conflicts or is insufficient, the system should present the conflict or abstain rather than choose the most convenient answer. A newer document is not automatically authoritative.

## Evaluation and monitoring

Build a test set reviewed by experienced auditors, covering different customers, schemes, document formats, and difficult cases. Evaluate retrieval and assessment separately: a good model cannot reason over evidence it never retrieved.

Track unsupported assessments, wrong evidence links, missed relevant evidence, abstention quality, and auditor corrections. Review failures before changing prompts or models, and rerun the test set after changes. In a pilot, corrections should inform evaluation; they should not automatically become training data.

## Graceful degradation

The system should still be useful when parts of the pipeline fail:

* If AI is unavailable, show structured findings and statuses as usual.
* If OCR fails, identify the file as unreadable and ask the auditor to inspect it manually.
* If search finds no supporting passage, show “No supporting evidence found in the material searched,” including the search scope where practical.
* If sources conflict, show them side by side and mark the assessment unresolved.
* If there is too little evidence, return “Insufficient evidence” rather than forcing a category.

## What I would keep deterministic or constrained

| Area                                      | Approach                                | Why                                                 |
| ------------------------------------------- | ----------------------------------------- | ----------------------------------------------------- |
| Authorization and tenant isolation        | Backend access control                  | A security rule cannot be probabilistic.            |
| Finding status, dates, and overdue checks | Database queries and business rules     | These have exact sources of truth.                  |
| Citation and quote validation             | Programmatic checks                     | Source IDs and quoted text can be checked directly. |
| Evidence discovery                        | Search/retrieval with traceable results | The auditor needs to inspect what was considered.   |
| Evidence interpretation                   | Constrained LLM, with abstention        | Language understanding is useful, but can be wrong. |
| Closure or conformity decision            | Auditor                                 | It is consequential professional judgment.          |

The guiding rule is simple: **use AI to reduce the work of finding and interpreting evidence, not to hide gaps in the data or make the auditor's decision for them.**

