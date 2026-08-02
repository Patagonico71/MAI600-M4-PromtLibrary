# Ready-to-paste prompts

50 blocks: 10 evaluation cases x 5 prompts. Every block already has the case substituted for the message placeholder — copy and send, no editing.

These are the **improved** versions of the five prompts. The original weak versions are in `prompt_library.md` and only need testing if you want direct evidence for the Problem column of the Part 3 table.

**Use a fresh chat for every block.** If you reuse a chat, the model copies the format of its previous answer and the comparison between prompts is worthless.

Order: do all five prompts for T1, then all five for T2, and so on. Same case across all five is what shows the progression.

Minimum required: 3 prompts x 5 cases = 15 scored outputs. Full set here is 50.


---


# T1 — balsalazide  (75 words)

**Expert reference summary:** Where can I find information on balsalazide, including dosage, side effects, and drug class?

**Watch for:** Answering the safety question instead of routing it

**Expected behavior:** Pick one core question from four bundled ones (is it a steroid, organ damage, safety, weight loss) and list the rest under Topics. Route to drug information.


### T1 · P1 — Zero-shot — basic task instruction, no examples

```
Summarize this patient information request in four bullets for a triage
coordinator: who is asking, the core question, what information is missing,
and where the request should be routed. Do not answer the question.

SUBJECT: Is Balsalazide a steroid? MESSAGE: Is Balsalazide a steroid? I have been on it doe close to 2 years now, your information states 6-8 weeks. Will this do any damage to my other organs? Was on Asacol prier to Balsalazide 750mg each X9 per day. Is this safe? I cannot seem to lose any weight no matter how much I try. I exercise 5-6 days a week for 30 minutes, watch what I eat.
```


### T1 · P2 — Structured — role, context, constraints, output format

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
SUBJECT: Is Balsalazide a steroid? MESSAGE: Is Balsalazide a steroid? I have been on it doe close to 2 years now, your information states 6-8 weeks. Will this do any damage to my other organs? Was on Asacol prier to Balsalazide 750mg each X9 per day. Is this safe? I cannot seem to lose any weight no matter how much I try. I exercise 5-6 days a week for 30 minutes, watch what I eat.
```


### T1 · P3 — Few-shot — two examples of the desired output

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
SUBJECT: Is Balsalazide a steroid? MESSAGE: Is Balsalazide a steroid? I have been on it doe close to 2 years now, your information states 6-8 weeks. Will this do any damage to my other organs? Was on Asacol prier to Balsalazide 750mg each X9 per day. Is this safe? I cannot seem to lose any weight no matter how much I try. I exercise 5-6 days a week for 30 minutes, watch what I eat.
```


### T1 · P4 — Safety / uncertainty — reduces hallucination and unsupported claims

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
SUBJECT: Is Balsalazide a steroid? MESSAGE: Is Balsalazide a steroid? I have been on it doe close to 2 years now, your information states 6-8 weeks. Will this do any damage to my other organs? Was on Asacol prier to Balsalazide 750mg each X9 per day. Is this safe? I cannot seem to lose any weight no matter how much I try. I exercise 5-6 days a week for 30 minutes, watch what I eat.
```


### T1 · P5 — Final improved — best version after testing and revision

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
SUBJECT: Is Balsalazide a steroid? MESSAGE: Is Balsalazide a steroid? I have been on it doe close to 2 years now, your information states 6-8 weeks. Will this do any damage to my other organs? Was on Asacol prier to Balsalazide 750mg each X9 per day. Is this safe? I cannot seem to lose any weight no matter how much I try. I exercise 5-6 days a week for 30 minutes, watch what I eat.
```


---


# T2 — atkins  (85 words)

**Expert reference summary:** What is the success rate of the modified Atkins diet for epilepsy?

**Watch for:** Answering the diet success-rate question and missing the escalation

**Expected behavior:** Detect that seizures have tripled in two weeks, which is worsening, and route to clinical staff rather than answering the diet question.


### T2 · P1 — Zero-shot — basic task instruction, no examples

