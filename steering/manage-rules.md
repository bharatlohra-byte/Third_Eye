# Managing Custom Rules

Make it effortless for a user to add, edit, or remove their own design principles and security constraints.
The user should be able to describe a rule in plain language — Kiro handles the formatting, ID assignment,
categorization, and placement in the template. The user never has to hand-edit markdown tables.

## When to Use

Trigger this workflow when the user says things like:
- "Add a rule: no public S3 buckets"
- "We must never log PII — add that as a constraint"
- "Add a design principle about zero trust"
- "Change SC-4 to a BLOCKER"
- "Remove SC-7" / "List our current rules"

## Step 0: Ensure a Template Exists

- Look for `.kiro/security-template.md` (then project root, then a user-provided path).
- If none exists, scaffold one from `templates/security-template.md` at `.kiro/security-template.md`,
  tell the user it was created, and continue. Never block adding a rule just because the file is missing.

## Adding a Rule

The user gives a rule in natural language. You do the rest:

1. **Classify it** as either a Design Principle (a guiding "why") or a Security Constraint (a pass/fail rule).
   - Most concrete "must / must not" statements are constraints. Broad guiding statements are principles.
   - If genuinely ambiguous, make a sensible choice and state it (don't interrogate the user).

2. **Pick the category** (constraints only): Identity & Access, Data Protection, Network & Infrastructure,
   or Application. If it fits none, create a new category section.

3. **Assign the next ID** by scanning existing IDs: `DP-<n+1>` for principles, `SC-<n+1>` for constraints.
   IDs are global and sequential across categories — never reuse or renumber existing IDs.

4. **Suggest a severity** (constraints only) based on impact, and say why in one line:
   - **BLOCKER** — exploitable exposure or secret/credential leakage (public data, hardcoded secrets, wildcard admin).
   - **HIGH** — significant weakening of a control (missing encryption, over-broad access).
   - **MEDIUM** — hardening / best-practice gaps.
   - **LOW** — minor or stylistic.

5. **Normalize the wording** into a short, checkable statement (imperative, testable). Keep the user's intent;
   just tighten it so a reviewer can objectively decide pass/fail.

6. **Write it** into the correct table in the template, preserving formatting. Use `fs_append`/edit — do not
   rewrite unrelated sections or reflow the user's other rules.

7. **Confirm** back concisely: show the new row (ID, category, statement, severity) and note where it went.
   If you had to guess classification or severity, say so and invite a quick correction.

**Example interaction:**
```
User: add a rule — nobody should be able to disable CloudTrail

Kiro: Added SC-11 to Identity & Access:
      "CloudTrail logging must not be disabled or deleted by non-security roles." — Severity: BLOCKER
      (Tampering with the audit trail hides attacker activity, so I rated it BLOCKER. Say the word to change it.)
```

## Editing a Rule

- The user references a rule by ID ("make SC-4 a BLOCKER") or by description ("the encryption one").
- If referenced by description, find the best match and confirm which rule you're changing before editing.
- Edit only that row. Keep the ID stable. Confirm the before/after.

## Removing a Rule

- Confirm which rule (by ID) is being removed, delete that row only, and confirm.
- Do NOT renumber the remaining IDs — existing IDs must stay stable so historical review logs remain valid.

## Listing Rules

- On request, print the current DPs and SCs grouped by category as a compact table so the user can see the
  full ruleset at a glance.

## Quick Add Section (batch capture)

The template includes a **Quick Add** area where users can dump rough, unformatted rules (one per line) without
worrying about tables. When the user asks to "process quick add" (or before running a review), you should:

1. Read each free-text line under Quick Add.
2. Turn each into a properly classified, categorized, ID'd, severity-rated entry using the Adding a Rule steps.
3. Move them into the proper Design Principles / Security Constraints tables.
4. Clear the processed lines from Quick Add and show the user what was added.

This lets users capture rules in the moment and have Kiro formalize them later.

## Principles for This Workflow

- **Plain language in, structured rule out.** The user should never touch a markdown table by hand.
- **Sensible defaults over interrogation.** Guess classification/severity and let the user correct — don't
  ask a chain of questions.
- **Stable IDs forever.** Never renumber; review logs and history reference IDs.
- **Surgical edits.** Touch only the rule in question; preserve everything else in the template.
