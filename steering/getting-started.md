# Getting Started (First-Run Onboarding)

Run this the first time the power is used in a workspace, or whenever the user asks to "set up",
"get started", or "configure security rules". The goal is to get the user to a working, committed ruleset
in a couple of minutes — with sensible pre-built rules — rather than facing a blank template.

## Trigger

- On first activation in a workspace where no `.kiro/security-template.md` exists yet.
- Or explicitly: "set up the security power", "help me add custom rules", "configure my security review".

Keep it a short, friendly conversation. Offer good defaults; don't overwhelm with questions.

## Step 1: Offer to Set Up Rules

Greet and explain briefly, then ask if they'd like to add custom rules now:

> "This power reviews your code against a security ruleset (yours, layered on the AWS Well-Architected
> baseline) and can produce a daily Red/Amber/Green review log. Want to set up your rules now? I can start you
> off with ready-made rule packs so you're not staring at a blank file."

Offer the choice:
- **Yes, use rule packs** → Step 2.
- **Yes, but I'll describe my own** → skip to Step 3 (add rules in plain language).
- **Just use the Well-Architected baseline for now** → scaffold the template as-is; they can add rules later.
- **Not now** → tell them how to start later ("just say 'add a rule: ...' or 'set up security rules' anytime").

## Step 2: Present the Pre-Built Rule Packs

Show the available packs (from `templates/rule-packs/`) and let the user pick any combination:

- **Baseline** — core Well-Architected essentials. Recommended for everyone.
- **Web Application** — input validation, authN/authZ, WAF, security headers.
- **Data Privacy** — PII handling, no-PII-in-logs, masking, retention.
- **PCI DSS** — cardholder data protection (PAN masking/encryption, no CVV storage).
- **Infrastructure as Code** — S3/SG/encryption defaults for Terraform/CFN/CDK.

Help them choose based on the project (ask a light question or two if useful: "Is this public-facing? Does it
handle personal or payment data? Is there Terraform/CloudFormation in the repo?"). Recommend **Baseline** plus
whatever matches.

**Then merge the chosen packs:**
1. Scaffold `.kiro/security-template.md` from `templates/security-template.md` if it doesn't exist.
2. Read each selected pack and merge its DP-*/SC-* entries into the matching sections of the template.
3. Renumber IDs so they are unique and sequential within the template (packs use their own prefixes; unify them).
4. Show the user the resulting ruleset summary (counts by category and severity).

## Step 3: Let Them Add Their Own Rules

Invite custom rules in plain language (see the manage-rules workflow):

> "Anything specific to your team? Just tell me, e.g., 'add a rule: no public S3 buckets' or 'we must never
> log PII'. You can also drop rough rules into the Quick Add box and I'll formalize them."

Formalize each rule they give (classify, ID, category, severity) and confirm.

## Step 4: Ask What Else to Track in the Daily Log

Beyond pass/fail constraints, ask what they want the daily review to explicitly watch and log:

> "Is there anything you always want the daily review to call out and log — even if it's not a hard rule? For
> example: new IAM permissions, new third-party dependencies, or changes to auth code."

Add each item to the template's **Custom Tracking / Watchlist** section. These get reported in every daily log.

## Step 5: Confirm Log Settings

Confirm where daily logs go (default `.kiro/security-reviews/daily-review-YYYY-MM-DD.md`) and that they're
always written. Adjust if the user wants a different location or filename.

## Step 6: Wrap Up and Commit

- Summarize what was created: the template location, packs applied, custom rules, and watchlist items.
- Recommend committing `.kiro/security-template.md` so the whole team shares the same ruleset:
  > "Commit `.kiro/security-template.md` to your repo so everyone reviews against the same rules."
- Tell them how to run a review ("run a security review" / "run the daily security review") and that they can
  change rules anytime in plain language.
- Optionally offer to create the one-click daily-review hook (see the daily-review workflow).

## Principles

- **No blank page.** Always offer pre-built packs so the user starts with real, useful rules.
- **Recommend, don't overwhelm.** Suggest Baseline + matching packs; keep questions light.
- **Plain language throughout.** Users describe rules and tracking items; Kiro does the formatting.
- **Make it shareable.** Nudge the user to commit the template so the team is aligned.
