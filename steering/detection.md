# Detection

Gain visibility into what is happening across your environment so you can identify misconfigurations, threats, and unexpected behavior, and act on them. Detection supports the "maintain traceability" design principle.

## Two Goals of Detection

1. **Understand and audit** — capture a complete, tamper-evident record of activity for investigation and compliance.
2. **Detect and respond** — turn that data into real-time alerts and automated actions.

## Foundational Logging

**AWS CloudTrail** — records API activity across your account(s).
- Enable an **organization trail** covering all accounts and all regions.
- Turn on **log file validation** to detect tampering.
- Deliver logs to a dedicated, locked-down **log-archive account** with restrictive bucket policies and object lock.
- Capture management events always; enable data events for sensitive resources (specific S3 buckets, Lambda) where the volume is justified.

**Other essential log sources:**
- **VPC Flow Logs** — network traffic metadata for investigation and anomaly detection.
- **Amazon S3 access logs / CloudFront logs** — object and edge access.
- **Application and OS logs** — shipped to **CloudWatch Logs** via the CloudWatch agent.
- **DNS query logs** — via Route 53 Resolver query logging.

## Configuration and Compliance Tracking

**AWS Config** — records resource configuration and changes over time.
- Enable across the organization.
- Use **managed and custom Config rules** to continuously evaluate compliance (e.g., "S3 buckets must not be public," "EBS volumes must be encrypted").
- Deploy **conformance packs** to apply a set of rules mapped to a framework (CIS, PCI, or your own baseline).
- Use auto-remediation to fix drift automatically where safe.

## Threat Detection Services

**Amazon GuardDuty** — continuous, ML-based threat detection.
- Analyzes CloudTrail, VPC Flow Logs, and DNS logs (plus optional S3, EKS, RDS, Lambda, and malware protection).
- Enable organization-wide with a delegated administrator in the security account.
- Findings surface compromised credentials, crypto-mining, reconnaissance, and data exfiltration signals.

**AWS Security Hub** — aggregates and prioritizes findings.
- Central pane of glass across GuardDuty, Inspector, Macie, Config, and partner tools.
- Runs automated security standard checks (AWS Foundational Security Best Practices, CIS, PCI DSS).
- Normalizes findings to a common format and supports automated response via EventBridge.

**Amazon Detective** — investigation and root-cause analysis.
- Builds linked behavior graphs from log data to speed up "what happened and how far did it spread" analysis.

**Amazon Inspector** — automated vulnerability management for EC2, ECR container images, and Lambda.

## From Signal to Action

Detection is only useful if it drives response:
- Route findings through **Amazon EventBridge** to trigger automated workflows.
- Use **Lambda** or **Systems Manager Automation** runbooks to isolate, snapshot, or remediate.
- Send prioritized alerts to on-call via SNS / chat integrations — avoid alert fatigue by filtering to actionable severity.
- Define which findings auto-remediate vs. require human review.

**Example: alert on root account usage (CloudWatch metric filter concept)**
```
Filter pattern on CloudTrail logs:
{ $.userIdentity.type = "Root" && $.userIdentity.invokedBy NOT EXISTS && $.eventType != "AwsServiceEvent" }
-> CloudWatch alarm -> SNS notification to security team
```

## Detection Checklist

- [ ] Organization CloudTrail enabled, all regions, log validation on, logs in a locked archive account.
- [ ] VPC Flow Logs and key application/OS logs shipped to CloudWatch.
- [ ] AWS Config enabled org-wide with conformance packs for your baseline.
- [ ] GuardDuty enabled organization-wide with delegated admin.
- [ ] Security Hub aggregating findings and running standard checks.
- [ ] Inspector scanning compute and container images for vulnerabilities.
- [ ] Findings routed through EventBridge to alerting and automated remediation.
- [ ] Alerts tuned to actionable severity to prevent fatigue.

## References
- Security Pillar — Detection: https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/detection.html
- GuardDuty: https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html
- Security Hub: https://docs.aws.amazon.com/securityhub/latest/userguide/what-is-securityhub.html