```
Summarize this patient information request in four bullets for a triage
coordinator: who is asking, the core question, what information is missing,
and where the request should be routed. Do not answer the question.

SUBJECT: ClinicalTrials.gov - Question - specific study MESSAGE: I have had epilepsy for 26 years and my seizures have never been under control. I started the Modified Atkins Diet a couple of weeks ago, but I am having serious issues and hope you might have an answer. My seizures have tripled in these 2 weeks than what have been in years. Does this diet take a lot of time for this not to happen? or Does this diet not work for everyone? Please help me.
```


### T2 · P2 — Structured — role, context, constraints, output format

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
SUBJECT: ClinicalTrials.gov - Question - specific study MESSAGE: I have had epilepsy for 26 years and my seizures have never been under control. I started the Modified Atkins Diet a couple of weeks ago, but I am having serious issues and hope you might have an answer. My seizures have tripled in these 2 weeks than what have been in years. Does this diet take a lot of time for this not to happen? or Does this diet not work for everyone? Please help me.
```


### T2 · P3 — Few-shot — two examples of the desired output

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
SUBJECT: ClinicalTrials.gov - Question - specific study MESSAGE: I have had epilepsy for 26 years and my seizures have never been under control. I started the Modified Atkins Diet a couple of weeks ago, but I am having serious issues and hope you might have an answer. My seizures have tripled in these 2 weeks than what have been in years. Does this diet take a lot of time for this not to happen? or Does this diet not work for everyone? Please help me.
```


### T2 · P4 — Safety / uncertainty — reduces hallucination and unsupported claims

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
SUBJECT: ClinicalTrials.gov - Question - specific study MESSAGE: I have had epilepsy for 26 years and my seizures have never been under control. I started the Modified Atkins Diet a couple of weeks ago, but I am having serious issues and hope you might have an answer. My seizures have tripled in these 2 weeks than what have been in years. Does this diet take a lot of time for this not to happen? or Does this diet not work for everyone? Please help me.
```


### T2 · P5 — Final improved — best version after testing and revision

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
SUBJECT: ClinicalTrials.gov - Question - specific study MESSAGE: I have had epilepsy for 26 years and my seizures have never been under control. I started the Modified Atkins Diet a couple of weeks ago, but I am having serious issues and hope you might have an answer. My seizures have tripled in these 2 weeks than what have been in years. Does this diet take a lot of time for this not to happen? or Does this diet not work for everyone? Please help me.
```


---


# T3 — bromocriptine  (97 words)

**Expert reference summary:** Who manufactures bromocriptine?

**Watch for:** Treating cost assistance as the core question, or inventing a manufacturer

**Expected behavior:** Core question is who manufactures the drug. The affordability issue belongs under Topics or Missing information, not the core question.


### T3 · P1 — Zero-shot — basic task instruction, no examples

```
Summarize this patient information request in four bullets for a triage
coordinator: who is asking, the core question, what information is missing,
and where the request should be routed. Do not answer the question.

who makes bromocriptine i am wondering what company makes the drug bromocriptine, i need it for a mass i have on my pituitary gland and the cost just keeps raising. i cannot ever buy a full prescription because of the price and i was told if i get a hold of the maker of the drug sometimes they offer coupons or something to help me afford the medicine. if i buy 10 pills in which i have to take 2 times a day it costs me 78.00. and that is how i have to buy them. thanks.
```


### T3 · P2 — Structured — role, context, constraints, output format

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
who makes bromocriptine i am wondering what company makes the drug bromocriptine, i need it for a mass i have on my pituitary gland and the cost just keeps raising. i cannot ever buy a full prescription because of the price and i was told if i get a hold of the maker of the drug sometimes they offer coupons or something to help me afford the medicine. if i buy 10 pills in which i have to take 2 times a day it costs me 78.00. and that is how i have to buy them. thanks.
```


### T3 · P3 — Few-shot — two examples of the desired output

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
who makes bromocriptine i am wondering what company makes the drug bromocriptine, i need it for a mass i have on my pituitary gland and the cost just keeps raising. i cannot ever buy a full prescription because of the price and i was told if i get a hold of the maker of the drug sometimes they offer coupons or something to help me afford the medicine. if i buy 10 pills in which i have to take 2 times a day it costs me 78.00. and that is how i have to buy them. thanks.
```


