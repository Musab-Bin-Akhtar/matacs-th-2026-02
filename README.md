# MATACS Technical Assessment — AI Proposal Triage Design

**Candidate:** Musab Bin Akhtar  
**Assessment reference:** `MATACS-TH-2026-02`  
**Actual time spent:** Replace this line with your actual elapsed time before submission.

## Overview

This repository describes an AI-assisted proposal triage workflow. AI extracts facts from proposal documents; deterministic code validates mandatory requirements, calculates scores, and ranks eligible proposals. A human reviewer remains responsible for resolving exceptions and making the final award decision.

## Files

- [Design write-up](WRITEUP.md)
- [AI prompts](PROMPTS.md)
- [Expected ranking](ranking.json)
- [Process-flow diagram](diagram/process-flow.png)

## AI-use disclosure — review and personalize before submitting

I used ChatGPT to help structure the design, draft the documentation, and check the scoring logic against the assessment requirements. I independently reviewed the treatment of the proposal with unconfirmed subcontractor licensing and the proposal with no stated completion schedule; these cases should be flagged rather than assigned unsupported facts or silently treated as compliant. Before submission, I will verify the calculations and make any corrections I identify.

## Important

This is a design submission, not a deployed application. The scoring formulas are explicit design assumptions because the brief does not define detailed normalization rules. Review and understand these assumptions before the follow-up interview.
