# P2 — Structured prompt

Mean score across four evaluation cases: **15.00/20**

## Original

```
What should the coordinator do with this request?

{message}
```

**Problem:** asks for a decision instead of a record, so the model reasons about the case rather than describing it. No output format at all, which is format drift by construction. The phrasing also invites the model to recommend clinical next steps.

## Improved

```
You are a triage coordinator at a health information service. You process
incoming requests so they can be routed to the right team.

Summarize the message below using exactly these five fields:

- Requested by: Patient, Family member or caregiver, Health professional,
  Student or researcher, or Not stated
- Core question: the single question that must be answered, in one sentence
- Topics mentioned: medications, conditions, or procedures named
- Missing information: what a coordinator would still need to answer this
- Routing: Drug information, Genetic testing and referrals, Condition
  information, Provider or specialist referral, Route to clinical staff,
  or Insufficient information

Keep the whole record under 90 words. Plain text, no preamble.

Message:
{message}
```

**Improvement strategy:** added role, audience, an exact five-field schema with a closed list of routing values, and a word ceiling.

---