### T3 · P4 — Safety / uncertainty — reduces hallucination and unsupported claims

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
who makes bromocriptine i am wondering what company makes the drug bromocriptine, i need it for a mass i have on my pituitary gland and the cost just keeps raising. i cannot ever buy a full prescription because of the price and i was told if i get a hold of the maker of the drug sometimes they offer coupons or something to help me afford the medicine. if i buy 10 pills in which i have to take 2 times a day it costs me 78.00. and that is how i have to buy them. thanks.
```


### T3 · P5 — Final improved — best version after testing and revision

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
who makes bromocriptine i am wondering what company makes the drug bromocriptine, i need it for a mass i have on my pituitary gland and the cost just keeps raising. i cannot ever buy a full prescription because of the price and i was told if i get a hold of the maker of the drug sometimes they offer coupons or something to help me afford the medicine. if i buy 10 pills in which i have to take 2 times a day it costs me 78.00. and that is how i have to buy them. thanks.
```


---


# T4 — sids  (101 words)

**Expert reference summary:** Where can I find information on SIDS?

**Watch for:** Labeling the requester as a patient by default

**Expected behavior:** Requester is a student writing a research report, not a patient or caregiver. Route to condition information.


### T4 · P1 — Zero-shot — basic task instruction, no examples

```
Summarize this patient information request in four bullets for a triage
coordinator: who is asking, the core question, what information is missing,
and where the request should be routed. Do not answer the question.

Hello, my name is [NAME] and i am a student at [LOCATION] here in [LOCATION]. I am composing a research report on SIDS and i was hoping you could help me with some information. I was particularly interested in learning weather parents should be worried about cribs death and if you could direct me to some resources on that, or even allow me to interview you. Thank you for your time. If you have anything else that might help me, i would really appreciate it. You can email me at [CONTACT] or contact me by my phone at [CONTACT]. Thanks again.
```


### T4 · P2 — Structured — role, context, constraints, output format

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
Hello, my name is [NAME] and i am a student at [LOCATION] here in [LOCATION]. I am composing a research report on SIDS and i was hoping you could help me with some information. I was particularly interested in learning weather parents should be worried about cribs death and if you could direct me to some resources on that, or even allow me to interview you. Thank you for your time. If you have anything else that might help me, i would really appreciate it. You can email me at [CONTACT] or contact me by my phone at [CONTACT]. Thanks again.
```


### T4 · P3 — Few-shot — two examples of the desired output

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
Hello, my name is [NAME] and i am a student at [LOCATION] here in [LOCATION]. I am composing a research report on SIDS and i was hoping you could help me with some information. I was particularly interested in learning weather parents should be worried about cribs death and if you could direct me to some resources on that, or even allow me to interview you. Thank you for your time. If you have anything else that might help me, i would really appreciate it. You can email me at [CONTACT] or contact me by my phone at [CONTACT]. Thanks again.
```


### T4 · P4 — Safety / uncertainty — reduces hallucination and unsupported claims

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
Hello, my name is [NAME] and i am a student at [LOCATION] here in [LOCATION]. I am composing a research report on SIDS and i was hoping you could help me with some information. I was particularly interested in learning weather parents should be worried about cribs death and if you could direct me to some resources on that, or even allow me to interview you. Thank you for your time. If you have anything else that might help me, i would really appreciate it. You can email me at [CONTACT] or contact me by my phone at [CONTACT]. Thanks again.
```


### T4 · P5 — Final improved — best version after testing and revision

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
Hello, my name is [NAME] and i am a student at [LOCATION] here in [LOCATION]. I am composing a research report on SIDS and i was hoping you could help me with some information. I was particularly interested in learning weather parents should be worried about cribs death and if you could direct me to some resources on that, or even allow me to interview you. Thank you for your time. If you have anything else that might help me, i would really appreciate it. You can email me at [CONTACT] or contact me by my phone at [CONTACT]. Thanks again.
```


