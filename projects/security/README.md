# Security Engineer Projects

Real vulnerable systems to attack, fix, and lock down - each one proves a specific security skill and produces something you can put on a portfolio. Security work is finding what's broken, proving it's exploitable, and closing it - so these have you think like an attacker and defend like an engineer. Every project is grounded in a real [HeyDevJob](https://heydevjob.com/security) ticket: the actual vulnerable code, the real exploit, and a live cloud workspace where you fix it.

Each project is tagged with its **type**:

- **Audit** - find and assess the weakness
- **Exploit** - prove a vulnerability is real (offensive)
- **Patch** - fix a specific vulnerability
- **Harden** - lock a system down preventatively

Pairs with the [Security Engineer Roadmap](../../roadmaps/security.md): the roadmap tells you what to learn, these are where you prove it.

## Junior projects

Start here. Find and fix the core vulnerability classes every assessment turns up.

| Project | Ticket | Type | Skills |
|---------|--------|------|--------|
| [Neutralize a Stored XSS in Comments](neutralize-a-stored-xss.md) | SE-102 | Patch | XSS, output encoding |
| [Close a SQL Injection in a Search Endpoint](close-a-sql-injection.md) | SE-101 | Exploit/Patch | SQL injection, parameterized queries |
| [Close a Command Injection in a Diagnostics API](close-a-command-injection.md) | SE-119 | Exploit/Patch | command injection, RCE, subprocess |
| [Block a Path Traversal Vulnerability](block-a-path-traversal.md) | SE-106 | Patch | path traversal, file handling |
| [Hash Passwords Instead of Storing Plaintext](hash-passwords-instead-of-plaintext.md) | SE-104 | Harden | password hashing, bcrypt |
| [Harden Session Cookie Security Flags](harden-session-cookie-flags.md) | SE-110 | Harden | sessions, cookies, web security |
| [Scrub a Secret From Git History](scrub-a-secret-from-git-history.md) | SE-116 | Patch | git, secret management, rotation |

## Mid projects

Multi-step work - exploit auth flaws, patch supply-chain risk, harden real systems.

| Project | Ticket | Type | Skills |
|---------|--------|------|--------|
| [Fix an IDOR Exposing Other Users' Invoices](fix-an-idor.md) | SE-216 | Exploit | broken access control, IDOR, authorization |
| [Block an SSRF in a URL Fetcher](block-an-ssrf.md) | SE-217 | Harden | SSRF, network security, cloud metadata |
| [Patch the JWT None-Algorithm Bypass](patch-the-jwt-none-algorithm-bypass.md) | SE-201 | Exploit | JWT, authentication, cryptography |
| [Patch Vulnerable npm Dependencies](patch-vulnerable-npm-dependencies.md) | SE-207 | Patch | dependency scanning, SCA, CVEs |
| [Move a Plaintext Secret to AWS Secrets Manager](move-a-secret-to-aws-secrets-manager.md) | SE-214 | Harden | secrets management, AWS, boto3 |
| [Add Defense-in-Depth to a Public API](add-defense-in-depth-to-an-api.md) | SE-205 | Harden | rate limiting, API hardening |

## Senior projects

Automation, cloud hardening, and secure design across vulnerability classes.

| Project | Ticket | Type | Skills |
|---------|--------|------|--------|
| [Build a Security Scan Pipeline (SAST + SCA)](build-a-security-scan-pipeline.md) | SE-301 | Audit | SAST, SCA, SBOM, semgrep, trivy |
| [Tighten Over-Permissive AWS IAM Policies](tighten-aws-iam-policies.md) | SE-305 | Harden | AWS IAM, least privilege |
| [Move JWT to httpOnly Cookies (XSS + CSRF)](move-jwt-to-httponly-cookies.md) | SE-304 | Harden | XSS, CSRF, cookies, secure design |

---

## Build it for real

Reading about SQL injection isn't the same as typing a payload into a search box and watching it hand back every user - then closing the door for good. Every project here is a live ticket on [HeyDevJob](https://heydevjob.com/security) - a real vulnerable system in a cloud workspace, no setup. The junior tier is free, no card. Complete it, and it lands on a portfolio hiring managers can open and click.

**Start the Security Engineer projects on HeyDevJob →** [heydevjob.com/security](https://heydevjob.com/security)
