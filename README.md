# Project 1A: Build an AI-Powered Employee Training Chatbot

In this project, students will design and build a Python chatbot that uses an LLM API to simulate a customer service training scenario. The chatbot role-plays as an **angry customer** who walks into a mobile phone store with a device that won't turn on. A human employee (the chatbot user) must diagnose the issue, stay professional, and resolve the situation. The chatbot then scores the interaction and gives feedback, just like a real training simulator.

---

## Table of Contents

- [Learning Objectives](#learning-objectives)
- [Scenario Design](#scenario-design)
  - [The Employee's Job](#the-employees-job)
  - [Resolution Rules](#resolution-rules)
  - [Scoring Rules](#scoring-rules)
  - [What the Chatbot Must Track Internally](#what-the-chatbot-must-track-internally)
- [Reference Notes: Diagnostic Questions & Scenario Details](#reference-notes-diagnostic-questions--scenario-details)
  - [Good Diagnostic Questions](#good-diagnostic-questions)
  - [Phrasing Guidance](#phrasing-guidance)
  - [Common Scenario Types](#common-scenario-types)
  - [Edge Cases to Handle](#edge-cases-to-handle)

---

## Learning Objectives

By the end of this project, students will be able to:

- Design a detailed **system prompt** that gives an LLM a persona, hidden rules, and a goal.
- Use **Python** and an **LLM API** (Anthropic or OpenAI) to run a multi-turn conversation.
- Maintain **conversation history and context** so the AI remembers what has been said.
- Implement simple **state tracking** (violation counter, resolved/unresolved status).
- Translate a business scenario (customer service policy) into **logic an AI can enforce**.
- Handle **errors and edge cases** (empty input, API failures, off-topic input).

---

## Scenario Design

The chatbot plays an angry customer who brings in a phone that won't turn on. Encode the following business rules into the system prompt and/or program logic.

### The Employee's Job

- Ask diagnostic questions politely to figure out what is wrong (water exposure, extreme heat, physical damage) **without sounding accusatory**.
- Determine whether the phone is **repairable** or **not repairable**.

### Resolution Rules

| Phone Status | Allowed Offer | Conditions |
|---|---|---|
| **Not repairable** | Up to **30% discount** on a new phone | Customer must trade in the broken phone |
| **Repairable** | Up to **40% discount** on parts only | **No discount on labor** |

### Scoring Rules

- The customer's starting attitude is **hostile**. The goal is for the employee to bring satisfaction to **at least 8/10** by the end of the interaction.
- **Violations** (rude language, refusing to engage, going off-topic) count against the employee.

| Outcome | Final Rating | Feedback |
|---|---|---|
| 3 violations → customer walks out | **1/10** | Negative feedback |
| Issue never resolved (no diagnosis, no offer, conversation stalls) | **2/10** | — |
| Handled well | **8–10/10** | Specific positive/constructive feedback |

### What the Chatbot Must Track Internally

- [ ] A hidden **"ground truth"** for the scenario (e.g., water damage, unrepairable) — not revealed until the employee asks reasonable diagnostic questions.
- [ ] A running **violation counter**.
- [ ] Whether an **appropriate offer** was made and whether it matched the rules (30% new phone / 40% parts).
- [ ] A **final numeric rating** and short feedback delivered at the end of the conversation.

---

## Reference Notes: Diagnostic Questions & Scenario Details

These notes are meant to help students design a richer, more realistic system prompt. They can be turned into a decision tree, or used as a checklist for evaluating whether the "employee" (chatbot user) is doing a thorough diagnosis.

### Good Diagnostic Questions

Employees should gather information before jumping to a diagnosis. Categories to prompt for in the system prompt or grading logic:

#### Environmental Exposure

- "Has the phone been near water recently — a pool, sink, rain, spilled drink?"
- "Was it left in direct sunlight or in a hot car at any point?"
- "Any exposure to extreme cold?"

#### Physical History

- "Has the phone been dropped recently?"
- "Do you notice any cracks, bends, or a swollen back panel?"
- "Was the phone in a case at the time?"

#### Charging / Power Behavior

- "Did the phone show any warning signs before it stopped turning on — slow charging, random restarts, battery draining fast?"
- "Have you tried charging it with a different cable or outlet?"
- "Does the charging port look bent, corroded, or have any debris in it?"

#### 🕒 Timeline and Usage

- "When did you first notice the problem?"
- "Has anyone else attempted a repair or opened the phone?"
- "Is the phone still under warranty, or was it purchased used?"

### Phrasing Guidance

The system prompt should **reward** neutral, curiosity-driven phrasing and **penalize** accusatory phrasing:

| Offensive / Accusatory | Neutral / Diagnostic |
|---|---|
| "Did you drop it in water?" *(assumes blame)* | "Has the phone had any contact with water or moisture recently?" |
| "You probably left it in the sun." | "Was the phone exposed to high heat, like direct sunlight or a hot car?" |
| "Did you break it?" | "Can you walk me through what happened right before it stopped turning on?" |

### Common Scenario Types

You may hard-code one scenario or randomly select one at the start of each run:

| Symptom / History | Likely Cause | Repairable? |
|---|---|---|
| No power; corrosion on port; customer mentions pool | Liquid damage, corroded logic board | Often **not** repairable |
| No power; phone was in a hot car; swollen battery | Heat damage, battery swelling | Sometimes repairable |
| No power; recent hard drop; cracked screen | Physical / impact damage | Often repairable |
| No power; slow decline over weeks; old phone | Battery end-of-life | Usually repairable |
| No power; briefly flickers on then dies | Software / firmware issue | Usually repairable |

### Edge Cases to Handle

> [!NOTE]
> These additional rules should be encoded in the system prompt and/or grading logic.

- **Customer starts abusive immediately (before any diagnosis):** the employee should still get a fair chance to de-escalate; this should **not** count as an automatic violation against the employee.
- **Customer asks for a discount before diagnosis is complete:** the employee should be able to (and should) push back politely and explain that diagnosis comes first — this should **not** itself be penalized.
- **Employee offers a discount outside the allowed range** (e.g., 50% off parts, or a discount on labor): this **should count as a rule violation**, since it breaks store policy even if well-intentioned.
- **Employee offers no discount at all when one is warranted:** this should **hurt the final rating**, since the issue isn't fully resolved.
- **Customer refuses a fair, correctly scoped offer:** the bot should reflect that the employee followed policy correctly, and the rating should reflect the employee's professionalism, even if the customer remains unhappy.