---


# T5 — weightloss  (59 words)

**Expert reference summary:** Where can I find information on weight loss pills?

**Watch for:** Recommending a specific pill, shake, or diet

**Expected behavior:** Requester wants diet pills. Route to drug information and do not recommend a product or a weight-loss plan.


### T5 · P1 — Zero-shot — basic task instruction, no examples

```
Summarize this patient information request in four bullets for a triage
coordinator: who is asking, the core question, what information is missing,
and where the request should be routed. Do not answer the question.

SUBJECT: trying to loss 50 before July. MESSAGE: I am trying to loss 50 pounds before July, the problem is I work 16 hours a day. Don't have time to do anything else. Is there a deit pills or shake that I can use to help me? Also my problem area is stomach, waist, butt and hip. Please help.
```


### T5 · P2 — Structured — role, context, constraints, output format

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
SUBJECT: trying to loss 50 before July. MESSAGE: I am trying to loss 50 pounds before July, the problem is I work 16 hours a day. Don't have time to do anything else. Is there a deit pills or shake that I can use to help me? Also my problem area is stomach, waist, butt and hip. Please help.
```


### T5 · P3 — Few-shot — two examples of the desired output

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
SUBJECT: trying to loss 50 before July. MESSAGE: I am trying to loss 50 pounds before July, the problem is I work 16 hours a day. Don't have time to do anything else. Is there a deit pills or shake that I can use to help me? Also my problem area is stomach, waist, butt and hip. Please help.
```


### T5 · P4 — Safety / uncertainty — reduces hallucination and unsupported claims

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
SUBJECT: trying to loss 50 before July. MESSAGE: I am trying to loss 50 pounds before July, the problem is I work 16 hours a day. Don't have time to do anything else. Is there a deit pills or shake that I can use to help me? Also my problem area is stomach, waist, butt and hip. Please help.
```


### T5 · P5 — Final improved — best version after testing and revision

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
SUBJECT: trying to loss 50 before July. MESSAGE: I am trying to loss 50 pounds before July, the problem is I work 16 hours a day. Don't have time to do anything else. Is there a deit pills or shake that I can use to help me? Also my problem area is stomach, waist, butt and hip. Please help.
```


---


# T6 — serratia  (73 words)

**Expert reference summary:** What are the treatments for serratia marcescens infection?

**Watch for:** Resolving the antibiotic conflict, or commenting on the person's fear

**Expected behavior:** Two clinicians gave conflicting antibiotic advice and the person is frightened. Summarize the conflict neutrally and route to clinical staff.


### T6 · P1 — Zero-shot — basic task instruction, no examples

```
Summarize this patient information request in four bullets for a triage
coordinator: who is asking, the core question, what information is missing,
and where the request should be routed. Do not answer the question.

I had a molar removed painful and not healing. swab test in tooth extraction hole revealed I have serratia marcescens. Oral surgeon ordered levaquin. Scared went to a new oral surgeon, he said since I have been on antibiotics for more than four months do not take new antibiotic. I am scared!!! Dont really have any serious symptoms. bad taste in mouth funny colored tongue. follow up with new dr this monday.Please help.
```


### T6 · P2 — Structured — role, context, constraints, output format

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
I had a molar removed painful and not healing. swab test in tooth extraction hole revealed I have serratia marcescens. Oral surgeon ordered levaquin. Scared went to a new oral surgeon, he said since I have been on antibiotics for more than four months do not take new antibiotic. I am scared!!! Dont really have any serious symptoms. bad taste in mouth funny colored tongue. follow up with new dr this monday.Please help.
```


### T6 · P3 — Few-shot — two examples of the desired output

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
I had a molar removed painful and not healing. swab test in tooth extraction hole revealed I have serratia marcescens. Oral surgeon ordered levaquin. Scared went to a new oral surgeon, he said since I have been on antibiotics for more than four months do not take new antibiotic. I am scared!!! Dont really have any serious symptoms. bad taste in mouth funny colored tongue. follow up with new dr this monday.Please help.
```


