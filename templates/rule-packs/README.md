# Rule Packs

Ready-made sets of security rules you can adopt as-is. Each pack is a curated list of design principles (DP-*)
and security constraints (SC-*) with sensible severities, drawn from the AWS Well-Architected Security Pillar
and common compliance needs.

**How to use them:**
- During setup, tell Kiro which packs you want (e.g., "add the baseline and web-app packs").
- Kiro merges the chosen packs into your `.kiro/security-template.md`, renumbering IDs so they're unique.
- You can then tweak, remove, or add rules in plain language ("remove SC-7", "add a rule: ...").
- Commit `.kiro/security-template.md` to your repo so the whole team shares the same ruleset.

**Available packs:**

| Pack | File | Best for |
|------|------|----------|
| Baseline | `baseline.md` | Every project — core Well-Architected essentials |
| Web Application | `web-app.md` | Public-facing apps and APIs |
| Data Privacy | `data-privacy.md` | Apps handling PII / sensitive data (GDPR-minded) |
| PCI DSS | `pci-dss.md` | Payment / cardholder data workloads |
| Infrastructure as Code | `iac.md` | Terraform / CloudFormation / CDK repos |

Packs are additive — combine as many as apply to your project.
