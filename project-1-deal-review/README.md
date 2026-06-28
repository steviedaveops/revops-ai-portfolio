# Project 1: RevOps Deal Review Assistant

## Overview

An AI-powered deal review tool built on Claude that scores individual 
sales opportunities against 10 red flag criteria and generates 
structured pipeline review reports for reps, managers, and executives.

Built as the first project in a hands-on RevOps AI portfolio, this 
tool demonstrates how a Revenue Operations leader can use Claude to 
automate one of the most time-consuming and inconsistent processes in 
B2B SaaS sales — the pipeline review.

---

## The Problem

Pipeline reviews in most B2B SaaS companies suffer from three chronic 
issues:

1. **Inconsistency** — different managers apply different standards 
   when evaluating deal health, making it impossible to compare risk 
   across reps or territories
2. **Subjectivity** — deals are often evaluated on rep confidence 
   rather than objective criteria, inflating forecast accuracy
3. **Time** — a thorough deal review takes 10–15 minutes per 
   opportunity. At 20+ deals in a pipeline, that's hours of prep 
   before every forecast call

This tool solves all three by encoding a consistent, objective set of 
evaluation criteria into a Claude system prompt — producing structured, 
repeatable deal reviews in seconds.

---

## The Solution

A Claude Project with a custom system prompt that:

- Evaluates every deal against 10 red flag criteria organized into 
  three categories: Data Quality, Deal Health, and Process Integrity
- Scores each deal 🟢 GREEN / 🟡 YELLOW / 🔴 RED based on flag count
- Assigns a Forecast Recommendation: Commit / Best Case / Pipeline / Omit
- Generates a structured report with audience-specific sections for 
  the rep, manager, RevOps, and executive leadership
- Operates in two modes: single deal review and batch pipeline review 
  from a CSV export

---

## Red Flag Criteria

### 🗂️ Data Quality
| # | Flag | Threshold |
|---|------|-----------|
| 1 | Amount blank or $0 | Any open opportunity |
| 2 | No products on opportunity | Any open opportunity |
| 3 | Close date in the past | Any open opportunity |
| 4 | Single-threaded | Only 1 contact on record |

### 📉 Deal Health
| # | Flag | Threshold |
|---|------|-----------|
| 5 | No activity logged | More than 14 days |
| 6 | No next step scheduled | Next step blank or date past |
| 7 | Close date vs. stage mismatch | Close within 30 days, stage below Proposal |
| 8 | Excessive time in stage | More than 60 days in current stage |

### ⚠️ Process Integrity
| # | Flag | Threshold |
|---|------|-----------|
| 9 | Push counter exceeded | Push counter greater than 2 |
| 10 | Weak stage exit criteria | Missing documentation at prior stage transitions |

### Risk Scoring
- 🟢 **GREEN** — 0 to 1 flags: healthy, proceed
- 🟡 **YELLOW** — 2 to 3 flags: needs attention or coaching
- 🔴 **RED** — 4 or more flags: escalate or remove from forecast

---

## Report Structure

Every report — whether single deal or batch pipeline review — follows 
this structure, designed around the principle of **audience-aware 
design**: each section is written for a specific reader with a 
specific job to do.

### Batch Pipeline Review Output
1. **Pipeline Health Summary** — financial metrics and priorities 
   for the period
2. **Opportunity Summary Table** — RED and YELLOW deals only, sorted 
   by flag count, with a one-sentence verdict per deal
3. **Deal Detail** — full flag analysis, positive signals, and 
   recommended actions by role (Rep / Manager / RevOps / CRO)
4. **Key Takeaways** — systemic patterns and highest-impact 
   recommendations across the full pipeline
5. **Forecast Category Definitions** — reference guide included in 
   every report

### Single Deal Review Output
1. **Risk Level + Forecast Recommendation** — immediate verdict
2. **Summary** — two sentences: the diagnosis and the recommended 
   disposition
3. **Deal Overview and Recommended Next Steps** — red flags, positive 
   signals, and role-specific actions

---

## How to Use

