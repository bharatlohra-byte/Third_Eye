---
name: "third-eye"
displayName: "Third Eye"
description: "A security watchdog for your codebase, guided by the AWS Well-Architected Framework Security Pillar. Reviews application code against your custom rules and the WAF baseline, and produces a daily Red/Amber/Green review log so nothing risky merges unnoticed."
keywords: ["third eye", "security review", "code review", "aws security", "well-architected", "iam", "encryption", "daily review log", "cloud security", "secure code"]
author: "Bharat Lohra, FDE @ Accenture"
---

# Third Eye

## Overview

**Third Eye** is a security watchdog for your codebase. It packages security best practices grounded in the **AWS Well-Architected Framework (WAF) Security Pillar** and puts them to work: reviewing architectures and code, checking changes against your own custom rules, and producing a daily Red/Amber/Green review log so a human reviewer can keep up even when a team ships fast with Kiro. It gives Kiro the guidance needed to review architectures, write infrastructure and application code, and answer security questions in a way that aligns with AWS-recommended patterns.

The Security Pillar describes how to protect data, systems, and assets to take advantage of cloud technologies to improve your security posture. It is organized around a set of design principles and seven focus areas. This power turns those principles into actionable guidance, decision checklists, and concrete AWS service recommendations so that security is applied consistently rather than as an afterthought.

Use this power when designing new systems, reviewing existing ones, hardening infrastructure-as-code, threat-modeling a feature, or answering "is this secure and how do I make it better?" questions. While the framework is AWS-centric, most principles (least privilege, defense in depth, encryption everywhere, traceability) transfer to any cloud or on-premises environment.

## The Seven Security Design Principles

These principles underpin every recommendation in this power:

1. **Implement a strong identity foundation** — Enforce least privilege and separation of duties with appropriate authorization. Centralize identity management and eliminate reliance on long-term static credentials.
2. **Maintain traceability** — Monitor, alert, and audit actions and changes in real time. Integrate logs and metrics with systems that automatically investigate and take action.
3. **Apply security at all layers** — Use defense in depth. Apply controls to the network edge, VPC, load balancing, every compute instance, operating system, application, and code.
4. **Automate security best practices** — Automated, software-based mechanisms improve your ability to securely scale rapidly and cost-effectively. Build security into templates and pipelines.
5. **Protect data in transit and at rest** — Classify data by sensitivity and use mechanisms such as encryption, tokenization, and access control where appropriate.
6. **Keep people away from data** — Reduce or eliminate direct access and manual processing of data to lower the risk of mishandling, modification, and human error.
7. **Prepare for security events** — Have an incident management and investigation process aligned to your requirements. Run simulations and use tooling with automation to increase detection, investigation, and recovery speed.

## The Seven Focus Areas

The framework organizes security work into these areas. Each has a dedicated steering file with detailed best practices, AWS service recommendations, and examples.

## Onboarding (First Use)

When this power is first used in a workspace and no `.kiro/security-template.md` exists yet — or when the user
asks to "set up" or "configure security rules" — start the **getting-started** onboarding flow. Kiro should:

1. Offer to set up the user's security rules (so they don't face a blank file).
2. Present the pre-built **rule packs** (Baseline, Web Application, Data Privacy, PCI DSS, Infrastructure as
   Code) and merge the ones the user picks into `.kiro/security-template.md`.
3. Invite additional custom rules in plain language.
4. Ask what else the user wants the daily review to explicitly track and log (the Watchlist).
5. Confirm log settings and recommend committing the template so the whole team shares one ruleset.

See the **getting-started** steering file for the full script.

## Available Steering Files

Read these on demand based on the task at hand:

- **security-foundations** — Governance, account structure, shared responsibility model, operating model, and organizational controls (AWS Organizations, Control Tower, SCPs).
- **identity-access-management** — Human and machine identity, least-privilege permissions, federation, temporary credentials, and IAM policy patterns.
- **detection** — Logging, monitoring, threat detection, and audit (CloudTrail, GuardDuty, Security Hub, Config, CloudWatch, Detective).
- **infrastructure-protection** — Network and compute protection, defense in depth, VPC design, edge protection (WAF, Shield, Network Firewall, Systems Manager).
- **data-protection** — Data classification, encryption at rest and in transit, key and secrets management (KMS, Secrets Manager, Macie, ACM).
- **incident-response** — Preparation, simulation, iteration, forensics, and automated response for security events.
- **application-security** — Secure SDLC, dependency and code scanning, secrets hygiene, and securing the build/deploy pipeline.
- **getting-started** — First-run onboarding: offers pre-built rule packs, gathers custom rules, and sets up the daily review log and watchlist.
- **manage-rules** — Lets users add, edit, remove, or list their custom rules in plain language; Kiro handles IDs, categories, and severities so no one edits markdown tables by hand.
- **custom-review** — Workflow for reviewing application code against a user-defined security template (their own design principles and constraints) and generating a structured summary report.
- **daily-review** — Reviewer-focused workflow that summarizes recent repo changes and rates each Red/Amber/Green against the security template, so a human reviewer can keep up with AI-assisted change volume.

