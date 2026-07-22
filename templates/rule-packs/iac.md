# Rule Pack: Infrastructure as Code

For repositories with Terraform, CloudFormation, or CDK. Add on top of the Baseline pack.

## Design Principles
| ID  | Principle | Notes |
|-----|-----------|-------|
| DP-I1 | Security controls are declared in code and enforced in the pipeline, not applied by hand. | |
| DP-I2 | Default to closed — resources are private and encrypted unless explicitly opened. | |

## Security Constraints
### Data Protection
| ID  | Constraint | Severity |
|-----|-----------|----------|
| SC-I1 | S3 buckets set Block Public Access and default encryption. | BLOCKER |
| SC-I2 | Databases, volumes, and snapshots declare encryption at rest. | HIGH |

### Network & Infrastructure
| ID  | Constraint | Severity |
|-----|-----------|----------|
| SC-I3 | No security group rule allows `0.0.0.0/0` to sensitive ports (22, 3389, 3306, 5432). | BLOCKER |
| SC-I4 | Compute runs in private subnets unless explicitly public-facing. | MEDIUM |

### Identity & Access
| ID  | Constraint | Severity |
|-----|-----------|----------|
| SC-I5 | IAM roles/policies in IaC avoid wildcard actions and resources. | HIGH |

### Application (pipeline)
| ID  | Constraint | Severity |
|-----|-----------|----------|
| SC-I6 | IaC is scanned (e.g., checkov / cfn-guard) and no secrets are hardcoded in templates or tfvars. | HIGH |
| SC-I7 | Remote state is encrypted and access-controlled; state is never committed to the repo. | HIGH |
