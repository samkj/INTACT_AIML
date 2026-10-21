# 6. Production Happens

A prototype that works for ten auditors is not automatically safe to use across customers, countries and certification schemes. Before calling this a production capability, I would focus on security boundaries, traceable evidence, repeatable evaluation and reliable failure handling.

## What must change before scaling

### 1. Tenant isolation and access control

Authorization must be enforced by the backend and data-access layer, not by the prompt or the UI. Every request for a finding, document, search result or generated brief must be scoped to the user's permissions and customer. The same boundary must apply to document storage, search indexes, caches, logs and generated results.

I would add automated tests for cross-tenant access, including attempts to fetch documents through guessed IDs or search queries. I would not release if one customer's evidence could appear in another customer's results.

### 2. Reliable data and document ingestion

Customer data quality will vary. I would profile missing fields, duplicate findings, inconsistent statuses and broken evidence links during onboarding. The system should surface those limitations rather than quietly infer values.

For documents, retain the original file and store extracted text with source metadata such as document ID, page, version and date where available. Use OCR for scanned PDFs, but report unreadable or low-quality extraction clearly. “Could not read this file” must not become “no evidence exists.”

Different schemes may use different terms. I would use scheme-specific, versioned terminology mappings reviewed by domain experts. The LLM may propose a mapping, but uncertain mappings should be flagged for review rather than silently applied.

### 3. Constrain and evaluate AI behaviour

The production output should use a fixed schema and a small set of assessment labels. The model receives only evidence retrieved for the authorised audit and is instructed to treat document contents as untrusted data, not as instructions. The application validates source IDs, page references and quoted text before displaying the result.

Those checks confirm that the citation exists; they do not prove the interpretation is correct. I would maintain a reference evaluation set with clear, partial, contradictory, missing and hard-to-read evidence. Track retrieval quality, incorrect matches, unsupported assessments, abstention quality and auditor corrections. Test changes to models, prompts, OCR or retrieval against this set before releasing them, and keep model and prompt versions so results can be investigated or rolled back.

Evaluation should expand gradually across customers, languages and schemes. A good result on one customer's documents is not evidence that the system performs equally well everywhere.

### 4. Privacy and customer control

Use only data the customer has permitted for the intended purpose. Minimise the information sent to model providers, use approved services and retention settings, and document where data is processed. Do not use customer content for model training unless that customer has explicitly permitted it. Corrections can improve the test set only under the appropriate permissions and privacy controls.

### 5. Operate it as a product, not a demo

I would add monitoring for processing failures, OCR quality, latency, cost, missing citations, abstention rates and user corrections. The pipeline needs timeouts, retries where safe, versioned configuration, error handling and a rollback path. Access and important system actions should be logged without unnecessarily copying sensitive document contents into logs.

The system must degrade gracefully:

* If the LLM is unavailable, the auditor can still see structured findings and recorded statuses.
* If extraction fails, the file is marked as unreadable and can be inspected manually.
* If evidence is missing or contradictory, the system says so rather than forcing an answer.
* If an auditor reports an incorrect match, the item is marked as disputed and dependent summaries are reviewed.

I would introduce this through a controlled rollout: internal testing, a limited pilot with consenting customers, review of real errors, and then gradual expansion. I would define release gates and an owner for investigating harmful or repeated failures.

## What I would keep deterministic

| Function                                 | Approach                               | Reason                                                   |
| ------------------------------------------ | ---------------------------------------- | ---------------------------------------------------------- |
| Authorization and tenant boundaries      | Backend access control                 | Security cannot depend on model behaviour.               |
| Finding status, dates and overdue checks | Database queries and business rules    | These have structured sources of truth.                  |
| Citation and quote validation            | Programmatic checks                    | Source existence and exact text can be checked directly. |
| Evidence interpretation                  | Constrained LLM with an abstain option | Language understanding helps, but may be wrong.          |
| Final closure or conformity decision     | Auditor                                | This is a consequential professional judgement.          |

## What I would own as the Applied AI/ML Engineer

I would own the evidence-retrieval and assessment pipeline, the output schema and citation validation, the evaluation dataset and test process, and investigation of AI-related errors. I would also work with the team to set measurable quality gates and monitor performance after release. I would not make the architecture, security or product decisions alone.

* **Product:** define the target workflow, user value, scope and success criteria with auditors.
* **Design:** make source evidence, uncertainty, conflicts and human actions clear and easy to inspect.
* **Data Engineering:** provide reliable authorised access to structured records and documents, data lineage and ingestion monitoring.
* **Platform/Security/Engineering:** implement tenant isolation, permissions, secure deployment, observability, reliability and rollback.
* **Audit-domain experts:** validate scheme terminology, reference labels and whether assessments are useful in practice.

I would be comfortable calling it production-ready only when it is secure across tenants, its important claims are traceable, performance has been tested on representative cases, failures are visible and recoverable, and there is a clear owner for ongoing evaluation. The goal is not to eliminate every AI error; it is to make errors detectable, containable and unlikely to mislead an auditor into treating an unsupported claim as fact.