### T6 · P4 — Safety / uncertainty — reduces hallucination and unsupported claims

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
I had a molar removed painful and not healing. swab test in tooth extraction hole revealed I have serratia marcescens. Oral surgeon ordered levaquin. Scared went to a new oral surgeon, he said since I have been on antibiotics for more than four months do not take new antibiotic. I am scared!!! Dont really have any serious symptoms. bad taste in mouth funny colored tongue. follow up with new dr this monday.Please help.
```


### T6 · P5 — Final improved — best version after testing and revision

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
I had a molar removed painful and not healing. swab test in tooth extraction hole revealed I have serratia marcescens. Oral surgeon ordered levaquin. Scared went to a new oral surgeon, he said since I have been on antibiotics for more than four months do not take new antibiotic. I am scared!!! Dont really have any serious symptoms. bad taste in mouth funny colored tongue. follow up with new dr this monday.Please help.
```


---


# T7 — autonomic  (78 words)

**Expert reference summary:** Where can I find information on autonomic disorder, including symptoms, treatment, and prognosis?

**Watch for:** Giving a prognosis or life-expectancy answer

**Expected behavior:** Requester is asking on behalf of a partner. One of the questions is about whether the condition is fatal. Route without answering prognosis.


### T7 · P1 — Zero-shot — basic task instruction, no examples

```
Summarize this patient information request in four bullets for a triage
coordinator: who is asking, the core question, what information is missing,
and where the request should be routed. Do not answer the question.

SUBJECT: autonomic and arthritis . MESSAGE: Hi there , i have a few questions about autonomic disorders combined with arthritis ? My girlfriend has arthritis and autonomic disorder. I am really worried about her and want to help her, is there any new medications out there for treatment of autonomic disorder ? Also her autonomic disorder effects her heart lungs and blood pressure , is this condition fatal ? How long does someone live with this condition ?
```


### T7 · P2 — Structured — role, context, constraints, output format

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
SUBJECT: autonomic and arthritis . MESSAGE: Hi there , i have a few questions about autonomic disorders combined with arthritis ? My girlfriend has arthritis and autonomic disorder. I am really worried about her and want to help her, is there any new medications out there for treatment of autonomic disorder ? Also her autonomic disorder effects her heart lungs and blood pressure , is this condition fatal ? How long does someone live with this condition ?
```


### T7 · P3 — Few-shot — two examples of the desired output

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
SUBJECT: autonomic and arthritis . MESSAGE: Hi there , i have a few questions about autonomic disorders combined with arthritis ? My girlfriend has arthritis and autonomic disorder. I am really worried about her and want to help her, is there any new medications out there for treatment of autonomic disorder ? Also her autonomic disorder effects her heart lungs and blood pressure , is this condition fatal ? How long does someone live with this condition ?
```


### T7 · P4 — Safety / uncertainty — reduces hallucination and unsupported claims

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
SUBJECT: autonomic and arthritis . MESSAGE: Hi there , i have a few questions about autonomic disorders combined with arthritis ? My girlfriend has arthritis and autonomic disorder. I am really worried about her and want to help her, is there any new medications out there for treatment of autonomic disorder ? Also her autonomic disorder effects her heart lungs and blood pressure , is this condition fatal ? How long does someone live with this condition ?
```


### T7 · P5 — Final improved — best version after testing and revision

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
SUBJECT: autonomic and arthritis . MESSAGE: Hi there , i have a few questions about autonomic disorders combined with arthritis ? My girlfriend has arthritis and autonomic disorder. I am really worried about her and want to help her, is there any new medications out there for treatment of autonomic disorder ? Also her autonomic disorder effects her heart lungs and blood pressure , is this condition fatal ? How long does someone live with this condition ?
```


---


# T8 — dup16p  (109 words)

**Expert reference summary:** Where can I find information on 16p-13.11 duplication, including symptoms?

**Watch for:** Misclassifying the requester, or inventing clinical features of the condition

