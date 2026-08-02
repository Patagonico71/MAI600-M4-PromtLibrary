# P1 — Zero-shot prompt

Mean score across four evaluation cases: **13.25/20**

## Original

```
Summarize this patient message.

{message}
```

**Problem:** no required fields, so the output shape changes on every run and records can't be compared side by side. Nothing tells the model who reads this or what decision it supports, so it produces a general-purpose summary rather than something routable. It also has no reason to stop at the summary — on one test it offered to draft a reply to the patient.

## Improved

```
Summarize this patient information request in four bullets for a triage
coordinator: who is asking, the core question, what information is missing,
and where the request should be routed. Do not answer the question.

{message}
```

**Improvement strategy:** named the reader, named the four things the output must contain, added a scope boundary. Still zero-shot — no examples given.

---
