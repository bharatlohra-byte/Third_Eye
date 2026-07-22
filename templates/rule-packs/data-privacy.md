# Rule Pack: Data Privacy

For applications handling PII or other sensitive personal data. Add on top of the Baseline pack.

## Design Principles
| ID  | Principle | Notes |
|-----|-----------|-------|
| DP-P1 | Collect and retain the minimum personal data necessary. | Data minimization. |
| DP-P2 | Keep people away from raw personal data; access is exceptional, logged, and time-boxed. | |

## Security Constraints
### Data Protection
| ID  | Constraint | Severity |
|-----|-----------|----------|
| SC-P1 | PII is never written to application logs, traces, or error messages. | BLOCKER |
| SC-P2 | Sensitive data is classified/tagged and encrypted with a customer-managed KMS key. | HIGH |
| SC-P3 | PII fields are masked or tokenized in non-production environments. | HIGH |
| SC-P4 | Data retention and deletion policy is enforced (no indefinite PII storage). | MEDIUM |
| SC-P5 | Access to personal data is logged and auditable. | HIGH |
| SC-P6 | Personal data is not sent to third parties/analytics without a documented lawful basis. | HIGH |
