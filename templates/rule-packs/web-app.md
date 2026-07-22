# Rule Pack: Web Application

For public-facing applications and APIs. Add on top of the Baseline pack.

## Design Principles
| ID  | Principle | Notes |
|-----|-----------|-------|
| DP-W1 | Treat all client input as untrusted. | |
| DP-W2 | Authenticate and authorize at the edge of the application. | |

## Security Constraints
### Application
| ID  | Constraint | Severity |
|-----|-----------|----------|
| SC-W1 | All user input is validated; database access uses parameterized queries. | BLOCKER |
| SC-W2 | Output is encoded to prevent XSS. | HIGH |
| SC-W3 | AuthN/AuthZ enforced on every non-public endpoint. | BLOCKER |
| SC-W4 | Security headers set (HSTS, CSP, X-Content-Type-Options). | MEDIUM |
| SC-W5 | Errors do not leak stack traces, secrets, or internal details to clients. | HIGH |
| SC-W6 | Rate limiting / throttling applied to public endpoints. | MEDIUM |

### Network & Infrastructure
| ID  | Constraint | Severity |
|-----|-----------|----------|
| SC-W7 | Public web endpoints sit behind WAF with managed rule groups. | HIGH |
| SC-W8 | No security group allows `0.0.0.0/0` inbound except intended public ports (80/443). | HIGH |