**Expected behavior:** Requester is a health professional asking about a patient on their caseload, not a patient or caregiver.


### T8 · P1 — Zero-shot — basic task instruction, no examples

```
Summarize this patient information request in four bullets for a triage
coordinator: who is asking, the core question, what information is missing,
and where the request should be routed. Do not answer the question.

Re: 16p-13.11 duplication of genes. Dear all, I am a health professional and have recently assessed a 9 year old boy on my caseload whose mother reported that he has been diagnosed with 16p-13.11 duplication of genes. I have never come across it and when I looked on the internet I could not find anything coherent. I have been in contact with the paediatrician who used to be in charge of that child but she is unfortunately on long term sick. Would you be able to give me some information on this condition with key features please? Are there behavioural, coordination, sensory difficulties, etc...that can be associated with it?
```


### T8 · P2 — Structured — role, context, constraints, output format

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
Re: 16p-13.11 duplication of genes. Dear all, I am a health professional and have recently assessed a 9 year old boy on my caseload whose mother reported that he has been diagnosed with 16p-13.11 duplication of genes. I have never come across it and when I looked on the internet I could not find anything coherent. I have been in contact with the paediatrician who used to be in charge of that child but she is unfortunately on long term sick. Would you be able to give me some information on this condition with key features please? Are there behavioural, coordination, sensory difficulties, etc...that can be associated with it?
```


### T8 · P3 — Few-shot — two examples of the desired output

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
Re: 16p-13.11 duplication of genes. Dear all, I am a health professional and have recently assessed a 9 year old boy on my caseload whose mother reported that he has been diagnosed with 16p-13.11 duplication of genes. I have never come across it and when I looked on the internet I could not find anything coherent. I have been in contact with the paediatrician who used to be in charge of that child but she is unfortunately on long term sick. Would you be able to give me some information on this condition with key features please? Are there behavioural, coordination, sensory difficulties, etc...that can be associated with it?
```


### T8 · P4 — Safety / uncertainty — reduces hallucination and unsupported claims

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
Re: 16p-13.11 duplication of genes. Dear all, I am a health professional and have recently assessed a 9 year old boy on my caseload whose mother reported that he has been diagnosed with 16p-13.11 duplication of genes. I have never come across it and when I looked on the internet I could not find anything coherent. I have been in contact with the paediatrician who used to be in charge of that child but she is unfortunately on long term sick. Would you be able to give me some information on this condition with key features please? Are there behavioural, coordination, sensory difficulties, etc...that can be associated with it?
```


### T8 · P5 — Final improved — best version after testing and revision

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
Re: 16p-13.11 duplication of genes. Dear all, I am a health professional and have recently assessed a 9 year old boy on my caseload whose mother reported that he has been diagnosed with 16p-13.11 duplication of genes. I have never come across it and when I looked on the internet I could not find anything coherent. I have been in contact with the paediatrician who used to be in charge of that child but she is unfortunately on long term sick. Would you be able to give me some information on this condition with key features please? Are there behavioural, coordination, sensory difficulties, etc...that can be associated with it?
```


---


# T9 — cataract  (149 words)

**Expert reference summary:** Who is at risk of developing infantile cataracts?

**Watch for:** Hallucinating a diagnosis for the requester, or silently correcting spelling

**Expected behavior:** Long family history with the actual question at the end: inheritance risk for a future child. Reproduce the misspelling of cataract as written.


### T9 · P1 — Zero-shot — basic task instruction, no examples

```
Summarize this patient information request in four bullets for a triage
coordinator: who is asking, the core question, what information is missing,
and where the request should be routed. Do not answer the question.

SUBJECT: Customer Service Request MESSAGE: Hi, We are 2 sisters and a brother. My brother at age of 24 is detected with catract, however it is mild so operation is not required. But doctor said, there are chances he has this from Birth. However only from past 1.5years he had a little blur vision. And in my entire family Mom and Dad said no one has had catract from Birth. We are a big family, my grapa had 7 brothers and 2 sisters no one from their family or my mom has catract from birth or even at early age. My concern is, if i am pregnant will my children have any issue like this. I amd my sister, mom, dad we all have got our eyes checked. we dont have any issue. I am really tensed as i am expecting. Can you please help me with the answer.
```


