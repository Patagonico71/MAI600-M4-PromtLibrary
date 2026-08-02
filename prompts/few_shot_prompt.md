# P3 — Few-shot prompt

Mean score across four evaluation cases: **13.50/20**

## Original

```
Classify this request and summarize it.

Message: {message}
```

**Problem:** "classify" with no categories given, so the model invents its own taxonomy and a different one each run. No example of depth, so the fields it does produce swing between one word and a paragraph.

## Improved

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
