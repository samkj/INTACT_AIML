## What this submission contains

This repository is a product and technical design submission for an AI-assisted audit preparation workflow. It does not contain a working application or deployed AI pipeline. The proposal narrows the problem to follow-up on previous nonconformities: helping an auditor find and review submitted evidence before a surveillance or recertification audit.

The system would combine structured audit records with passages extracted from linked documents. Deterministic rules would provide statuses, dates, and access control; search would find candidate evidence; and a constrained language model could describe whether a passage appears relevant, with source references and an option to abstain. Auditors would inspect the evidence and retain responsibility for decisions about closure and conformity. The product concept emphasizes source traceability, visible uncertainty and conflicts, and a useful manual path when extraction or AI fails.

## Design documents

The documents follow the progression of the proposal:

1. [Problem framing](docs/01-problem-framing.md) — the decision problem, assumptions to test, and the initial use case.
2. [Product experience](docs/02-product-experience.md) — the auditor workflow, evidence presentation, uncertainty, and human control.
3. [AI system design](docs/03-design-ai-system.md) — a small retrieval and assessment pipeline and how to evaluate it.
4. [Uncomfortable realities](docs/04-uncomfortable-realities.md) — data quality, access boundaries, privacy, provenance, and failure handling.
5. [Proof it works](docs/05-proof_it_works.md) — a proposed pilot, reference set, measures, and decision criteria.
6. [Production](docs/06-production.md) — operational requirements, rollout, ownership, and production readiness.

## What I would do with more time

I would first validate the workflow with auditors before building the AI portion. I would observe preparation work across audit types and schemes, learn where time is actually spent, and test whether a structured evidence worklist solves enough of the problem on its own. Then I would prototype the previous-findings workflow and test it with auditors, including cases with irrelevant, partial, conflicting, missing, scanned, and unreadable evidence.

For a technical evaluation, I would assemble a permitted, representative set of historical findings and documents, have experienced auditors independently label relevant passages and evidence gaps, and review disagreements. I would measure retrieval separately from assessment, inspect every materially misleading result, and compare preparation and verification time with the current process. The proposed pilot gates in [Proof It Works](docs/05-proof_it_works.md) are hypotheses to agree with the team, not results from an experiment already run.

## What I would improve for production

Before a customer rollout, I would prioritize:

- Backend-enforced tenant isolation and authorization across records, documents, indexes, caches, and generated results.
- Reliable ingestion with page-level provenance, OCR quality indicators, and explicit unreadable-file states.
- Validated structured model output, source and quote checks, abstention, and clear separation between recorded facts, evidence, model assessments, and auditor decisions.
- A representative, auditor-reviewed evaluation set with release checks for retrieval quality, incorrect matches, unsupported claims, and abstention quality.
- Privacy controls for customer data, operational monitoring, safe retries, versioning, rollback, and a controlled pilot with owners for investigating errors.

These are proposed next steps, not implemented or verified production controls. The detailed plan and team responsibilities are in [Production](docs/06-production.md).

## Repository history

The existing commits retain the progression from problem framing through the product, AI design, risks, evaluation, and production considerations. The README is added as a summary of that work; the earlier commits are left intact so the history continues to show how the submission took shape.
