# Rule Pack: Baseline

Core essentials from the AWS Well-Architected Security Pillar. Recommended for every project.

## Design Principles
| ID  | Principle | Notes |
|-----|-----------|-------|
| DP-1 | Enforce least privilege for every human and machine identity. | |
| DP-2 | Encrypt all data at rest and in transit (TLS 1.2+). | |
| DP-3 | Use no long-lived static credentials; prefer roles and short-lived tokens. | |
| DP-4 | Apply defense in depth — never rely on a single control. | |
| DP-5 | Maintain traceability — log and monitor security-relevant actions. | |

## Security Constraints
### Identity & Access
| ID  | Constraint | Severity |
|-----|-----------|----------|
| SC-1 | No IAM policy uses `Action: "*"` with `Resource: "*"`. | BLOCKER |
| SC-2 | No IAM user long-lived access keys in code or config. | BLOCKER |
| SC-3 | MFA required for privileged human access. | HIGH |

### Data Protection
| ID  | Constraint | Severity |
|-----|-----------|----------|
| SC-4 | Data stores (S3, RDS, EBS, DynamoDB) encrypt at rest. | HIGH |
| SC-5 | Secrets come from Secrets Manager / SSM at runtime, never hardcoded. | BLOCKER |
| SC-6 | TLS enforced for data in transit; plaintext transport denied. | HIGH |

### Detection
| ID  | Constraint | Severity |
|-----|-----------|----------|
| SC-7 | CloudTrail logging is enabled and not disabled by app code/roles. | HIGH |

### Application
| ID  | Constraint | Severity |
|-----|-----------|----------|
| SC-8 | No secrets, tokens, or keys committed to the repository. | BLOCKER |
| SC-9 | Dependencies are pinned and free of known critical vulnerabilities. | HIGH |
