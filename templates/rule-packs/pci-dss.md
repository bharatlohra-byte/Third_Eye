# Rule Pack: PCI DSS

For workloads that store, process, or transmit cardholder data. Add on top of the Baseline and Data Privacy
packs. This is a practical starting set, not a substitute for a formal PCI DSS assessment.

## Design Principles
| ID  | Principle | Notes |
|-----|-----------|-------|
| DP-C1 | Minimize the cardholder data environment (CDE) and its scope. | |
| DP-C2 | Never store sensitive authentication data after authorization. | CVV, full track, PIN. |

## Security Constraints
### Data Protection
| ID  | Constraint | Severity |
|-----|-----------|----------|
| SC-C1 | The primary account number (PAN) is masked when displayed (max first6/last4). | BLOCKER |
| SC-C2 | PAN is rendered unreadable at rest (strong encryption or tokenization). | BLOCKER |
| SC-C3 | Sensitive authentication data (CVV, track, PIN) is never persisted. | BLOCKER |
| SC-C4 | Cardholder data is only transmitted over strong TLS; never over open/plaintext channels. | BLOCKER |

### Identity & Access
| ID  | Constraint | Severity |
|-----|-----------|----------|
| SC-C5 | Access to cardholder data is restricted by role on a need-to-know basis. | HIGH |
| SC-C6 | All access to cardholder data and audit trails is logged. | HIGH |

### Detection
| ID  | Constraint | Severity |
|-----|-----------|----------|
| SC-C7 | Audit logs for the CDE are retained per policy and protected from tampering. | HIGH |
