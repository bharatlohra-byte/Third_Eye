# Data Protection

Classify data by sensitivity and protect it in transit and at rest, while keeping people away from direct data access. Covers the "protect data" and "keep people away from data" design principles.

## Data Classification

You cannot protect data appropriately until you know how sensitive it is.

- Define classification levels (e.g., public, internal, confidential, restricted).
- Tag resources and data stores with their classification so automated policy can act on it.
- Let classification drive controls: encryption requirements, allowed regions, retention, and who/what may access it.
- Use **Amazon Macie** to automatically discover and classify sensitive data (PII, credentials, financial data) in S3.

## Encryption at Rest

Encrypt everything at rest by default. Nearly all AWS storage and database services integrate with **AWS KMS**.

- **S3** — enable default bucket encryption (SSE-KMS for sensitive data). Combine with Block Public Access.
- **EBS** — enable account-level EBS encryption by default so all new volumes are encrypted.
- **RDS / Aurora / DynamoDB / Redshift** — enable encryption at creation (it generally cannot be added in place later).
- **Backups and snapshots** inherit or require encryption — verify they are covered.

**Key management with AWS KMS:**
- Prefer **customer managed keys (CMKs)** for sensitive workloads so you control key policies, rotation, and access.
- Enable **automatic annual key rotation**.
- Restrict key usage with **key policies** and grants; separate the roles that use a key from those that administer it.
- Use **CloudTrail** to audit every key use.
- For strict compliance needs, use **CloudHSM** or KMS custom key stores.

## Encryption in Transit

Encrypt data moving between clients, services, and AWS.

- Enforce **TLS 1.2+** everywhere; disable older protocols.
- Use **AWS Certificate Manager (ACM)** to provision and auto-renew certificates for load balancers, CloudFront, and API Gateway.
- Enforce HTTPS with policies: S3 bucket policies requiring `aws:SecureTransport`, and redirect HTTP→HTTPS at load balancers/CDN.
- Use TLS for internal service-to-service traffic, not just external.

**Example: S3 bucket policy denying non-TLS access**
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Sid": "DenyInsecureTransport",
    "Effect": "Deny",
    "Principal": "*",
    "Action": "s3:*",
    "Resource": [
      "arn:aws:s3:::my-secure-bucket",
      "arn:aws:s3:::my-secure-bucket/*"
    ],
    "Condition": { "Bool": { "aws:SecureTransport": "false" } }
  }]
}
```

## Secrets Management

Secrets (DB passwords, API keys, tokens) must never live in code, container images, or plaintext environment files.

- Store secrets in **AWS Secrets Manager** (supports automatic rotation) or **SSM Parameter Store** (SecureString) for simpler cases.
- Grant applications access via IAM roles; fetch secrets at runtime.
- Enable **automatic rotation** for database and service credentials.
- Scan repositories and images for leaked secrets (git-secrets, CI secret scanning) and rotate anything exposed.

## Keeping People Away from Data

Reduce direct human access to reduce risk of error and misuse.

- Automate data handling; avoid manual queries against production data.
- Use tooling that operates on data on the user's behalf (e.g., run reports through a service, not by granting DB console access).
- Where access is unavoidable, make it just-in-time, time-boxed, MFA-gated, and fully logged.
- Use S3 Access Points, Lake Formation, or query services with fine-grained authorization instead of broad bucket/table access.

## Protecting Data Integrity and Availability

- Enable **versioning** and **object lock** on critical S3 data to protect against overwrite/delete.
- Use **AWS Backup** for centralized, policy-driven, encrypted backups with a defined retention and recovery plan.
- Consider immutable/WORM storage for audit logs and compliance data.

## Data Protection Checklist

- [ ] Data classification scheme defined and applied via tags.
- [ ] Macie discovering sensitive data in S3.
- [ ] Encryption at rest enabled by default (S3, EBS, RDS, DynamoDB, backups).
- [ ] KMS customer managed keys for sensitive data with rotation and scoped key policies.
- [ ] TLS 1.2+ enforced in transit; ACM-managed certs; non-TLS access denied.
- [ ] Secrets in Secrets Manager / SSM SecureString with rotation; none in code.
- [ ] Direct human access to production data minimized and, where needed, just-in-time and logged.
- [ ] Critical data protected with versioning, object lock, and centralized backups.

## References
- Security Pillar — Data Protection: https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/data-protection.html
- AWS KMS best practices: https://docs.aws.amazon.com/kms/latest/developerguide/best-practices.html
- AWS Secrets Manager: https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html
