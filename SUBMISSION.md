# Third Eye — Submission Details

Draft copy for the Kiro power submission form (https://kiro.dev/powers/submit/).
Not part of the power itself — this file is a convenience reference.

## Name
Third Eye

## Identifier
third-eye

## Repository
https://github.com/bharatlohra-byte/Third_Eye

## Author
Bharat Lohra, FDE @ Accenture

## Short description (one line)
A security watchdog for your codebase, guided by the AWS Well-Architected Framework Security Pillar.

## Description (2–3 sentences)
Third Eye reviews your application code and infrastructure against your own custom security rules, layered on
the AWS Well-Architected Security baseline. It produces a daily Red/Amber/Green review log with a dated audit
trail, so a human reviewer can keep up even when a team ships fast with Kiro. Rules are added in plain
language and it ships with ready-made rule packs and guided onboarding.

## Keywords
third eye, security review, code review, aws security, well-architected, iam, encryption, daily review log,
cloud security, secure code

## Category
Security / Code Review

## Problem it solves
When a team generates code quickly with an AI assistant, review volume grows and it becomes hard for a human
reviewer to stay on top of what changed and whether it is safe. Third Eye gives that reviewer a fast, daily,
risk-rated picture of repository changes measured against the team's own rules — and records what was flagged
and what was accepted.

## Key features
- Reviews source code and IaC (no cloud credentials or MCP required — pure knowledge-base power).
- User-defined design principles and security constraints, added in plain language.
- Daily delta review of repo changes rated Red / Amber / Green.
- Mandatory date-stamped review log as an audit trail.
- Ready-made rule packs: Baseline, Web Application, Data Privacy, PCI DSS, Infrastructure as Code.
- Guided first-run onboarding; optional one-click daily-review hook.
- Full AWS Well-Architected Security Pillar reference across seven focus areas.

## How it differs from existing powers
Existing Well-Architected powers assess a live AWS account via MCP. Third Eye instead reviews the codebase
against custom, project-specific rules and produces an ongoing, logged reviewer summary — complementary to,
not overlapping with, account-assessment tools.

## Example prompts
- "Set up my security rules."
- "Add a rule: no public S3 buckets."
- "Run a security review of this service."
- "Run the daily security review and log it."
- "List our current security rules."

## Icon
icon-512.png (512x512) included in the repository.

## License
MIT
