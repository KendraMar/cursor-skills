---
name: jira-highlights
description: >-
  Generate monthly or sprint highlights from Jira data. Queries issues by
  project/component and date range, groups by epic/theme, enriches with
  comment detail, and produces concise stakeholder-ready bullet points.
  Use when the user asks for sprint highlights, monthly highlights, review
  of work, team accomplishments, or a status summary from Jira.
---

# Jira Highlights Generator

Produce a concise, stakeholder-ready summary of team accomplishments from
Jira data over a given time period (sprint or month).

## Execution Steps

1. **Clarify scope** — Confirm the time window (sprint dates or month),
   project key, and component. Default to env vars if available
   (`VITE_JIRA_PROJECT_KEY`, `VITE_JIRA_COMPONENT`).
2. **Query Jira** — Fetch issues updated or transitioned in the window
   using JQL:
   ```
   project = "{PROJECT}" AND component = "{COMPONENT}"
     AND updated >= "{START}" AND updated <= "{END}"
   ```
   Use the Atlassian MCP server (`jira_search` tool) or the app's
   `jiraService.ts` fetch layer — whichever is available.
3. **Fetch comments** — For each issue in a relevant status (In Progress,
   Review, Done/Closed), pull full comment history. Comments contain the
   specific accomplishments and context that make highlights rich.
4. **Group by theme** — Cluster issues into thematic groups based on epic
   name and summary keywords. Typical groups for HCC UXD:
   - Kessel / AuthZ
   - Console Settings (Notifications, Integrations, Sources)
   - Subscriptions
   - Chroming (Navigation, Help Panel, Homepage, Masthead)
   - AI Enablement
   - Other
   Adjust groups to fit the team's current focus areas.
5. **Generate highlight bullets** — For each group, produce 1–3 concise
   bullets following the format rules below.
6. **Add Risks and Next Up** — Append a short Risks section (blockers,
   dependencies) and Next Up section (upcoming priorities).
7. **Review with user** — Present the draft and iterate on length, tone,
   and grouping per feedback.

---

## Bullet Format Rules

Each bullet should follow this pattern:

```
**{Theme / Initiative}** — {What was accomplished, in past tense}.
{Customer benefit in one sentence}. *(Contributors)*
```

### Phrasing guidelines

- Lead with **past-tense action verbs**: Completed, Delivered, Shipped,
  Explored, Prototyped, Presented, Validated, Built.
- Include **specific artifacts**: Figma flows, share decks, Amplitude
  dashboards, research summaries, mockups, scripts.
- Include **quantifiable detail** when available: participant counts,
  story counts, session counts, milestone dates.
- Include **contributor first names** at the end in italics.
- Include **customer benefit** — one sentence on why this matters to
  end users. Frame as the user's gain, not the team's activity.
- Keep each bullet to **2–3 sentences max** at final conciseness.

### Conciseness levels

The user may ask you to adjust verbosity. Follow this scale:

| Level | Target | Style |
|-------|--------|-------|
| Detailed | 4–5 sentences per bullet | Include comment-level color, dates, names, blockers |
| Standard | 2–3 sentences per bullet | Artifacts + customer benefit + contributors |
| Concise | 1–2 sentences per bullet | Key accomplishment + contributors only |

Start at **Standard**. Adjust when the user asks for "more detail" or
"shorter / more concise."

---

## Output Template

```
**{Team Name} — {Month Year} Highlights**

### Highlights

- **{Theme}** — {Accomplishment}. {Customer benefit}. *({Names})*

- **{Theme}** — {Accomplishment}. {Customer benefit}. *({Names})*

### Risks

- **{Risk area}** — {One-line description of blocker or concern}

### Next Up

- {Upcoming priority 1}
- {Upcoming priority 2}
```

---

## Example Output

```
**HCC UXD Team — March 2026 Highlights**

### Highlights

- **AI-generated dashboard widgets** — Completed Figma flows for
  personalized, shareable dashboards. V1 designs in ENG review. Helps
  customers surface role-specific data without navigating multiple
  pages. *(Mary, Kendra)*

- **Help Panel research** — 12-participant study completed and
  presented. Began undocked help panel mockups (April 21 dev target).
  Lets customers reference help without leaving their task. *(SJ, Mary)*

- **AuthZ onboarding** — AI-prototyped 4 conversion flows, handed off
  for Management Fabric review. Smooths the transition for thousands
  of migrating customers. *(Asumi, SJ, Kendra)*

### Risks

- **Sub automation** blocked by security concerns around scripted access.

### Next Up

- Undocked help panel dev handoff
- AuthZ new customer exploration
- Dashboard ENG phasing review
```

---

## Comment Enrichment Strategy

Comments are the richest source of "color" for highlights. When
fetching comments:

1. **Filter by date** — Only include comments created within the
   target time window.
2. **Extract accomplishments** — Look for phrases indicating completed
   work: "shared with," "presented at," "handed off," "completed,"
   "created," "shipped," "published."
3. **Extract plans/next steps** — Look for: "plans to," "working on,"
   "next step," "will," "building."
4. **Extract blockers** — Look for: "blocked by," "waiting on,"
   "dependency," "pushed back."
5. **Ignore noise** — Skip automated comments, bot messages, and
   one-word acknowledgments.

---

## Status Mapping

Only issues in these statuses contribute to highlights:

| Jira Status | Highlight treatment |
|-------------|-------------------|
| Done / Closed | "Completed {topic}." or past-tense verb lead |
| In Review / Ready for Review | "Submitted {topic} for review." |
| In Progress | "Worked on {topic} by {detail from comments}." |
| To Do / Refinement / Backlog | Excluded from highlights |

---

## Tips

- When the user provides a screenshot of a previous highlights slide,
  match its structure, tone, and level of detail.
- Group themes flexibly — if the team's focus areas have shifted,
  create new groups rather than forcing issues into stale categories.
- For monthly highlights, query a wider window (full calendar month)
  and emphasize closed/shipped work. For sprint highlights, use the
  exact sprint start/end dates and include in-progress work.
