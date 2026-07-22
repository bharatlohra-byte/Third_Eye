# Daily Reviewer Summary

Purpose: give a **human reviewer** a fast, trustworthy daily picture of what changed in the repository and
how risky it is, rated **Red / Amber / Green** against the team's security template. When a team ships code
with Kiro, change volume grows and manual review gets hard. This workflow surfaces and records the security
posture of each change so nothing risky merges silently — even if the reviewer ultimately decides to accept it.

## When to Use

Trigger when the user (a reviewer) asks to:
- "Give me the daily security summary"
- "What changed on the repo and is any of it risky?"
- "Run the daily RAG review"

It is also a good fit for a `userTriggered` Kiro hook the reviewer clicks each morning (see "Automating" below).

## Step 1: Load the Ruleset

Read the security template (see the custom-review steering file for locating and parsing it):
- Recommended location: `.kiro/security-template.md`
- Extract design principles, security constraints (with severities), scope globs, **RAG rating rules**, and
  **Daily Review Settings**.
- If no template exists, offer to scaffold one from `templates/security-template.md` and proceed with the
  Well-Architected baseline in the meantime.

## Step 2: Determine What Changed

Use git to find the change set for the configured window (default: commits in the last 24h). Useful commands:

```bash
# First, refresh remote branches so nothing recent is missed on a stale local clone
git fetch --all --quiet

# Commits in the last day, all branches, with author and summary
git log --all --since="24 hours ago" --pretty=format:"%h %an %ad %s" --date=short

# Files changed in that window
git log --all --since="24 hours ago" --name-only --pretty=format:"%h %an %s"

# Full diff for a specific commit or range
git diff <base>..<head>
```

Respect the template's **Base for comparison**, **Branches to include**, and scope include/exclude globs.
Only review files that are in scope. If the repo has open pull requests and a CLI is available
(`gh`, `glab`), you may group by PR instead of raw commits.

## Step 3: Review Each Change Against the Template

For the changed, in-scope code and IaC, evaluate the security constraints exactly as described in the
custom-review workflow (PASS / FAIL / PARTIAL / NOT APPLICABLE / NEEDS REVIEW), citing file:line evidence.
Focus only on what changed in the window — this is a delta review, not a full-repo scan.

Then translate findings into a RAG rating per unit (per author group / file area / PR, per the template):

- 🔴 **Red** — any BLOCKER or HIGH constraint violated in the change.
- 🟠 **Amber** — MEDIUM/LOW violation, PARTIAL pass, or NEEDS REVIEW.
- 🟢 **Green** — no violations found.

Use the template's RAG Rating Rules if the user customized the thresholds.

## Step 4: Produce the Daily Summary

Lead with an at-a-glance banner, then detail. Example shape:

```
Daily Security Review — 2026-07-22
Window: last 24h · Branches: all · Scope: src/**, infra/**

Overall: 🔴 1 Red · 🟠 2 Amber · 🟢 5 Green   (8 changed areas across 3 authors)
```

**Change table:**

| Change (commit/PR) | Author | Area | Rating | Top issue | Constraint | Evidence |
|--------------------|--------|------|--------|-----------|------------|----------|
| a1b2c3d | jsmith | infra/iam.tf | 🔴 Red | Wildcard IAM policy | SC-1 (BLOCKER) | infra/iam.tf:42 |
| e4f5g6h | apatel | src/api | 🟠 Amber | Input not validated on new endpoint | SC-8 | src/api/orders.ts:88 |
| i7j8k9l | apatel | docs | 🟢 Green | — | — | — |

**Reviewer callouts (prioritized):**
- List every 🔴 first with a one-line "what and why it matters" and a remediation suggestion.
- Then 🟠 items, including anything marked NEEDS REVIEW that needs a human decision.
- Keep 🟢 as a count; list only if the reviewer asks.

**Reviewer note:**
- End with: these ratings are advisory. The reviewer may accept (pass) an Amber or Red item, but the risk
  has been called out here and is recorded in the dated log file (Step 5), creating an audit trail.

## Step 5: Write the Dated Log File (required)

Every daily review MUST be written to a date-stamped log file, in addition to being shown in chat. This is
the audit trail — it records what was flagged and, later, what the reviewer chose to accept.

- **Location & name:** use the template's `Log directory` and `Log filename pattern`
  (defaults: `.kiro/security-reviews/daily-review-YYYY-MM-DD.md`, where the date is today's date).
- **Create the directory** if it does not exist.
- **If a log for today already exists:** follow the template's setting (default: append a new run section
  headed with a timestamp, e.g., `## Run at 2026-07-22 14:30`), so multiple runs in one day are all preserved
  rather than overwritten.
- **Log contents:** the full summary from Step 4 — banner, change table, prioritized callouts, and the
  reviewer note. Include the review window, branches, and scope so the record is self-describing.
- After writing, tell the reviewer the exact path of the log file that was created or updated.

**Example log header:**
```markdown
# Daily Security Review — 2026-07-22

- Window: last 24h
- Branches: all
- Scope: src/**, infra/**
- Overall: 🔴 1 Red · 🟠 2 Amber · 🟢 5 Green

## Run at 2026-07-22 09:00
...summary...
```

Then offer next actions: open a specific finding, propose fixes, or re-run after changes.

## Automating (Optional Hook)

This workflow pairs well with a `userTriggered` Kiro hook so the reviewer generates the summary with one click:

```json
{
  "name": "Third Eye — Daily Review",
  "version": "1.0.0",
  "when": { "type": "userTriggered" },
  "then": {
    "type": "askAgent",
    "prompt": "Run the Third Eye daily reviewer summary: review repo changes from the last 24h against .kiro/security-template.md and produce a Red/Amber/Green summary."
  }
}
```

Note: Kiro hooks are event-driven (there is no built-in scheduler), so a reviewer clicks this each day, or it
can be tied to another event (e.g., `agentStop`). Offer to create the hook only if the user wants it.

## Principles

- **Delta, not full scan.** Review what changed in the window; keep it fast enough to run daily.
- **Always rate, never hide.** Every change gets a RAG; risky items are called out even if later accepted.
- **Evidence over opinion.** Cite file:line; mark uncertainty as NEEDS REVIEW (→ Amber), don't assert.
- **Reviewer stays in control.** The power advises and records; the human decides what to pass.
