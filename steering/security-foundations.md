# Security Foundations

Governance and organizational controls that everything else builds on. Get these right before optimizing individual workloads.

## Shared Responsibility Model

Security in AWS is shared between AWS and the customer:

- **AWS is responsible for security *of* the cloud** — the hardware, global infrastructure, and managed-service software.
- **The customer is responsible for security *in* the cloud** — configuration, identity, data, patching of self-managed resources, and network rules.

The line shifts by service model. With EC2 you own OS patching and network config; with Lambda or S3 AWS handles more of the stack, but you still own IAM, data classification, and access policies. Always confirm which side owns a given control before assuming it is handled.

## Multi-Account Strategy

A single account does not scale securely. Use multiple accounts to create hard security and billing boundaries.

**Recommended approach:**
- Use **AWS Organizations** to centrally manage accounts and apply policies.
- Use **AWS Control Tower** to stand up a well-architected, multi-account landing zone with guardrails pre-configured.
- Separate workloads by environment (prod, staging, dev) and by blast radius (a compromised dev account should not touch prod).
- Maintain dedicated accounts for shared concerns: a **log archive** account (immutable audit logs), a **security tooling** account (GuardDuty/Security Hub admin), and a **network** account (shared transit).

**Why:** Account boundaries are the strongest isolation AWS offers. They limit blast radius, simplify least privilege, and make billing and audit attribution clean.

## Service Control Policies (SCPs)

SCPs are organization-level guardrails that set the maximum available permissions for accounts. They do not grant permissions; they restrict what IAM in member accounts can allow.

**Common guardrails to enforce with SCPs:**
- Deny disabling of CloudTrail, GuardDuty, or Config.
- Deny use of the root user for actions.
- Restrict resource creation to approved regions.
- Deny deletion of security/logging resources by non-security roles.
- Require encryption or block public S3 access org-wide.

**Example: prevent members from stopping CloudTrail**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyCloudTrailTampering",
      "Effect": "Deny",
      "Action": [
        "cloudtrail:StopLogging",
        "cloudtrail:DeleteTrail",
        "cloudtrail:UpdateTrail"
      ],
      "Resource": "*"
    }
  ]
}
```

## Operating Model and Governance

- **Define ownership.** Every account and workload needs a named owner responsible for its security posture.
- **Establish a security baseline.** Codify required controls (encryption, logging, tagging) and enforce them with automation.
- **Tag consistently.** Use tags for data classification, ownership, environment, and cost. Tags drive automated policy and detection.
- **Stay informed of threats.** Track AWS Security Bulletins and subscribe to AWS Health for account-specific issues.
- **Reduce manual effort.** Prefer AWS-managed services and automated guardrails over custom, manually-operated controls.

## Root User Protection

The root user has unrestricted access and cannot be limited by IAM. Treat it as break-glass only.

- Enable MFA (prefer a hardware token stored securely).
- Remove all root access keys.
- Do not use root for day-to-day work; create administrative roles instead.
- Set up alerting on any root sign-in or activity.
- In AWS Organizations, centrally manage root credentials for member accounts where supported.

## Foundational Checklist

- [ ] AWS Organizations (ideally Control Tower) in place with a landing zone.
- [ ] Dedicated log-archive and security-tooling accounts.
- [ ] Workloads separated by environment and blast radius.
- [ ] SCPs enforce baseline guardrails (protect logging, restrict regions, block root).
- [ ] Root user secured (MFA on, keys removed, alerting enabled).
- [ ] Consistent tagging strategy applied and enforced.

## References
- Security Pillar — Security Foundations: https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/security-foundations.html
- AWS Organizations SCPs: https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html
