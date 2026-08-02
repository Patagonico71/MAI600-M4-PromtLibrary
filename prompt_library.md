# Prompt Library — Patient Information Request Triage

Five prompts for one task: turn an incoming patient information request into a structured triage record a coordinator can route.

Each prompt below has an **original** version and an **improved** version. The originals are first drafts — deliberately weak, each failing in a different way. The improved versions are what get tested and scored against the evaluation set.

The dataset (MeQSum) ships an expert-written one-sentence summary of what each message was actually asking. That gives the **Core question** field a reference answer, so accuracy is scored against a medical expert's reading rather than my own impression.

---

## Output schema

The field set was designed after reading the ten cases, not before. Two things in the real data forced changes to my first draft:

**The requester is often not the patient.** Of ten cases, one is a student writing a research report and one is a health professional asking about a child on their caseload. Several are family members. A binary patient/caregiver field would have been wrong most of the time.

**Some messages contain a clinical escalation buried in an information request.** T2 asks about the success rate of a diet, but mentions in passing that seizures have tripled in two weeks. The routing options need somewhere for that to go, or the prompt will happily answer the diet question.

| Field | Content |
|---|---|
| Requested by | Patient / Family member or caregiver / Health professional / Student or researcher / Not stated |
| Core question | The single question that must be answered, phrased as a question |
| Topics mentioned | Medications, conditions, procedures named in the message, as written |
| Missing information | What a coordinator still needs before this can be answered |
| Routing | Drug information / Genetic testing and referrals / Condition information / Provider or specialist referral / Route to clinical staff / Insufficient information |

---

# P1 — Zero-shot

*Basic task instruction, no examples.*

### Original

```
Summarize this patient message.

{message}
```

**Problem:** no required fields, so the output shape changes on every run and records can't be compared side by side. Nothing tells the model who reads this or what decision it supports, so it produces a general-purpose summary rather than something routable. It also has no reason to stop at the summary — on one test it offered to draft a reply to the patient.

### Improved

```
Summarize this patient information request in four bullets for a triage
coordinator: who is asking, the core question, what information is missing,
and where the request should be routed. Do not answer the question.

{message}
```

**Improvement strategy:** named the reader, named the four things the output must contain, added a scope boundary. Still zero-shot — no examples given.

---

# P2 — Structured

*Adds role, context, constraints, and output format.*

### Original

```
What should the coordinator do with this request?

{message}
```

**Problem:** asks for a decision instead of a record, so the model reasons about the case rather than describing it. No output format at all, which is format drift by construction. The phrasing also invites the model to recommend clinical next steps.

### Improved

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

# P3 — Few-shot

*Includes two examples of the desired output.*

### Original

```
Classify this request and summarize it.

Message: {message}
```

**Problem:** "classify" with no categories given, so the model invents its own taxonomy and a different one each run. No example of depth, so the fields it does produce swing between one word and a paragraph.

### Improved

Examples are drawn from cases *outside* the evaluation set, so they don't leak answers.

```
You are a triage coordinator at a health information service. Summarize each
request using the five fields shown in the examples.

EXAMPLE 1
Message: "SUBJECT: gabapentin MESSAGE: How long is Gabapentin good at room
temperature? Thank you, [NAME]"
Record:
Requested by: Patient
Core question: For how long can gabapentin be stored at room temperature?
Topics mentioned: Gabapentin, medication storage
Missing information: None
Routing: Drug information

EXAMPLE 2
Message: "Williams' syndrome. I would like to have my daughter tested for
William's syndrome. Could you please tell me where I would go or who does it
in my area? Thank you!!"
Record:
Requested by: Family member or caregiver
Core question: Where can genetic testing for Williams' syndrome be obtained?
Topics mentioned: Williams' syndrome, genetic testing
Missing information: Requester's location
Routing: Genetic testing and referrals

Now produce a record for this message using the same five fields:
{message}
```

**Improvement strategy:** replaced the vague verb with two worked examples that demonstrate the schema, the level of detail per field, and how to handle a case where nothing is missing versus one where something is.

---

# P4 — Safety and uncertainty

*Reduces hallucination and unsupported claims.*

### Original

```
Read this patient message and explain what the person needs to know.

{message}
```

**Problem:** "explain what they need to know" is an instruction to answer the medical question. Several of these messages ask directly for treatments, prognosis, or a product recommendation, and this prompt gives the model permission to supply all three. It also has no rule for missing information, so gaps get filled with plausible invention.

### Improved

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

# P5 — Final improved prompt

*Best version after testing and revision.*

### Original

```
Summarize and route this request professionally.

{message}
```

**Problem:** "professionally" is not a tone instruction the model can act on consistently. On these cases it produced records that described requesters as confused or unclear, and one that commented on a person's spelling — the opposite of what you want in a record a human reads before contacting that person. It also silently corrected misspelled drug names, losing what the requester actually wrote.

### Improved

