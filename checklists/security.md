[Home](../README.md) › **Security Checklist**

# Security Engineer Job-Readiness Checklist

`Checklist` · `9 stages` · `Tick what you can prove`

This is the concrete skill list that gets you hired as a security engineer: think like an attacker, defend like an engineer, and prove you can find, exploit, patch, and harden real systems. It tracks the [Security Engineer Roadmap](../roadmaps/security.md) stage by stage, junior to senior.

Tick what you can already do unaided - no tutorial open, no copy-paste. Where you stall, that's your next ticket on [HeyDevJob](https://heydevjob.com/security), where you fix the real broken system instead of reading about it.

## 🌐 Stage 1 - Web Security Fundamentals (OWASP Top 10)

- [ ] Name the current OWASP Top 10 and explain each risk in one sentence
- [ ] Reason about a vulnerability as a *class*, not a one-off bug
- [ ] Identify a reflected, stored, and DOM-based XSS and explain the difference
- [ ] Fix XSS with context-aware output encoding (HTML, attribute, JS, URL contexts)
- [ ] Write a Content-Security-Policy that meaningfully reduces XSS impact
- [ ] Spot unsafe sinks in front-end code (`innerHTML`, `document.write`, `eval`)

## 💉 Stage 2 - Injection

- [ ] Confirm a SQL injection safely with a non-destructive payload
- [ ] Exploit SQLi to bypass auth or dump a table (in an authorized target)
- [ ] Explain why escaping and blocklisting fail where parameterization wins
- [ ] Rewrite a string-built query as a parameterized / prepared statement
- [ ] Identify command injection and fix it by removing the shell (`shell=False`, arg lists)
- [ ] Recognize SSRF and explain the cloud-metadata credential-theft path
- [ ] Block SSRF by validating the resolved IP and blocking link-local/private ranges
- [ ] Account for DNS rebinding when validating outbound request destinations

## 🚪 Stage 3 - Broken Access Control

- [ ] Identify an IDOR and articulate why authentication doesn't fix it
- [ ] Add a server-side ownership check that scopes a query to the current user
- [ ] Exploit path traversal with `../` to read a file outside the intended directory
- [ ] Fix path traversal by canonicalizing and verifying the resolved base path
- [ ] Audit an endpoint set for missing authorization decisions
- [ ] Explain why Broken Access Control evades signature-based scanners

## 🔑 Stage 4 - Authentication & Session Security

- [ ] Forge a token via the JWT `alg: none` bypass and patch it with an algorithm allowlist
- [ ] Explain JWT algorithm confusion (RS256 to HS256) and its fix
- [ ] Verify a JWT correctly: pin the algorithm, verify the signature, validate claims
- [ ] Store passwords with Argon2id / scrypt / bcrypt and explain why fast hashes lose
- [ ] Set `HttpOnly`, `Secure`, and `SameSite` on session cookies and state what each defends
- [ ] Choose between localStorage and httpOnly cookies for a JWT, with tradeoffs
- [ ] Design a login flow resistant to brute-force and credential stuffing

## 🗝️ Stage 5 - Secrets & Credentials

- [ ] Find a committed secret with a scanner (gitleaks / trufflehog)
- [ ] Purge a secret from full git history with `git filter-repo` or BFG
- [ ] Explain why deleting the line in a new commit is not enough, and rotate the secret
- [ ] List the ways a plaintext env-var secret leaks (`docker inspect`, CI logs, child procs)
- [ ] Move a credential to a secrets manager fetched at runtime with a short-lived token
- [ ] Set up automatic rotation and audit logging for a managed secret

## 📦 Stage 6 - Dependency & Supply Chain Security

- [ ] Run an SCA scan and read the CVE output critically
- [ ] Triage a CVE by reachability before patching
- [ ] Patch a vulnerable direct and transitive dependency to a safe version
- [ ] Generate an SBOM and explain its incident-response value
- [ ] Pin and verify dependency integrity (lockfiles, hashes)

## 🤖 Stage 7 - Security Testing & Automation

- [ ] Explain SAST vs DAST vs SCA and what each catches
- [ ] Build a CI gate that runs static analysis + dependency CVE scan + SBOM
- [ ] Fail the build only on high-confidence, high-severity findings
- [ ] Triage and suppress false positives with a tracked, explicit reason
- [ ] Keep the security baseline trustworthy so developers don't learn to ignore it

## 🛡️ Stage 8 - Hardening & Secure Configuration

- [ ] Add rate limiting and request-size caps to a public, unauthenticated API
- [ ] Enumerate the real permissions a workload uses and scope an IAM role to least privilege
- [ ] Pin IAM `Action` and `Resource` and add condition keys where they fit
- [ ] Implement CSRF protection (token + `SameSite` + Origin/Referer check)
- [ ] Combine httpOnly cookies and CSRF defense to close XSS and CSRF together
- [ ] Run the service as a low-privilege user to shrink blast radius

## 🎯 Stage 9 - Senior: Secure Design & Detection

- [ ] Threat-model a new feature with STRIDE and trust boundaries
- [ ] Prioritize threats by likelihood and impact, then mitigate/accept/transfer/eliminate
- [ ] Drive an incident response: contain, rotate, preserve evidence, eradicate, recover
- [ ] Reconstruct an attack timeline from logs (initial access, lateral movement, impact)
- [ ] Run a blameless post-incident review that closes the vulnerability class
- [ ] Design layered defenses where no single control is load-bearing

> [!IMPORTANT]
> **Can't tick it? Prove it.**
> Every unchecked box is a ticket. On HeyDevJob you don't read about a vulnerability - you exploit the real one, patch it, and harden the system, then it lands on a portfolio hiring managers can open and click. The junior tier is free. No card, no setup.
>
> **Start your portfolio →** [heydevjob.com/security](https://heydevjob.com/security)

---

**Explore Security** · [📍 Roadmap](../roadmaps/security.md) · [🛠️ Projects](../projects/security/README.md) · [💬 Interview](../interview/security.md) · [✅ Checklist](security.md)
