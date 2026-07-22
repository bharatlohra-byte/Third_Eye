# Custom Security Review

This workflow lets a user define their own **design principles** and **security constraints** in a template,
then have Kiro review application code against them and produce a structured summary. It layers the user's
project-specific rules on top of the AWS Well-Architected Security baseline covered by the other steering files.

## When to Use

Trigger this workflow when the user asks to:
- "Review my code against our security template / security constraints"
- "Run a security review"
- "Check this app against our design principles"

## Step 1: Locate the Security Template

The template defines the ruleset. Look for it in this order:

1. `.kiro/security-template.md` (recommended location)
2. `security-template.md` at the project root
3. Any path the user explicitly provides

**If no template exists:**
- Offer to scaffold one by copying the template shipped with this power:
  `templates/security-template.md` → `.kiro/security-template.md`
- Ask the user to fill in their design principles and constraints, or offer to pre-populate it with the
  Well-Architected baseline (see the other steering files) so they can trim it down.
- Do not proceed with a review until there is a ruleset to review against.

## Step 2: Parse the Template

Read the template and extract:
- **Project context** — stack, data sensitivity, compliance targets (tailors severity and focus).
- **Design principles** (DP-*) — the guiding "why."
- **Security constraints** (SC-*) — the pass/fail rules, each with a severity (BLOCKER/HIGH/MEDIUM/LOW).
- **Scope** — include/exclude globs.
- **Output preferences** — format, fail condition, whether to include remediation.

If a section is empty, fall back to the Well-Architected defaults for that area (pull the relevant
steering file: identity-access-management, data-protection, infrastructure-protection, application-security).

## Step 3: Review the Code

For each constraint, inspect the in-scope code and configuration and determine a status:

- **PASS** — evidence the constraint is satisfied.
- **FAIL** — concrete violation found (cite file and line).
- **PARTIAL** — satisfied in some places, violated in others.
- **NOT APPLICABLE** — constraint does not apply to this codebase.
- **NEEDS REVIEW** — cannot determine automatically; flag for a human.

Guidance for reviewing:
- Search infrastructure-as-code (Terraform, CloudFormation, CDK) for IAM, encryption, network, and public-access rules.
- Search application code for input validation, parameterized queries, error handling, and authN/authZ.
- Scan for hardcoded secrets, tokens, and keys across all in-scope files.
- Check dependency manifests for pinned versions and known-vulnerable packages.
- Always cite the exact file and line for any FAIL or PARTIAL so the finding is actionable.
- Do not fabricate findings — if there is no evidence, mark NEEDS REVIEW rather than guessing.
- Map each finding back to its constraint ID and, where relevant, the Well-Architected design principle.

## Step 4: Generate the Summary

Produce a report using the user's output preferences (default: both table and narrative).

**Header:**
- Application name, date, scope reviewed, overall verdict (PASS / FAIL against the configured fail condition).

**Findings table:**

| ID   | Constraint | Severity | Status | Evidence (file:line) | Remediation |
|------|-----------|----------|--------|----------------------|-------------|
| SC-1 | No wildcard IAM | BLOCKER | FAIL | infra/iam.tf:42 | Scope actions/resources to what the role needs |
| SC-3 | S3 Block Public Access | BLOCKER | PASS | infra/s3.tf:10 | — |
| ...  | | | | | |

**Summary counts:**
- Totals by status (PASS/FAIL/PARTIAL/NA/NEEDS REVIEW) and by severity.
- Highlight all BLOCKER and HIGH failures first.

**Narrative section:**
- Short prioritized list of the most important issues and why they matter (tie to design principles).
- Concrete remediation steps for each failure (include remediation only if the user opted in).
- Note any areas marked NEEDS REVIEW that require human judgment.

**Verdict:**
- Apply the template's "fail the review if" rule (default: fail on any BLOCKER or HIGH). This default matches
  the daily review's 🔴 Red rating so a full review and the daily summary agree on what "must fix" means.
- State clearly whether the code passes or must be remediated before shipping.

## Step 5: Offer Next Actions

After presenting the summary, offer to:
- Fix specific findings (make the code changes).
- Re-run the review after fixes.
- Save the report to a file (e.g., `security-review-<date>.md`) if the user wants a record.

## Principles for a Good Review

- **User rules first, baseline second.** The template is authoritative; Well-Architected fills gaps.
- **Every finding is actionable.** Cite location and give a fix.
- **No false confidence.** Mark uncertainty as NEEDS REVIEW instead of asserting PASS.
- **Severity drives priority.** Lead with BLOCKER/HIGH; don't bury critical issues under minor ones.
