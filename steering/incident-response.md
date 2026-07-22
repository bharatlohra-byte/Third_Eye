# Incident Response

Even with strong preventive controls, prepare to detect, investigate, and recover from security events quickly. Covers the "prepare for security events" design principle.

## Prepare Before an Incident

You cannot improvise incident response under pressure. Establish these ahead of time:

**People and process:**
- Define an incident response plan and playbooks for common scenarios (credential compromise, exposed data, malware, DDoS).
- Assign clear roles: incident commander, investigators, communications, and decision-makers.
- Document escalation paths and contacts, including AWS Support and legal/compliance.
- Know your regulatory notification obligations and timelines.

**Access for responders:**
- Pre-provision a dedicated incident response role with the permissions responders need — do not scramble to grant access mid-incident.
- Keep break-glass access secured (MFA, alerting on use) and separate from day-to-day roles.
- Ensure responders can operate in the security tooling account and pivot into affected accounts.

**Prepare your environment:**
- Centralize and protect logs (see the detection steering file) so evidence survives an attacker who gains access.
- Tag resources with owners and classification so responders can quickly assess impact.
- Maintain a clean-room / forensics account for isolating and analyzing compromised resources.

## Detect and Analyze

- Route high-severity findings from GuardDuty and Security Hub to on-call automatically.
- Use **Amazon Detective** to build the timeline: what identity did what, from where, and how far it spread.
- Preserve evidence: snapshot affected EBS volumes, capture memory where possible, and export relevant logs before making changes.
- Determine scope: which credentials, accounts, resources, and data are affected.

## Contain, Eradicate, Recover

- **Contain:** isolate affected resources (restrictive security group / NACL), disable or rotate compromised credentials, revoke sessions, and quarantine instances.
- **Eradicate:** remove attacker persistence (unexpected IAM users/roles/keys, backdoors, unauthorized resources) and patch the exploited weakness.
- **Recover:** restore from known-good backups or images, validate integrity, and return to normal operations with heightened monitoring.

**Automate response where safe:**
- Use **EventBridge** to trigger **Lambda** or **Systems Manager Automation** runbooks for repeatable actions: quarantine an instance, disable an access key, snapshot a volume, or apply an isolation security group.
- Automation reduces mean-time-to-respond and removes manual error under stress.

**Example: automated key disable on GuardDuty finding (concept)**
```
GuardDuty finding (compromised IAM credentials)
-> EventBridge rule
-> Lambda: iam:UpdateAccessKey (Status=Inactive) + attach deny-all policy + snapshot + notify IC
```

## Simulate and Iterate

- Run **game days** and tabletop exercises to rehearse the plan against realistic scenarios.
- Practice both detection and response tooling so responders are fluent when it matters.
- After every incident or exercise, run a blameless post-incident review.
- Feed lessons back into detections, guardrails, runbooks, and preventive controls — each incident should make the next one less likely and easier to handle.

## Incident Response Checklist

- [ ] Documented IR plan with per-scenario playbooks.
- [ ] Defined roles, escalation paths, and notification obligations.
- [ ] Pre-provisioned responder role and secured break-glass access.
- [ ] Centralized, tamper-resistant logs and a forensics/clean-room account.
- [ ] Findings auto-routed to on-call; Detective available for investigation.
- [ ] Evidence-preservation steps defined (snapshots, log export) before remediation.
- [ ] Automated containment runbooks via EventBridge + Lambda/SSM.
- [ ] Regular game days and blameless post-incident reviews feeding improvements.

## References
- Security Pillar — Incident Response: https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/incident-response.html
- AWS Security Incident Response Guide: https://docs.aws.amazon.com/whitepapers/latest/aws-security-incident-response-guide/welcome.html
