# Infrastructure Protection

Apply defense in depth to networks and compute so that a single failed control does not lead to compromise. Covers the "apply security at all layers" design principle.

## Network Protection

Design networks so that only necessary traffic flows, and only between the endpoints that need it.

**VPC design:**
- Place workloads in **private subnets** by default; only truly public-facing endpoints (load balancers, NAT) sit in public subnets.
- Use **route tables** and **NACLs** as coarse subnet-level controls and **security groups** as fine-grained instance-level controls.
- Prefer **VPC endpoints (PrivateLink)** to reach AWS services without traversing the public internet.
- Segment by trust zone (web tier, app tier, data tier) and restrict traffic between tiers to required ports only.

**Security groups:**
- Default deny. Open the minimum ports to the minimum sources.
- Reference other security groups as sources instead of CIDR ranges where possible (e.g., app tier SG allows the web tier SG on port 8080).
- Avoid `0.0.0.0/0` inbound except on intended public endpoints behind protective controls.

**Edge protection:**
- **AWS WAF** — filter malicious web requests (SQLi, XSS, bad bots) at CloudFront, ALB, or API Gateway. Use managed rule groups plus custom rules.
- **AWS Shield** — DDoS protection; Shield Advanced adds enhanced detection, response support, and cost protection for high-value endpoints.
- **AWS Network Firewall** — stateful, managed firewall for VPC-level filtering, intrusion prevention, and domain allow/deny lists.
- **Route 53 Resolver DNS Firewall** — block DNS queries to known-bad domains.

## Compute Protection

Reduce the attack surface of the systems running your workloads.

**Reduce attack surface:**
- Use hardened, minimal base images. Remove unnecessary packages, ports, and services.
- Prefer managed and serverless compute (Lambda, Fargate) where AWS handles more of the patching burden.
- Disable direct SSH/RDP; use **AWS Systems Manager Session Manager** for shell access (no open ports, full audit logging).

**Patch and configuration management:**
- Use **Systems Manager Patch Manager** to automate OS and application patching on a schedule.
- Use **Systems Manager State Manager** and golden AMIs (via EC2 Image Builder) to keep configuration consistent and drift-free.
- Scan images continuously with **Amazon Inspector** and block vulnerable images from deployment.

**Validate at multiple layers:**
- Host-based protections (endpoint agents, host firewalls) in addition to network controls.
- For containers, scan images in ECR, enforce least-privilege task roles, and run read-only root filesystems where feasible.

## Automate Protection

- Build security controls into infrastructure-as-code templates so every deployment inherits them.
- Use **AWS Config rules** to detect and auto-remediate drift (e.g., a security group opened to the world gets reverted).
- Enforce approved network and compute configurations through pipelines and SCPs rather than manual review.

**Example: deny security group rules open to the world via Config (concept)**
```
Config rule: restricted-common-ports / vpc-sg-open-only-to-authorized-ports
-> flags SGs allowing 0.0.0.0/0 on sensitive ports (22, 3389, 3306...)
-> EventBridge -> SSM Automation revokes the offending rule
```

## Infrastructure Protection Checklist

- [ ] Workloads in private subnets; public exposure limited to intended endpoints.
- [ ] Security groups default-deny, scoped to minimum ports and specific sources.
- [ ] VPC endpoints used for AWS service access where practical.
- [ ] WAF protecting web endpoints; Shield (Advanced for critical) enabled.
- [ ] Network Firewall / DNS Firewall filtering where required.
- [ ] No direct SSH/RDP; access via SSM Session Manager with logging.
- [ ] Automated patching (Patch Manager) and golden images in use.
- [ ] Inspector scanning compute and containers; vulnerable images blocked.
- [ ] Network/compute guardrails enforced via Config rules and IaC.

## References
- Security Pillar — Infrastructure Protection: https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/infrastructure-protection.html
- AWS WAF: https://docs.aws.amazon.com/waf/latest/developerguide/what-is-aws-waf.html
- Systems Manager Session Manager: https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html
