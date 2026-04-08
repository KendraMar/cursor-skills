---
name: jira-comment-writer
description: Draft and post structured comments on Jira tickets while working in Cursor. Supports status updates, design decisions, blockers, and general notes. Use when the user wants to comment on a Jira ticket, post an update, document a decision, flag a blocker, or write a note on a CPUX issue.
---

# Jira Comment Writer

Draft structured comments for Jira tickets and optionally post them via the Atlassian MCP.

## Prerequisites

- Atlassian MCP must be connected
- Cloud ID: `redhat.atlassian.net`
- Project key: `CPUX`

## Workflow

```
Comment Progress:
- [ ] Step 1: Identify the ticket and comment type
- [ ] Step 2: Gather context
- [ ] Step 3: Draft the comment
- [ ] Step 4: Present draft for review
- [ ] Step 5: Post to Jira (if approved)
```

### Step 1: Identify the Ticket and Comment Type

Ask the user for:

1. **Jira ticket key** — e.g., `CPUX-1234`. If a ticket is already being discussed in the conversation, use that.
2. **Comment type** — one of:

| Type | When to use |
|------|-------------|
| **Status update** | Reporting progress, what was done, what's next |
| **Design decision** | Documenting a UX/design choice and rationale |
| **Blocker / Question** | Flagging something that's blocking progress or needs stakeholder input |
| **General note** | Anything else — handoff notes, context, links, etc. |

If the type is obvious from context, skip asking and proceed.

### Step 2: Gather Context

Based on the comment type, collect the relevant details:

**Status update:**
- What was completed?
- What's in progress or next?
- Any risks or timeline changes?

**Design decision:**
- What decision was made?
- What alternatives were considered?
- Why was this approach chosen?
- Any supporting links (Figma, docs, research)?

**Blocker / Question:**
- What is blocked?
- Who needs to respond or unblock?
- What is the impact if unresolved?
- Suggested resolution (if any)?

**General note:**
- What information needs to be captured?
- Any links or references to include?

If the conversation already contains this context, extract it rather than re-asking.

### Step 3: Draft the Comment

Use markdown `contentFormat`. Follow these templates:

**Status update:**
```
**Status update**

**Done:**
- [What was completed]

**In progress:**
- [What's currently being worked on]

**Next:**
- [What's coming up]

[Optional: risks, timeline notes]
```

**Design decision:**
```
**Design decision: [brief title]**

**Decision:** [What was decided]

**Alternatives considered:**
- [Option A] — [why not chosen]
- [Option B] — [why not chosen]

**Rationale:** [Why this approach was selected]

[Optional: links to Figma, docs, research]
```

**Blocker / Question:**
```
**🚧 Blocker: [brief title]**

**Blocked:** [What can't proceed]
**Waiting on:** [Person or team]
**Impact:** [What happens if this isn't resolved]

[Optional: suggested resolution]
```

**General note:**
```
**Note: [brief title]**

[Content of the note]

[Optional: links, references]
```

### Step 4: Present Draft for Review

Show the full draft to the user formatted as it will appear in Jira. Ask:

- "Does this look good? I can post it to [TICKET-KEY], or you can edit it first."

Allow the user to request changes. Iterate until approved.

### Step 5: Post to Jira

Once approved, post using the Atlassian MCP:

```json
{
  "cloudId": "redhat.atlassian.net",
  "issueIdOrKey": "<TICKET-KEY>",
  "commentBody": "<full markdown comment>",
  "contentFormat": "markdown"
}
```

After posting, confirm success: "Comment posted to [TICKET-KEY]."

If the user declines posting, say: "No problem — the comment is ready to copy whenever you need it."

## Tips

- Keep comments concise. Jira comments that are too long get skimmed or ignored.
- Use bold headers so comments are scannable in the Jira activity feed.
- When referencing other tickets, use the key format (`CPUX-1234`) so Jira auto-links them.
- If the user says "comment on this ticket" without specifying a type, infer the best type from the conversation context.