Call action "readSteering" with the relevant file name to load detailed guidance for the area you're working in.

## Custom Security Review

Beyond the reference guidance, this power can actively **review your code against your own rules**. Users
define their design principles and security constraints in a fillable template, and Kiro checks the codebase
against them (layered on top of the Well-Architected baseline) and produces a findings report.

**To use it:**
1. Copy `templates/security-template.md` into your project as `.kiro/security-template.md` (Kiro will scaffold
   it for you automatically if it's missing).
2. Add your rules the easy way — just tell Kiro **"add a rule: no public S3 buckets"** (or jot rough rules in
   the template's **Quick Add** box and say "process quick add"). Kiro assigns the ID, category, and severity;
   you never edit the tables by hand. You can also "list our rules", "change SC-4 to a BLOCKER", or "remove SC-7".
   See the **manage-rules** steering file.
3. Ask Kiro to "run a security review" — it reads the template, reviews in-scope code, and returns a
   status per constraint (PASS/FAIL/PARTIAL/NEEDS REVIEW) with cited evidence, remediation, and an overall verdict.

If no template exists, Kiro will offer to scaffold one. See the **custom-review** steering file for the full workflow.

## Daily Reviewer Summary (Red / Amber / Green)

When a team ships code with Kiro, change volume rises and manual review gets hard. This power gives a
**human reviewer** a daily summary of what changed in the repository, rated **🔴 Red / 🟠 Amber / 🟢 Green**
against the security template:

- 🔴 **Red** — a BLOCKER/HIGH constraint is violated; should not merge without action.
- 🟠 **Amber** — a minor violation, partial pass, or something that needs human review.
- 🟢 **Green** — no violations found in the change.

The power always reports the rating and calls out risky changes with cited evidence. The reviewer stays in
control — they may still accept (pass) an item — but nothing risky merges silently. **Every review is written
to a date-stamped log file** (default: `.kiro/security-reviews/daily-review-YYYY-MM-DD.md`) so there is a
persistent audit trail of what was flagged and what was accepted.

**To use it:** ask Kiro to "run the daily security review." It reads `.kiro/security-template.md`, inspects
repo changes in the configured window (default: last 24h), returns a RAG summary, and writes the dated log
file. It can also be wired to a one-click `userTriggered` hook. See the **daily-review** steering file for the
full workflow.

## How to Use This Power

**When reviewing an architecture or design:**
1. Start with the seven design principles as a lens.
2. Walk the focus areas most relevant to the workload (identity and data protection apply almost always).
3. Read the matching steering file(s) for detailed checks and service recommendations.
4. Flag gaps against least privilege, encryption, traceability, and blast-radius reduction.

**When writing infrastructure-as-code:**
1. Default to encryption enabled, public access blocked, and scoped IAM roles.
2. Pull the relevant steering file (e.g., data-protection for S3/RDS, infrastructure-protection for VPC/security groups).
3. Prefer automated guardrails (SCPs, Config rules) over manual review.

**When answering a security question:**
1. Identify which focus area the question maps to.
2. Read that steering file and give a principle-backed answer with a concrete AWS recommendation.

## Quick-Reference Security Checklist

A fast triage list. Deeper checks live in the focus-area steering files.

- [ ] Root user has MFA, no access keys, and is not used for daily operations.
- [ ] Identity is centralized (IAM Identity Center / federation); no shared or long-lived IAM user keys.
- [ ] Permissions follow least privilege; wildcards (`*`) in actions/resources are justified.
- [ ] CloudTrail is enabled in all regions with log file validation and delivered to a locked-down account.
- [ ] GuardDuty and Security Hub are enabled organization-wide.
- [ ] All data stores encrypt at rest with KMS; TLS enforced in transit.
- [ ] S3 Block Public Access is on at account and bucket level unless there is an explicit, reviewed reason.
- [ ] Secrets are in Secrets Manager / SSM Parameter Store, never in code or plaintext env files.
- [ ] Network follows defense in depth: private subnets, least-open security groups, WAF/Shield at the edge.
- [ ] There is a documented, rehearsed incident response runbook with defined roles and access.
- [ ] Guardrails are automated via AWS Organizations SCPs and AWS Config conformance packs.

## Best Practices for Applying This Power

- **Lead with principles, back with services.** Explain the "why" (design principle) then the "how" (specific AWS control).
- **Default to secure.** When generating code or config, choose the secure option by default and call out any deviation.
- **Reduce blast radius.** Prefer smaller accounts, scoped roles, and segmented networks over broad, convenient access.
- **Automate over document.** A guardrail that enforces a rule beats a wiki page describing it.
- **Never invent credentials or secrets.** Use placeholders and point to Secrets Manager / Parameter Store.

## References

- AWS Well-Architected Framework — Security Pillar: https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html
- AWS Well-Architected Framework (overview): https://aws.amazon.com/architecture/well-architected/
- AWS Security Best Practices / Prescriptive Guidance: https://aws.amazon.com/architecture/security-identity-compliance/

Content synthesized from the public AWS Well-Architected Framework documentation and rephrased for compliance with licensing restrictions.
