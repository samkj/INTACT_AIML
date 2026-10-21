# 2. Design the Product Experience

## Product idea: Previous Findings Follow-up

For the first version, I would focus on helping auditors review previous findings (nonconformities) before a surveillance or recertification audit.

The product brings the previous finding, its recorded status, corrective action, and submitted evidence into one place. It highlights items that may need attention and explains why, so the auditor spends less time searching and more time verifying.

The key principle is simple: the system finds and organises evidence; the auditor judges whether it is sufficient. It must not automatically close a finding or decide whether an organisation conforms.

## 2.1 The auditor journey

```
Open upcoming audit
        ↓
Review list of previous findings
        ↓
Open a finding to check sources and gaps
        ↓
Decide: request evidence, add an audit question,
        or leave it for verification on site
```

The first screen should be a short worklist, not a long AI-generated report. For each finding, show:

* Previous finding and recorded status
* Corrective action, if available
* Evidence found in submitted documents
* Any missing or conflicting information
* A preparation signal explaining why the item needs attention (the suggested next step appears when the finding is opened, see 2.2)

### Example worklist

| Previous finding                        | What the system found                                                                                             | Preparation signal                                 |
| ----------------------------------------- | ------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| NC-014: Training records incomplete     | A training matrix was uploaded, but records for the affected employees were not found in the material searched.   | Evidence may be incomplete                         |
| NC-021: Corrective action effectiveness | A later report says the action was implemented, but the previous audit requires its effectiveness to be verified. | Needs verification                                 |
| NC-027: Document control                | The submitted procedure has a newer revision date than the one referenced in the previous audit.                  | Needs verification (confirm which version applies) |

These are fictional examples. The signals help the auditor prioritise preparation; they are not compliance ratings or conclusions.

## 2.2 Opening a finding: show evidence, not just an answer

When the auditor opens an item, they should see the original finding, recorded status, relevant evidence, and a short explanation of the system's assessment. Citations should open the source document at the relevant page or show the corresponding structured record.

### Example: NC-021, Corrective action effectiveness

**Recorded status:** Open in the audit system

**Sources found:**

* Previous\_Audit\_Report.pdf, page 18: "Effectiveness to be verified."
* Operations\_Update.pdf, page 6: "Corrective action CA-104 has been implemented."
* Effectiveness-check record: not found in the connected material searched.

**Why it is flagged:** The update appears related to the finding, but it describes implementation. The available material does not show whether the action was effective.

**Suggested next step:** Request the effectiveness-check record or add a question to verify it during the audit.

The auditor can request more evidence, add a question to the audit plan, mark a suggested evidence link as unrelated, or leave the item for on-site verification.

## 2.3 How the product communicates uncertainty

**Sources and evidence.** Every important factual statement should link to a source: an audit record, document page, excerpt, date, or version where available. The interface should distinguish what a document says from what the AI infers. A submitted document shows that a statement was made; it does not automatically prove that the underlying activity happened.

**Confidence or uncertainty.** I would use clear labels instead of a single percentage that may look more precise than it is:

* **Relevant source found:** A potentially relevant source was identified. This does not mean the finding is resolved.
* **Needs verification:** The source may be relevant, but the auditor must confirm its meaning or applicability.
* **Evidence may be incomplete:** The material found does not address part of the finding.
* **Conflicting information:** Sources or recorded statuses disagree.
* **Unable to assess:** A document could not be read reliably, or there is not enough information.

These labels describe the available evidence, not whether the organisation is compliant.

**Missing information.** The product must say "not found in the material searched," not "does not exist." It should explain which connected sources were searched and show when a document could not be accessed or read. The auditor can then request it before the audit or plan to check it on site.

**Conflicting information.** Show conflicting sources side by side with their dates and statuses. Do not automatically trust the newest document or silently merge the statements. For example, the audit system may record a corrective action as closed while a later report describes the same action as still in progress, or two documents may give different revision dates for the same procedure. The product should explain the difference and let the auditor decide what further evidence is needed.

**Facts versus recommendations.** Keep these clearly separate:

* **Recorded status:** Taken from the audit system.
* **Source statement:** What a document says.
* **AI assessment:** A possible link, gap, or conflict identified between the sources.
* **Suggested next step:** An optional recommendation.
* **Auditor decision:** The human's own conclusion or action.

**Why the result appeared.** Each flagged item should have a short explanation that can be checked, for example: "Flagged because the previous finding requires an effectiveness check, the new report only describes implementation, and no effectiveness-check record was found in the connected material searched." The explanation should point to the sources and limitations, not claim to reveal the model's hidden reasoning.

## 2.4 What happens when the AI is wrong?

Suppose the system links Operations\_Update.pdf to NC-021 because both mention "CA-104", but the document actually refers to a different action at another site.

1. The auditor opens the highlighted excerpt, notices that it refers to another site, and chooses "This evidence is unrelated."
2. The system removes the link from supporting evidence and marks the suggestion as corrected. It must not keep presenting the link as verified.
3. The item's preparation signal is recalculated without that document. Here NC-021 would now show "Evidence may be incomplete", because no implementation or effectiveness evidence remains.
4. The auditor chooses what to do next: keep the link removed, flag the item for review, or link the correct evidence.
5. The correction is recorded with the original link, reason if provided, user action, and timestamp. This keeps the history traceable.
6. Related summaries are checked. If other suggestions relied on the wrong link, the system updates them or flags them for review.

The product should confirm: "Correction recorded. This document is no longer treated as evidence for NC-021." Correcting an AI link does not itself change the finding's recorded status. With the right permissions, this case can also be used as a test example to prevent similar errors. Customer data should not automatically be used for model training.

## 2.5 Human control

The system helps the auditor locate and compare evidence, but the auditor remains responsible for deciding what it means. Recorded statuses and dates should come from the audit system, not be generated by an LLM. The AI should not close findings or make conformity decisions. The auditor's decisions must remain separate from AI suggestions, and the workflow should still work manually when evidence is unavailable or the system cannot assess it reliably.