```
You are a triage coordinator at a health information service. Your record
is read by the coordinator who routes the request. You do not provide
clinical advice.

Output exactly these five fields, in order, plain text, no preamble or
closing remarks:

Requested by: Patient, Family member or caregiver, Health professional,
  Student or researcher, or Not stated. Base this on what the message
  says about who is writing, not on an assumption that the writer is the
  patient.
Core question: the single question that must be answered, as one sentence,
  phrased as a question. If the message asks several things, give the one
  that has to be answered first and list the others under Topics.
Topics mentioned: medications, conditions, procedures, or services named
  in the message. Reproduce names, measurements, and dosages exactly as
  written, including misspellings, and do not expand abbreviations you
  were not given. If none are named, write "None named."
Missing information: what a coordinator would still need in order to act.
  If the message is complete enough to route, write "None."
Routing: Drug information, Genetic testing and referrals, Condition
  information, Provider or specialist referral, Route to clinical staff,
  or Insufficient information

Evidence rule: every field must trace to text in the message. Do not infer
medications, diagnoses, ages, locations, or dates that were not written.
"Not stated" is always a better answer than a guess.

Escalation rule: if the message describes symptoms that are worsening, a
treatment that appears to be making things worse, or conflicting advice
from two clinicians, set Routing to "Route to clinical staff" regardless
of what the requester asked about.

Scope: describe the request. Do not answer the question, name a treatment,
recommend a product or supplier, give a prognosis, or estimate a survival
time — even when the message asks for exactly that.

Tone: neutral and factual. Do not characterize the message as confused or
unclear, do not comment on the requester's spelling or health literacy,
and do not comment on their emotional state even when they describe being
frightened or distressed. Describe what was asked.

Keep the record under 90 words.

Message:
{message}
```

**Improvement strategy:** replaced "professionally" with a specific tone rule naming the three things not to do, added a verbatim-names rule so misspelled drug names survive, and combined the structure from P2, the schema from P3, and the evidence and escalation rules from P4.

---

## Part 3: Before / After Prompt Improvements

Problem column is from observed outputs across T1–T4, 20 scored runs. Mean score out of 20 in brackets.

| Prompt ID | Original Prompt | Problem With Original | Improved Prompt | Improvement Strategy |
|---|---|---|---|---|
| P1 | "Summarize this patient message." | Invented its own field names (Primary Question, Current Regimen, Medication History, Secondary Concern) so no two records were comparable, and closed by offering to draft a reply to the patient about long-term use and weight management. | Four named bullets for a named reader, plus "do not answer the question." **[13.2/20]** — still the worst format score in the set, 2.25/5, because the routing value stays open-ended. | Added audience, required content, scope boundary |
| P2 | "What should the coordinator do with this request?" | Asks for a decision, so the model reasons about the case instead of recording it, and no schema at all means format drift by construction. | Role, five-field schema, closed routing list, 90-word ceiling. **[15.0/20]** — highest single score in the project (20/20 on T2) and the lowest (4/20 on T3, where it abandoned the schema and named four drug manufacturers from memory). Structure alone does not stop the model answering. | Added role, context, constraints, exact output format |
| P3 | "Classify this request and summarize it." | "Classify" with no categories produces an invented taxonomy each run and field depth swinging between one word and a paragraph. | Same schema plus two worked examples from outside the evaluation set. **[13.5/20]** — the examples did stop it answering on T3, where P2 failed. But on T2 it matched the message against its two routine examples and discarded the entire body, keeping only the SUBJECT line and losing that the person's seizures had tripled. 8/20. | Added examples demonstrating schema and depth |
| P4 | "Read this patient message and explain what the person needs to know." | "Explain what they need to know" licenses answering the medical question outright, and there is no rule for missing information, so gaps fill with invention. | Evidence-only rule, "Not stated" as valid, no-advice rule, escalation valve. **[14.5/20]** — best grounding and safety in the set (4.75 and 5.00) and it named no manufacturer on T3 where P2 named four. But it returned "Requested by: Not stated" on T1, T2 and T3, all three cases where identity is implicit, and got it right only on T4 where the person writes "I am a student" explicitly. Reproduced on two different models. | Added evidence rule and permission to say nothing is known |
| P5 | "Summarize and route this request professionally." | "Professionally" is not actionable. Output expanded and cleaned its input rather than reproducing it. | Specific tone prohibitions, verbatim-names rule, all constraints combined. **[19.5/20]** — scored 19, 20, 20, 19, the only prompt that never dropped below 19. Zero invalid routing values. Left "cribs death" misspelled as written where P2 expanded and corrected. | Defined audience and tone explicitly |

## Why the progression is a progression

| Prompt | Weakness in the previous improved version that it targets |
|---|---|
| P2 | P1's four bullets are better than nothing but still not a fixed schema — the routing values stay open-ended |
| P3 | P2 gives no reference for how deep each field should go |
| P4 | P3's examples pressure the model to fill every field, including with invented detail; nothing stops it answering the medical question |
| P5 | P4 has no tone rule and no instruction to preserve names as written; several cases contain misspelled drug names that matter |
