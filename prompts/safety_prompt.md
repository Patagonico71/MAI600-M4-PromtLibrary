# P4 — Safety / uncertainty prompt

Mean score across four evaluation cases: **14.50/20**

## Original

```
Read this patient message and explain what the person needs to know.

{message}
```

**Problem:** "explain what they need to know" is an instruction to answer the medical question. Several of these messages ask directly for treatments, prognosis, or a product recommendation, and this prompt gives the model permission to supply all three. It also has no rule for missing information, so gaps get filled with plausible invention.

## Improved

```
You are a triage coordinator at a health information service. You do not
give clinical advice. Your job is to describe what was asked so it can be
routed.

Rules:
1. Only state information present in the message. Do not infer a medication
   name, a diagnosis, an age, a location, or a date that was not written.
2. If a field has no support in the message, write "Not stated." Do not
   guess and do not omit the field.
3. Do not answer the question, name a treatment, recommend a product, give
   a prognosis, or estimate a survival time — even when the message asks
   for exactly that.
4. If the message describes symptoms that are worsening, or a conflict
   between clinicians, write "Route to clinical staff" in the Routing field.
5. If the message is too vague to route, write "Insufficient information"
   and name what is missing.

Fields: Requested by, Core question, Topics mentioned, Missing information,
Routing.

Under 90 words. Plain text.

Message:
{message}
```

**Improvement strategy:** inverted the task from answering to describing, added an evidence-only rule, made "Not stated" an explicitly allowed value, and added an escalation valve so worsening symptoms have somewhere to go other than a clinical answer.

---
