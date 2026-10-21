# 3. Design the AI System

## Goal

For the first version, I would focus only on ​**follow-up of previous nonconformities**​. The system should bring together each previous finding, its current status, corrective action, and submitted evidence. AI helps the auditor assess whether the evidence *appears to address* the finding; it does not decide whether the finding is closed.

I would start with a small, inspectable pipeline rather than a complex multi-agent system.

## Proposed architecture

```
Audit database                  Submitted documents
(findings, status, dates,       (PDFs, scans, reports,
 corrective actions)             photos, notes)
       |                              |
       v                              v
Deterministic data access       Extract text / OCR
       |                              |
       |                       Split into source-linked
       |                       passages and metadata
       |                              |
       +----------+-------------------+
                  v
       Find evidence relevant to each finding
       (IDs and metadata first; text search second)
                  |
                  v
       Constrained LLM assessment
       - Does this passage appear relevant?
       - What does it say, in a short summary?
       - What exact quote supports the assessment?
                  |
                  v
       Validate output and source references
       (schema, IDs, quotes, access permissions)
                  |
                  v
       Auditor brief: status + evidence + limitations
```

## Main components and why they are needed

### 1. Structured data access

Use Intact's existing database or APIs to retrieve the audit scope, previous findings, corrective actions, statuses, dates, and links to uploaded evidence. These are the source of truth for structured facts.

Statuses, due dates, and overdue calculations should come from deterministic queries and rules, not an LLM. That avoids a generative model accidentally changing a status or calculating a date incorrectly.

### 2. Document extraction

For digital PDFs and text documents, extract the text and retain document ID, page number, section where available, upload date, and version. Use OCR for scans and image-based PDFs. Keep the original file available so the auditor can check the extracted text.

If a file cannot be read reliably, report that limitation. Do not silently treat an unreadable document as evidence that information is absent.

### 3. Find candidate evidence

For the first version, use an ordered, simple search strategy:

1. Exact references such as finding IDs, corrective-action IDs, and document links.
2. Keyword or full-text search for related wording.
3. Semantic search only where exact terms are not enough to find relevant passages.

Return a small set of candidate passages with source metadata. Retrieval finds material to inspect; it does not prove that the material supports closure.

### 4. Constrained LLM assessment

Give the model the finding description and a limited set of retrieved passages. Ask for a structured response, for example:

* `assessment`: appears to address / partially addresses / does not appear to address / insufficient evidence;
* `reason`: a short explanation tied to the finding;
* `evidence`: source IDs, page numbers, and exact supporting quotes;
* `limitations`: what remains unclear.

The prompt should tell the model to use only the supplied evidence, treat uploaded documents as untrusted content rather than instructions, and return **insufficient evidence** when the sources do not support an assessment. The model must not invent missing details or declare a finding officially closed.

### 5. Validation and presentation

Before displaying the result, validate the response against a fixed schema. Check that cited source IDs and page numbers exist, and that quoted text matches the extracted source text. These checks can catch invalid or fabricated citations, although they cannot guarantee that the interpretation is correct.

Show the result beside the original finding and evidence. Keep the database status separate from the AI assessment, and let the auditor open the original document to verify the claim.

## What I would leave out initially

I would not start with external web data, fine-tuning, autonomous agents, or an LLM-generated risk score. They add complexity without being necessary to test this narrow use case. External information could also introduce questions about relevance, authority, and licensing. I would first prove that existing audit records and submitted evidence are enough to save auditors time.

## What I would evaluate

Create a small, representative set of previous findings and documents that experienced auditors have reviewed. Compare system output with their assessment, including cases where evidence is irrelevant, incomplete, contradictory, or unreadable.

Measure:

* Whether relevant evidence is retrieved;
* Whether the assessment is supported by the cited passages;
* Incorrect matches and unsupported claims;
* How often the system appropriately says “insufficient evidence”;
* Time auditors need to verify an item.

I would inspect failures by category, not rely on one overall accuracy score. The first success criterion is not automatic closure; it is ​**faster, traceable evidence review without hiding uncertainty**​.