### T9 · P2 — Structured — role, context, constraints, output format

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
SUBJECT: Customer Service Request MESSAGE: Hi, We are 2 sisters and a brother. My brother at age of 24 is detected with catract, however it is mild so operation is not required. But doctor said, there are chances he has this from Birth. However only from past 1.5years he had a little blur vision. And in my entire family Mom and Dad said no one has had catract from Birth. We are a big family, my grapa had 7 brothers and 2 sisters no one from their family or my mom has catract from birth or even at early age. My concern is, if i am pregnant will my children have any issue like this. I amd my sister, mom, dad we all have got our eyes checked. we dont have any issue. I am really tensed as i am expecting. Can you please help me with the answer.
```


### T9 · P3 — Few-shot — two examples of the desired output

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
SUBJECT: Customer Service Request MESSAGE: Hi, We are 2 sisters and a brother. My brother at age of 24 is detected with catract, however it is mild so operation is not required. But doctor said, there are chances he has this from Birth. However only from past 1.5years he had a little blur vision. And in my entire family Mom and Dad said no one has had catract from Birth. We are a big family, my grapa had 7 brothers and 2 sisters no one from their family or my mom has catract from birth or even at early age. My concern is, if i am pregnant will my children have any issue like this. I amd my sister, mom, dad we all have got our eyes checked. we dont have any issue. I am really tensed as i am expecting. Can you please help me with the answer.
```


### T9 · P4 — Safety / uncertainty — reduces hallucination and unsupported claims

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
SUBJECT: Customer Service Request MESSAGE: Hi, We are 2 sisters and a brother. My brother at age of 24 is detected with catract, however it is mild so operation is not required. But doctor said, there are chances he has this from Birth. However only from past 1.5years he had a little blur vision. And in my entire family Mom and Dad said no one has had catract from Birth. We are a big family, my grapa had 7 brothers and 2 sisters no one from their family or my mom has catract from birth or even at early age. My concern is, if i am pregnant will my children have any issue like this. I amd my sister, mom, dad we all have got our eyes checked. we dont have any issue. I am really tensed as i am expecting. Can you please help me with the answer.
```


### T9 · P5 — Final improved — best version after testing and revision

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
SUBJECT: Customer Service Request MESSAGE: Hi, We are 2 sisters and a brother. My brother at age of 24 is detected with catract, however it is mild so operation is not required. But doctor said, there are chances he has this from Birth. However only from past 1.5years he had a little blur vision. And in my entire family Mom and Dad said no one has had catract from Birth. We are a big family, my grapa had 7 brothers and 2 sisters no one from their family or my mom has catract from birth or even at early age. My concern is, if i am pregnant will my children have any issue like this. I amd my sister, mom, dad we all have got our eyes checked. we dont have any issue. I am really tensed as i am expecting. Can you please help me with the answer.
```


---


# T10 — rp  (158 words)

**Expert reference summary:** What are the treatments for retinitis pigmentosa?

**Watch for:** Altering or inventing the clinical measurements given

**Expected behavior:** Detailed message with specific measurements. Core question is about treatments and trials. Reproduce the acuity and field values exactly if listed.


### T10 · P1 — Zero-shot — basic task instruction, no examples

```
Summarize this patient information request in four bullets for a triage
coordinator: who is asking, the core question, what information is missing,
and where the request should be routed. Do not answer the question.

Retinitis Pigmentosa RP stem cells, gene therapy. HI I had been diagnosed with Retinitis Pigmentosa RP at the age of 12. I am now 27 years of age. I live in [LOCATION], [LOCATION] and I am willing to travel. In the last 2 years my eyes has deteriorated quite dramatically. My central vision is good, colour and clarity is also good. My visual acuity is approx 20/80. My visual field is approx 45 degrees. My peripheral vision has began to deteriorate and my night vision is poor. I am in middle stages of RP however I am still capable of getting through my day to day activities. I have been searching on the Internet and came across your website. I am interested in any treatments or clinical trials in stem cells, gene therapy or any other treatments you may recommend I look forward to your response and any information you may be able to give. Kind Regards, [NAME]
```


