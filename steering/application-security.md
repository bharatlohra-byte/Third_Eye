# Application Security

Build security into how software is written, built, and deployed so vulnerabilities are caught early and the pipeline itself is trustworthy. This reflects the newest focus area of the Security Pillar (AppSec).

## Shift Security Left

Find and fix issues as early as possible — a flaw caught in code review is far cheaper than one found in production.

- Provide developers with security training, secure defaults, and reusable hardened components.
- Make the secure path the easy path: vetted libraries, templates, and paved-road pipelines.
- Give teams fast feedback in their IDE and pull requests rather than gating only at the end.

## Secure the Software Development Lifecycle

**Threat modeling:**
- Model threats for new features and significant changes: what are the assets, who are the actors, what can go wrong, and what mitigations apply.
- Prioritize mitigations by risk and revisit as the design evolves.

**Code and dependency scanning:**
- **SAST** (static analysis) on source in CI to catch insecure patterns.
- **SCA** (software composition analysis) to find vulnerable and outdated dependencies; use **Amazon Inspector** for Lambda and container image scanning, plus ecosystem tools (Dependabot, npm/pip audit).
- **Secret scanning** on every commit and image build to catch leaked credentials before they merge.
- **DAST** against running staging environments for runtime issues.

**Validate inputs and handle data safely:**
- Validate and sanitize all untrusted input; use parameterized queries to prevent injection.
- Encode output to prevent XSS.
- Apply least privilege in application IAM roles — the app should only reach the resources it needs.

## Secure the Pipeline and Artifacts

The build/deploy pipeline is a high-value target; a compromised pipeline can ship malicious code everywhere.

- Protect source: enforce branch protection, required reviews, and signed commits.
- Scope CI/CD permissions tightly with dedicated IAM roles; no broad admin in build runners.
- Verify integrity of artifacts and dependencies; pin versions and prefer trusted registries to reduce supply-chain and typosquatting risk.
- Store build secrets in Secrets Manager / Parameter Store, injected at runtime — never in pipeline definitions or logs.
- Scan infrastructure-as-code (CloudFormation/Terraform) for misconfigurations before deploy (e.g., cfn-guard, checkov).
- Promote immutable, scanned artifacts through environments rather than rebuilding per stage.

## Runtime Application Protection

- Front web applications with **AWS WAF** using managed rule groups plus app-specific rules.
- Use API Gateway authorizers, Cognito, or a trusted IdP for authentication and authorization at the edge of the app.
- Log application security events and feed them into your detection pipeline.
- Rate-limit and apply bot control for public APIs.

## Automate and Enforce

- Make security checks part of the pipeline so they run on every change, not on request.
- Fail builds on high-severity findings; track and triage lower-severity ones.
- Use policy-as-code to enforce standards (required scans, no plaintext secrets, encryption on) automatically.

**Example: pipeline security gates (concept)**
```
commit -> secret scan + SAST -> build -> SCA + image scan (Inspector)
       -> IaC scan (checkov/cfn-guard) -> deploy to staging -> DAST
       -> promote same signed artifact to prod
(any high-severity finding fails the stage)
```

## Application Security Checklist

- [ ] Threat modeling performed for new features and major changes.
- [ ] SAST, SCA, secret scanning, and IaC scanning run automatically in CI.
- [ ] Inspector scanning Lambda and container images; vulnerable artifacts blocked.
- [ ] Input validation, parameterized queries, and output encoding applied.
- [ ] Application IAM roles follow least privilege.
- [ ] Pipeline permissions scoped; source protected with reviews and branch protection.
- [ ] Dependencies pinned from trusted registries; supply-chain risk considered.
- [ ] Build/runtime secrets in Secrets Manager / Parameter Store, never in code or logs.
- [ ] WAF and proper authN/authZ protect running applications.
- [ ] Security gates fail builds on high-severity findings.

## References
- Security Pillar — Application Security: https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/application-security.html
- Amazon Inspector: https://docs.aws.amazon.com/inspector/latest/user/what-is-inspector.html
