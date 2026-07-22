# Identity and Access Management

Controlling who (human) and what (machine) can do which actions on which resources, under which conditions. This is the highest-leverage security area — most breaches trace back to identity mistakes.

## Two Kinds of Identity

- **Human identities** — administrators, developers, operators, and end users who interact with your resources.
- **Machine identities** — services, applications, and workloads that need to call AWS APIs (EC2 instances, Lambda functions, containers, CI/CD).

Both should use **temporary credentials** wherever possible and follow least privilege.

## Human Identity Best Practices

**Centralize identity with federation.** Do not create individual IAM users per person. Instead:
- Use **AWS IAM Identity Center** (successor to AWS SSO) as the front door for workforce access across accounts.
- Federate from your existing identity provider (Okta, Entra ID, Google Workspace) via SAML/OIDC.
- Users assume roles that grant temporary credentials — no long-lived keys to leak.

**Enforce strong sign-in:**
- Require MFA for all human access, ideally phishing-resistant (FIDO2/WebAuthn).
- Apply strong password policies where passwords still exist.
- Use permission sets in Identity Center to map groups to scoped roles per account.

## Machine Identity Best Practices

**Never embed long-term keys in code or config.** Instead:
- Attach **IAM roles** to compute: instance profiles for EC2, execution roles for Lambda, task roles for ECS, IRSA for EKS.
- Use **IAM Roles Anywhere** for workloads outside AWS that need AWS access via X.509 certificates.
- Rotate any unavoidable static credentials automatically and store them in Secrets Manager.

**Detect and eliminate static credentials:**
- Use IAM Access Analyzer and credential reports to find unused users and old keys.
- Alert on creation of new long-lived access keys.

## Least Privilege Permissions

Grant only the permissions required to perform a task, and no more.

**Techniques:**
- **Start minimal, add as needed.** Begin with no permissions and grant specific actions/resources.
- **Use IAM Access Analyzer policy generation** to create policies from observed CloudTrail activity.
- **Scope resources.** Avoid `"Resource": "*"` unless the action genuinely requires it.
- **Use condition keys** to constrain by source IP, VPC, tag, region, or MFA presence.
- **Apply permission boundaries** to cap the maximum permissions a role/user can have, even if their policy grants more.
- **Prefer managed guardrails** — SCPs at the org level, permission boundaries at the account level.

**Example: scoped, condition-constrained policy**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:PutObject"],
      "Resource": "arn:aws:s3:::app-data-prod/${aws:PrincipalTag/team}/*",
      "Condition": {
        "Bool": { "aws:SecureTransport": "true" },
        "StringEquals": { "aws:RequestedRegion": "us-east-1" }
      }
    }
  ]
}
```

## Separation of Duties

- Split powerful capabilities across roles so no single identity can act unchecked (e.g., the role that deploys is not the role that approves).
- Require different principals for sensitive combinations (create keys vs. use keys; write logs vs. delete logs).
- Use SCPs and permission boundaries to make separation enforceable, not just procedural.

## Managing Access Continuously

Access is not "set once." Review and reduce over time:
- Run periodic access reviews; remove unused permissions and identities.
- Use **IAM Access Analyzer** to detect resources shared externally (public S3, cross-account roles) and validate policies against best practices.
- Analyze **last-accessed data** to prune unused service permissions.
- Automate deprovisioning when people or workloads are decommissioned.

## Common Anti-Patterns to Flag

- Long-lived IAM user access keys embedded in apps or CI.
- `Action: "*"` with `Resource: "*"` outside of a deliberately-scoped admin role.
- Shared credentials used by multiple people or services.
- Root user access keys.
- Cross-account `sts:AssumeRole` trust with no external ID or condition.
- Missing MFA on privileged human access.

## IAM Checklist

- [ ] Workforce access via IAM Identity Center / federation, not individual IAM users.
- [ ] MFA enforced for all human access (phishing-resistant where possible).
- [ ] Compute uses IAM roles; no static keys in code or config.
- [ ] Policies scoped to specific actions and resources; wildcards justified.
- [ ] Permission boundaries and SCPs cap maximum permissions.
- [ ] Condition keys constrain access (MFA, source, region, tags).
- [ ] Access Analyzer runs to find external sharing and unused access.
- [ ] Regular access reviews and automated deprovisioning in place.

## References
- Security Pillar — Identity and Access Management: https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/identity-and-access-management.html
- IAM best practices: https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html
