# Third Eye

A security watchdog for your codebase — a Kiro Power guided by the **AWS Well-Architected Framework Security Pillar**.

Third Eye reviews application code against your own custom rules (layered on the Well-Architected baseline)
and produces a **daily Red / Amber / Green review log**, so a human reviewer can keep up even when a team
ships fast with Kiro. Nothing risky merges unnoticed — and if a reviewer accepts a risk anyway, it's recorded.

## What's Inside

- **Security guidance** across the seven Well-Architected focus areas (identity, detection, infrastructure,
  data protection, incident response, application security, and foundations).
- **Custom rules** — define your own design principles and security constraints in plain language.
- **Daily reviewer summary** — a delta review of recent repo changes, rated Red/Amber/Green, written to a
  date-stamped audit log.
- **Rule packs** — ready-made rule sets (Baseline, Web App, Data Privacy, PCI DSS, Infrastructure as Code).
- **Guided onboarding** — Kiro walks you through setup on first use.

## Installation

### From this repository
1. Open the Kiro Powers panel.
2. Click **Add Custom Power** → **Git Repository**.
3. Enter this repository's URL and install.

### From a local copy
1. Clone or download this repository.
2. Open the Kiro Powers panel → **Add Custom Power** → **Local Directory**.
3. Point it at the repository folder (the one containing `POWER.md`).

## Getting Started

After installing, in a project workspace just say:

- "set up my security rules" — Kiro offers rule packs and scaffolds `.kiro/security-template.md`.
- "add a rule: no public S3 buckets" — add rules in plain language.
- "run the daily security review" — get a Red/Amber/Green summary and a dated log.

## Structure

```
POWER.md              # metadata + overview + onboarding
steering/             # focus-area guides + review/rule workflows
templates/
  security-template.md  # the fillable ruleset users copy into their project
  rule-packs/           # ready-made rule sets
```

## Author

Bharat Lohra, FDE @ Accenture