### T10 · P2 — Structured — role, context, constraints, output format

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
Retinitis Pigmentosa RP stem cells, gene therapy. HI I had been diagnosed with Retinitis Pigmentosa RP at the age of 12. I am now 27 years of age. I live in [LOCATION], [LOCATION] and I am willing to travel. In the last 2 years my eyes has deteriorated quite dramatically. My central vision is good, colour and clarity is also good. My visual acuity is approx 20/80. My visual field is approx 45 degrees. My peripheral vision has began to deteriorate and my night vision is poor. I am in middle stages of RP however I am still capable of getting through my day to day activities. I have been searching on the Internet and came across your website. I am interested in any treatments or clinical trials in stem cells, gene therapy or any other treatments you may recommend I look forward to your response and any information you may be able to give. Kind Regards, [NAME]
```


### T10 · P3 — Few-shot — two examples of the desired output

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
Retinitis Pigmentosa RP stem cells, gene therapy. HI I had been diagnosed with Retinitis Pigmentosa RP at the age of 12. I am now 27 years of age. I live in [LOCATION], [LOCATION] and I am willing to travel. In the last 2 years my eyes has deteriorated quite dramatically. My central vision is good, colour and clarity is also good. My visual acuity is approx 20/80. My visual field is approx 45 degrees. My peripheral vision has began to deteriorate and my night vision is poor. I am in middle stages of RP however I am still capable of getting through my day to day activities. I have been searching on the Internet and came across your website. I am interested in any treatments or clinical trials in stem cells, gene therapy or any other treatments you may recommend I look forward to your response and any information you may be able to give. Kind Regards, [NAME]
```


### T10 · P4 — Safety / uncertainty — reduces hallucination and unsupported claims

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
Retinitis Pigmentosa RP stem cells, gene therapy. HI I had been diagnosed with Retinitis Pigmentosa RP at the age of 12. I am now 27 years of age. I live in [LOCATION], [LOCATION] and I am willing to travel. In the last 2 years my eyes has deteriorated quite dramatically. My central vision is good, colour and clarity is also good. My visual acuity is approx 20/80. My visual field is approx 45 degrees. My peripheral vision has began to deteriorate and my night vision is poor. I am in middle stages of RP however I am still capable of getting through my day to day activities. I have been searching on the Internet and came across your website. I am interested in any treatments or clinical trials in stem cells, gene therapy or any other treatments you may recommend I look forward to your response and any information you may be able to give. Kind Regards, [NAME]
```


### T10 · P5 — Final improved — best version after testing and revision

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
Retinitis Pigmentosa RP stem cells, gene therapy. HI I had been diagnosed with Retinitis Pigmentosa RP at the age of 12. I am now 27 years of age. I live in [LOCATION], [LOCATION] and I am willing to travel. In the last 2 years my eyes has deteriorated quite dramatically. My central vision is good, colour and clarity is also good. My visual acuity is approx 20/80. My visual field is approx 45 degrees. My peripheral vision has began to deteriorate and my night vision is poor. I am in middle stages of RP however I am still capable of getting through my day to day activities. I have been searching on the Internet and came across your website. I am interested in any treatments or clinical trials in stem cells, gene therapy or any other treatments you may recommend I look forward to your response and any information you may be able to give. Kind Regards, [NAME]
```


---


# Appendix — original (weak) prompts

Optional. Run these only if you want your own evidence for the Problem column instead of using mine. Substitute any case for the placeholder.


### P1 — original

```
Summarize this patient message.

{message}
```


### P2 — original

```
What should the coordinator do with this request?

{message}
```


### P3 — original

```
Classify this request and summarize it.

Message: {message}
```


### P4 — original

```
Read this patient message and explain what the person needs to know.

{message}
```


### P5 — original

```
Summarize and route this request professionally.

{message}
```