### Setup
1. Go to [claude.ai](https://claude.ai) and click **Projects** 
   in the left sidebar
2. Click **New Project** → name it `RevOps Deal Review Assistant`
3. Click **Set project instructions** and paste the full contents 
   of `system-prompt.md` into the instructions field
4. Click **Save**

### Single Deal Review
Start a new chat inside the project and paste deal data in this 
format:

- Account: [Account Name]
- Stage: [Stage]
- Amount: [Amount]
- Close Date: [Date]
- Days in Current Stage: [X]
- Last Activity: [Date]
- Next Step: [Text or None]
- Next Step Date: [Date or None]
- Contacts: [X]
- Products: [List or None]
- Push Counter: [X]
- Notes: [Stage history notes]

The assistant will return a full deal review automatically.

### Batch Pipeline Review
1. Export your Salesforce pipeline report as a CSV using the 
   column structure in `sample-pipeline.csv`
2. Start a new chat inside the project
3. Upload the CSV using the attachment icon
4. Send this trigger prompt: 
Run a full pipeline review on the uploaded file. Apply all
red flag criteria to every deal. Skip any Closed Won
opportunities. Return Red and Yellow deals only, sorted by
flag count descending. End with the pipeline health summary.
Perform all flag analysis and calculations silently. Do not
output any reasoning, working, data tables, or intermediate
steps — not even in a code block. Output the final formatted
report only, beginning with Section 1. When the report is
complete, output a single line: "Type AUDIT LOG to see flag
analysis."
5. The assistant will return a fully formatted pipeline review 
   report ready to share

### Audit Log
If you need to review how a deal was scored — to challenge a 
flag or verify a calculation, type: AUDIT LOG

The assistant will return the full per-deal flag analysis for 
the most recent review.

---

## Design Decisions

### Why separate Data Quality, Deal Health, and Process Integrity?
Most pipeline review frameworks only catch surface-level hygiene 
issues. Separating flags into three categories forces a more complete 
evaluation — and in interviews, demonstrates that the builder 
understands the difference between a data problem, a momentum problem, 
and a process problem. These require different responses and different 
owners.

### Why put the Summary before the detail?
Following the inverted pyramid principle — lead with the conclusion, 
follow with the evidence. A CRO reviewing 15 deals in a 30-minute 
call needs the verdict in the first 10 seconds, not after reading 
eight bullet points.

### Why impersonal language in the Summary section?
In a group pipeline review setting, directing actions at the CRO by 
name ("The CRO should remove this deal...") can undermine leadership 
credibility if the recommendation is not followed. Impersonal language 
("This deal should be removed...") communicates the same urgency 
without creating political risk for the executive in the room.

### Why two modes — single deal and batch?
Different use cases require different output density. A single deal 
review is used for deep coaching conversations — it benefits from 
rich, specific language. A batch pipeline review is used for fast 
executive decision-making — it benefits from consistent, scannable 
structure. The same system prompt handles both by detecting the input 
type and adjusting output format accordingly.

### Why Forecast Category Definitions at the bottom?
Definitions belong at the end as a reference, not the beginning as 
an explanation. Readers who know the definitions skip them. Readers 
who need them can find them. Placing them at the top interrupts the 
flow for the majority to serve the minority.

---

## Bugs Found and Fixed

| # | Bug | Root Cause | Fix |
|---|-----|-----------|-----|
| 1 | Non-triggered flags listed under Red Flags | Claude listed all evaluated flags regardless of outcome | Added explicit instruction: only list flags that were actually triggered |
| 2 | Flag count in header didn't match bullet count | Claude counted flags before listing them — two separate steps that drifted | Restructured prompt to derive count from the list, not predict it ahead |
| 3 | CRO Summary contained action directives at the CRO | No constraint on language in the summary section | Added behavior rule: never use "the CRO should" — use impersonal language throughout |
| 4 | Yellow deals disappeared from output between runs | Borderline deals (exactly 2 flags) scored inconsistently due to probabilistic model behavior | Strengthened threshold language: round UP on ambiguous flags; updated test CSV to give borderline deals 3 clear flags |
| 5 | Pre-report reasoning displayed in conversation | Claude reasoned visibly before generating output — accurate but clutters conversation at scale | Added behavior rule: perform all reasoning silently; output final report only |
| 6 | Closed Won deals referenced by name in exclusion note | Prompt didn't specify how to handle excluded deals | Added rule: excluded deals are silently included in financial calculations only — never listed by name |

---

## Key Technical Insights

**Claude is probabilistic, not deterministic.** The same prompt and 
data will produce slightly different phrasing each run due to 
temperature — the intentional randomness built into the model. Facts 
stay consistent; word choice varies. The fix is tighter output format 
instructions for structured data, and creative latitude only where 
variation adds value.

**System prompts are the intellectual contribution.** Anyone can ask 
Claude a question. Building a tool with consistent, role-specific, 
audience-aware behavior requires deliberate prompt architecture — 
persona, criteria, scoring logic, output format, and behavior rules 
working together as a system.

**Context window is a production constraint.** At scale (100+ deals), 
visible reasoning tokens exhaust the context window before the report 
is complete. The solution — silent reasoning enforced by behavior 
rules, with on-demand audit log — is how production AI tools are 
architected. Claude Code and the Anthropic API handle this more 
reliably via the `thinking` parameter.

---

## Files in This Folder

| File | Description |
|------|-------------|
| `README.md` | This document |
| `system-prompt.md` | The complete Claude system prompt |
| `sample-pipeline.csv` | 10-deal test dataset used for development |
| `sample-output.md` | Example pipeline review report output |

---

## What I'd Build Next

- **Stage 3 — Claude Code integration**: automate the Salesforce 
  export → CSV → report pipeline so the review runs on a schedule 
  without manual steps
- **Action library**: encode repeatable RevOps plays (re-engagement, 
  stage reset, forecast scrub) that Claude triggers automatically 
  based on the flag pattern of each deal
- **Deal audit report**: pull stage history, push timestamps, and 
  activity logs from Salesforce to generate a per-deal audit trail 
  alongside the flag report

---

*Part of the [RevOps AI Portfolio](../README.md) — built by Dave Glen, Senior Revenue Operations Leader*
