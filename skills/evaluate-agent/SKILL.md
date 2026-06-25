---
name: evaluate-agent
description: >-
  Run a UX evaluation of an MCP-based agent against the HCC UXD evaluation
  framework. Use when the user asks to evaluate, test, score, or review an
  agent's UX quality, or when they reference CPUX-6441, CPUX-6562, or the
  agent evaluation dimensions.
---

# Evaluate Agent UX Quality

Run an MCP-based agent through the HCC UXD evaluation framework, producing a
structured findings report with scores and prioritized recommendations.

## Pre-flight: Gather Inputs

Before starting, collect the following from the user. Use `AskQuestion` where
possible; otherwise ask conversationally.

### Required inputs

1. **Agent identifier** — which agent is being tested? (e.g., "RBAC MCP agent
   for CRCPLAN-420", "Notifications & Integrations agent for CRCPLAN-419")
2. **Access method** — how does the evaluator reach the agent?
   - URL to the agent UI (staging/prod)
   - MCP server endpoint (if testing via Cursor MCP)
   - CLI or API access details
   - Auth method: SSO, API token, direct link, etc.
3. **Test tasks** — the specific user scenarios to run. Each task should be a
   realistic end-user goal, stated as a user story:
   > "As an org admin, I need to grant User X read access to System Y."

   Minimum: **2 tasks** (one happy-path, one edge/error case).
   Ideal: 3-5 tasks spanning different complexity levels.
4. **Evaluation dimensions** — confirm which dimensions apply (defaults below).
5. **Jira context** — parent epic (default: CPUX-6441), story to update.

### Collecting test tasks

If the user doesn't have tasks ready, help them generate good ones:

- Pull the agent's target use cases from its CRCPLAN ticket description
- Identify 1 straightforward task, 1 multi-step task, and 1 error/edge case
- Confirm with the user before proceeding

### Collecting login/access instructions

Since Cursor cannot interactively log into a web UI, clarify the access model:

- **If MCP server**: get the server URL; the agent can call it directly via MCP tools
- **If web UI**: the user performs the interaction and pastes the agent's responses, OR provides screenshots
- **If API**: get endpoint + auth token

## Evaluation Dimensions

Score each dimension 1-5 (1 = critical failure, 5 = excellent).

| # | Dimension | What to assess |
|---|-----------|----------------|
| 1 | **Clarity of intent** | Does the agent clearly communicate what it will do before acting? |
| 2 | **Transparency** | Can the user see what the agent did, what data it accessed, and why? |
| 3 | **Recovery & error handling** | When things go wrong, does the agent explain the error and offer a path forward? |
| 4 | **Confidence signals** | Does the agent indicate certainty/uncertainty? Does it hedge appropriately? |
| 5 | **Human-in-the-loop for mutations** | For destructive or consequential actions, does the agent confirm before proceeding? |
| 6 | **Task completion** | Did the agent actually accomplish the user's goal? |
| 7 | **Efficiency** | How many turns/steps did it take? Was there unnecessary back-and-forth? |
| 8 | **Tone & language** | Is the language appropriate for the audience (sysadmin, org admin, developer)? |

### Automation feasibility per dimension

| Dimension | Automatable? | Method |
|-----------|-------------|--------|
| Clarity of intent | Partially | Check for confirmation prompts before actions |
| Transparency | Partially | Verify response includes "what I did" summary |
| Recovery & error handling | Yes | Send malformed inputs, check for graceful responses |
| Confidence signals | Partially | NLP check for hedging language patterns |
| Human-in-the-loop | Yes | Attempt mutations, verify confirmation step exists |
| Task completion | Yes | Compare final state to expected outcome |
| Efficiency | Yes | Count turns, measure token usage |
| Tone & language | No | Requires human judgment |

## Evaluation Workflow

### Step 1: Set up

- Confirm access to the agent
- Document agent version, date, environment (staging/prod)
- Record the test tasks verbatim

### Step 2: Run each test task

For each task:

1. State the task clearly to the agent
2. Record the full interaction (all turns)
3. Note: first response time, number of turns, any errors
4. Score each evaluation dimension (1-5) with a brief justification

### Step 3: Synthesize findings

Use this report template:

```markdown
# Agent UX Evaluation Report

**Agent:** [name and version]
**Date:** [evaluation date]
**Evaluator:** [name]
**Related Jira:** [CPUX-XXXX], parent epic [CPUX-6441]
**Environment:** [staging/prod/local]

## Test Tasks

### Task 1: [task description]
**Outcome:** [pass/partial/fail]
**Turns:** [count]

[Full interaction transcript or summary]

#### Dimension Scores
| Dimension | Score (1-5) | Notes |
|-----------|:-----------:|-------|
| Clarity of intent | | |
| Transparency | | |
| Recovery & error handling | | |
| Confidence signals | | |
| Human-in-the-loop | | |
| Task completion | | |
| Efficiency | | |
| Tone & language | | |

### Task 2: [task description]
[Same structure]

## Summary

| Dimension | Task 1 | Task 2 | Average |
|-----------|:------:|:------:|:-------:|
| Clarity of intent | | | |
| ... | | | |
| **Overall** | | | |

## Prioritized Recommendations

### Must-change (before GA)
1. [finding + recommendation]

### Should-change (next iteration)
1. [finding + recommendation]

### Nice-to-have
1. [finding + recommendation]
```

### Step 4: Deliver

- Save the report as a markdown file in the project
- Add a comment to the relevant Jira story with a summary and link
- Link findings from the parent epic CPUX-6441

## References

- Parent epic: [CPUX-6441](https://redhat.atlassian.net/browse/CPUX-6441) — Evaluate UX quality of HCC MCP agents
- Automation exploration: [CPUX-6562](https://redhat.atlassian.net/browse/CPUX-6562) — Investigate automation approaches
- RBAC agent: [CRCPLAN-420](https://redhat.atlassian.net/browse/CRCPLAN-420)
- Notifications agent: [CRCPLAN-419](https://redhat.atlassian.net/browse/CRCPLAN-419)
