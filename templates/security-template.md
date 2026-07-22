# Security Review Template

Copy this file into your project (recommended location: `.kiro/security-template.md`) and fill in the
sections below. Kiro uses it as the ruleset when you ask for a security review — it checks your
application code against **your** design principles and constraints, on top of the AWS Well-Architected
Security baseline.

Delete the example entries and replace them with your own. Leave a section empty to fall back to the
Well-Architected defaults for that area.

> **Adding rules is easy — you don't have to edit the tables by hand.** Just tell Kiro
> `add a rule: <your rule in plain language>` and it will classify it, assign an ID, pick a category and
> severity, and drop it in the right place. Or jot rough rules in the **Quick Add** box below and ask Kiro to
> "process quick add." You can also say "list our rules", "change SC-4 to a BLOCKER", or "remove SC-7".

---

## Project Context

<!-- Helps Kiro tailor the review. -->

- **Application name:** <e.g., Payments API>
- **Environment(s):** <e.g., prod, staging>
- **Tech stack:** <e.g., Node.js + TypeScript, Terraform, PostgreSQL on RDS>
- **Data sensitivity:** <e.g., handles PII and cardholder data — PCI DSS scope>
- **Compliance targets:** <e.g., PCI DSS, SOC 2, HIPAA, none>

---

## Design Principles

<!--
List the security design principles this project must uphold. These are the "why" that guides review.
Each principle should be a short, checkable statement.
-->

| ID  | Principle | Notes |
|-----|-----------|-------|
| DP-1 | Least privilege everywhere — every identity and role grants only what it needs. | |
| DP-2 | Encrypt all data at rest and in transit. | TLS 1.2+ only. |
| DP-3 | No long-lived credentials in code or config. | Use roles / secrets manager. |
| DP-4 | Defense in depth — no single control is the only barrier. | |
| DP-5 | Fail securely — errors must not leak data or grant access. | |
| <add> | | |

---

## Quick Add — just write your rules here

<!--
Easiest way to add rules: type them in plain language below, one per line. You don't need to pick an ID,
category, or severity. Then tell Kiro "process quick add" (or just ask for a review) and Kiro will formalize
each line into the tables below — assigning IDs, categories, and severities — and clear this list.

You can also just say to Kiro: "add a rule: <your rule>" and skip this entirely.
-->

```
# e.g.
# no public S3 buckets
# every Lambda must set a timeout
# we must never log PII
```

---

## Security Constraints

<!--
Hard rules the code MUST satisfy. Constraints are pass/fail. Group them however suits your project.
Use severity: BLOCKER (must fix before ship), HIGH, MEDIUM, LOW.

You normally don't edit these tables by hand — say "add a rule: ..." to Kiro and it fills them in for you.
-->

### Identity & Access
| ID  | Constraint | Severity |
|-----|-----------|----------|
| SC-1 | No IAM policy may use `Action: "*"` with `Resource: "*"`. | BLOCKER |
| SC-2 | All human access is federated via SSO; no IAM user access keys. | HIGH |
| <add> | | |

### Data Protection
| ID  | Constraint | Severity |
|-----|-----------|----------|
| SC-3 | All S3 buckets have Block Public Access enabled. | BLOCKER |
| SC-4 | Databases and volumes are encrypted with a customer-managed KMS key. | HIGH |
| SC-5 | Secrets are read from Secrets Manager / SSM at runtime, never hardcoded. | BLOCKER |
| <add> | | |

### Network & Infrastructure
| ID  | Constraint | Severity |
|-----|-----------|----------|
| SC-6 | No security group allows `0.0.0.0/0` inbound except intended public endpoints. | HIGH |
| SC-7 | Workloads run in private subnets unless explicitly public-facing. | MEDIUM |
| <add> | | |

### Application
| ID  | Constraint | Severity |
|-----|-----------|----------|
| SC-8 | All user input is validated; database access uses parameterized queries. | BLOCKER |
| SC-9 | No secrets, tokens, or keys committed to the repository. | BLOCKER |
| SC-10 | Dependencies are pinned and free of known critical vulnerabilities. | HIGH |
| <add> | | |

---

## Scope

<!-- Optional: narrow or widen what Kiro reviews. -->

- **Include:** <e.g., src/**, infra/**, .github/workflows/**>
- **Exclude:** <e.g., tests/**, generated/**, node_modules/**>

---

## Review Output Preferences

<!-- Optional: how you want the summary. -->

- **Format:** <table | narrative | both>  (default: both)
- **Fail the review if:** <e.g., any BLOCKER only | any BLOCKER or HIGH>  (default: any BLOCKER or HIGH)
- **Include remediation suggestions:** <yes | no>  (default: yes)

<!--
The default (BLOCKER or HIGH fails) matches the daily 🔴 Red rating, so a full review and the daily summary
agree on what counts as "must fix". Set to "any BLOCKER only" if you want HIGH issues to warn without failing.
-->


---

## RAG Rating Rules

<!--
Red / Amber / Green ratings drive the daily reviewer summary. Adjust the mapping if your team
uses different thresholds. These map constraint severities + statuses to a traffic-light.
-->

- 🔴 **Red** — one or more BLOCKER or HIGH constraints violated. Should not merge without action.
- 🟠 **Amber** — MEDIUM/LOW violation, a PARTIAL pass, or anything marked NEEDS REVIEW. Reviewer judgment required.
- 🟢 **Green** — no constraint violations found in the change.

> The power always reports the rating. A reviewer may still choose to accept (pass) an Amber or Red
> item — but the risk has been called out and recorded in the summary.

---

## Daily Review Settings

<!-- Controls the daily reviewer summary. Optional — sensible defaults apply. -->

- **Base for comparison:** <e.g., changes since last review | last 24h | vs main branch>  (default: commits in the last 24h)
- **Branches to include:** <e.g., all | main, develop | feature/*>  (default: all active branches)
- **Group summary by:** <author | file area | pull request | commit>  (default: author, then file area)

### Daily Review Log (required)

<!--
Every daily review is ALWAYS written to a date-stamped log file (in addition to being shown in chat).
This creates a persistent audit trail of what was flagged and what was accepted.
-->

- **Log directory:** <e.g., .kiro/security-reviews/>  (default: `.kiro/security-reviews/`)
- **Log filename pattern:** <must include the date>  (default: `daily-review-YYYY-MM-DD.md`)
- **If a log for today already exists:** <overwrite | append a new run section>  (default: append a new run section with a timestamp)

---

## Custom Tracking / Watchlist

<!--
Anything extra you want the daily review to explicitly track and log — beyond pass/fail of the constraints.
Write these in plain language; Kiro will report on each one in every daily log so nothing you care about
slips by unnoticed. You can also just tell Kiro "also track <X> in the daily review".
-->

| ID  | Track this | Why it matters |
|-----|-----------|----------------|
| TR-1 | New or changed IAM roles, policies, and permissions. | High-impact access changes. |
| TR-2 | New third-party dependencies added. | Supply-chain risk. |
| TR-3 | Changes to authentication or authorization code. | Access-control regressions. |
| <add> | | |
