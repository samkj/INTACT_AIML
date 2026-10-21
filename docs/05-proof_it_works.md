# 5. Prove That It Works

## What I need to prove

For the first version, I would test one specific hypothesis:

> **Can we help an auditor review previous nonconformities faster by finding the relevant submitted evidence and giving a traceable assessment, without hiding missing or conflicting information?**

I would not try to prove that AI can decide whether a nonconformity is closed. That remains the auditor's decision.

## Smallest useful experiment

I would build an offline prototype for the **previous findings follow-up** workflow:

1. Load previous findings, corrective actions and their recorded statuses from structured audit data.
2. Retrieve related evidence using linked document IDs and finding references first, then text search.
3. Use an LLM to classify the retrieved evidence as ​*appears to address*​, ​*partially addresses*​, ​*does not appear to address*​, or ​*insufficient/conflicting evidence*​.
4. Require a short explanation and source references. Validate that the cited document/page exists and that the quoted text matches the extracted text.
5. Show the result beside the original finding so an auditor can verify it.

I would keep this a small, single-purpose pipeline. No fine-tuning, external web research, automated closure decisions or risk scores are needed to test the hypothesis.

## Dataset and reference answers

I would start with around **50 historical findings** from data that Intact is permitted to use for this evaluation. I would select cases with different outcomes: clear evidence, partial evidence, conflicting documents, no supporting evidence, and scanned or difficult-to-read documents. If available, I would include more than one customer or scheme, but I would not claim generalisation if the sample only represents one.

Two experienced auditors would independently review the cases and record:

* which documents or passages are relevant;
* whether the evidence appears to address the finding;
* what remains uncertain or missing.

They would discuss disagreements to create a reference set. Their judgement is the comparison point for the prototype, not an unquestionable label. The actual result of a later audit can be tracked as a useful secondary signal, but it is not perfect ground truth for whether the preparation-time assessment was reasonable.

## How I would evaluate it

I would evaluate retrieval and the LLM separately. If the system never finds the relevant document, changing the prompt will not fix the underlying problem.

| What to measure                              | Why it matters                                                                                                                   |
| ---------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| Relevant evidence retrieved                  | Checks whether the system finds the material an auditor needs.                                                                   |
| Citation and quote validity                  | Every displayed citation must resolve to a real source and quoted text. Invalid citations should be blocked, not merely counted. |
| Assessment agreement with auditors           | Checks whether the evidence interpretation is useful and reasonable.                                                             |
| Unsupported claims and incorrect matches     | These are especially dangerous because they can look convincing. Review every such failure.                                      |
| Appropriate use of “insufficient evidence” | The system must abstain when the available material does not support a conclusion.                                               |
| Preparation time and verification effort     | Measures whether the tool makes the actual workflow better, not just whether the output looks good.                              |

I would then ask 3–5 auditors to complete a small set of preparation tasks with and without the prototype. I would compare time taken, relevant evidence found, and issues missed. The test should include difficult cases, not just examples where the evidence clearly supports closure.

## Decision after the experiment

Before running the test, I would agree acceptance criteria with the product team and auditors. As an initial proposal, I would want to see roughly ​**20% less preparation time**​, with no meaningful drop in relevant evidence found or increase in missed issues. I would also require every displayed citation to pass source validation and investigate all materially misleading assessments before exposing the tool to customers.

These are proposed pilot gates, not industry benchmarks. If the system saves time by making unsupported claims or hiding uncertainty, the experiment has failed. If it is accurate but does not reduce effort, I would simplify the workflow or revisit whether AI is needed.

The outcome that would justify further investment is straightforward: **auditors can verify previous findings more quickly, can see why an item was surfaced, and are not led to trust evidence that does not support the assessment.**

