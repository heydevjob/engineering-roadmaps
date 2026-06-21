# Security Engineer Roadmap

The path from "I can spot a SQL injection" to "I can harden a whole system" - the skills, in order, that get you hired as a security engineer and trusted to find what's broken before an attacker does.

Security is a discipline of **finding, proving, and closing** weaknesses. The hireable signal is "I think like an attacker and defend like an engineer." This roadmap is weighted toward real vulnerabilities in real code. Every node tells you **what** to learn, **why it matters**, and **how you'd prove it**, tagged with the kind of security work it is:

- **Audit** - find and assess the weakness
- **Exploit** - prove a vulnerability is real (offensive)
- **Patch** - fix a specific vulnerability
- **Harden** - lock a system down preventatively

Work top to bottom. You learn to break things before you can defend them.

---

## Stage 1 - Web Security Fundamentals (OWASP Top 10)

The vulnerability classes that show up in every assessment and every interview.

### Cross-site scripting (XSS)
- *What:* How untrusted input becomes executable in a victim's browser, and how output encoding stops it.
- *Why it matters:* XSS leads to session theft and account takeover. Hand-rolled HTML string concatenation is where most stored XSS lives.
- *Prove it:* [Neutralize a Stored XSS in Comments](../projects/security/neutralize-a-stored-xss.md) *(Patch)*

## Stage 2 - Injection

Untrusted input reaching an interpreter - the root of the most severe vulnerabilities.

### SQL injection
- *What:* How unparameterized queries let input become SQL, and how to find, exploit, and fix it.
- *Why it matters:* SQLi can dump or destroy an entire database, and it has topped the vulnerability charts for 15+ years.
- *Prove it:* [Close a SQL Injection in a Search Endpoint](../projects/security/close-a-sql-injection.md) *(Exploit/Patch)*

### Command injection
- *What:* Input reaching a shell, and the argument-list fix that removes the shell entirely.
- *Why it matters:* Command injection is remote code execution - the highest-severity class. Filtering metacharacters loses; removing the shell wins.
- *Prove it:* [Close a Command Injection in a Diagnostics API](../projects/security/close-a-command-injection.md) *(Exploit/Patch)*

### Server-side request forgery (SSRF)
- *What:* Tricking a server into making requests it shouldn't - to internal services or cloud metadata.
- *Why it matters:* SSRF is how attackers pivot into internal networks and steal cloud credentials. A top modern vulnerability.
- *Prove it:* [Block an SSRF in a URL Fetcher](../projects/security/block-an-ssrf.md) *(Harden)*

## Stage 3 - Broken Access Control

Reaching data or files you shouldn't - the most common serious web vulnerability today.

### Path traversal
- *What:* Escaping an intended directory with `../` to read arbitrary files, and validating the resolved path.
- *Why it matters:* Path traversal turns "serve a file by name" into "read any file the process can" - source, configs, keys, `/etc/passwd`.
- *Prove it:* [Block a Path Traversal Vulnerability](../projects/security/block-a-path-traversal.md) *(Patch)*

### IDOR (insecure direct object reference)
- *What:* Reading another user's record by changing an id, and the missing server-side ownership check.
- *Why it matters:* An authenticated-but-not-authorized endpoint lets anyone enumerate ids and read everyone's data - trivially exploitable, easily missed.
- *Prove it:* [Fix an IDOR Exposing Other Users' Invoices](../projects/security/fix-an-idor.md) *(Exploit)*

## Stage 4 - Authentication & Session Security

Who someone is, proven correctly. The flaws here are account takeover.

### JWT verification flaws
- *What:* Forging tokens when verification is weak - the `alg: none` bypass and algorithm confusion.
- *Why it matters:* A token you don't verify correctly is a master key. The `none`-algorithm attack is a decade old and still in production.
- *Prove it:* [Patch the JWT None-Algorithm Bypass](../projects/security/patch-the-jwt-none-algorithm-bypass.md) *(Exploit)*

### Password storage
- *What:* Hashing with a slow, salted algorithm - and why plaintext or fast hashes are a breach waiting to happen.
- *Why it matters:* When a database leaks, password storage decides whether accounts fall in seconds or hold for years.
- *Prove it:* [Hash Passwords Instead of Storing Plaintext](../projects/security/hash-passwords-instead-of-plaintext.md) *(Harden)*

### Session cookie security
- *What:* Protecting session cookies with `HttpOnly`, `Secure`, `SameSite`, and a strong secret.
- *Why it matters:* A session cookie is a bearer token - whoever holds it *is* that user. Framework defaults leave them theft-friendly.
- *Prove it:* [Harden Session Cookie Security Flags](../projects/security/harden-session-cookie-flags.md) *(Harden)*

## Stage 5 - Secrets & Credentials

Keys and tokens, and the places they leak.

### Secrets in git history
- *What:* Removing a committed secret from all of history - because deleting the line isn't enough - and rotating it.
- *Why it matters:* A committed secret is compromised the moment it's pushed, and it stays recoverable in history until rewritten.
- *Prove it:* [Scrub a Secret From Git History](../projects/security/scrub-a-secret-from-git-history.md) *(Patch)*

### Secrets management
- *What:* Moving credentials out of env/manifests into a secrets manager fetched at runtime.
- *Why it matters:* Plaintext-in-env is the most common credential-leak vector - visible in `docker inspect`, manifests, and CI logs.
- *Prove it:* [Move a Plaintext Secret to AWS Secrets Manager](../projects/security/move-a-secret-to-aws-secrets-manager.md) *(Harden)*

## Stage 6 - Dependency & Supply Chain Security

Most of your code is someone else's. Their vulnerabilities are yours.

### Vulnerable dependencies
- *What:* Finding known-vulnerable packages, triaging reachability, and patching to a safe version.
- *Why it matters:* Supply-chain CVEs (Log4Shell-class) are some of the most exploited vulnerabilities. Every CVE in your tree is your CVE.
- *Prove it:* [Patch Vulnerable npm Dependencies](../projects/security/patch-vulnerable-npm-dependencies.md) *(Patch)*

## Stage 7 - Security Testing & Automation

Finding vulnerabilities at scale, not one at a time.

### SAST + SCA pipelines
- *What:* Building an automated scan (static analysis + dependency CVE scan + SBOM) that blocks bad merges.
- *Why it matters:* Catching issues before they ship is ~100x cheaper than after. Automated scanning is how security scales across a codebase.
- *Prove it:* [Build a Security Scan Pipeline (SAST + SCA)](../projects/security/build-a-security-scan-pipeline.md) *(Audit)*

## Stage 8 - Hardening & Secure Configuration

Reducing the blast radius before anything goes wrong.

### API defense-in-depth
- *What:* Layering rate limits and request-size caps so a single client can't overwhelm a service.
- *Why it matters:* "We'll add rate limiting later" is the most common pre-incident phrase. These defenses block most brute-force and payload-bomb abuse for free.
- *Prove it:* [Add Defense-in-Depth to a Public API](../projects/security/add-defense-in-depth-to-an-api.md) *(Harden)*

### Least privilege & IAM
- *What:* Scoping permissions to the minimum, especially cloud IAM policies.
- *Why it matters:* Most breaches escalate through over-permissioned roles. Least privilege is the single highest-leverage hardening control.
- *Prove it:* [Tighten Over-Permissive AWS IAM Policies](../projects/security/tighten-aws-iam-policies.md) *(Harden)*

### Secure cookies & CSRF
- *What:* Moving tokens into httpOnly cookies and adding CSRF protection - defending two vulnerability classes at once.
- *Why it matters:* A JWT in `localStorage` is XSS-stealable; a state-changing endpoint with no CSRF check is forgeable from any site.
- *Prove it:* [Move JWT to httpOnly Cookies (XSS + CSRF)](../projects/security/move-jwt-to-httponly-cookies.md) *(Harden)*

## Stage 9 - Senior: Secure Design & Detection

Where security stops being reactive - designing it in, and catching what slips through.

### Threat modeling & incident response
- *What:* Finding what could go wrong in a design before it's built, and reconstructing an attack from evidence when prevention fails.
- *Why it matters:* Senior security work prevents whole classes of vulnerability at design time and owns detection-and-response when they happen.
- *Prove it:* hands-on threat-modeling and incident-response projects are in authoring - for now, the secure-design senior tickets above (scan pipeline, IAM, CSRF) carry this stage.

---

## Where you are on the path

| Stage | You can... | Hiring level |
|-------|-----------|--------------|
| 1-2 | Find and fix the core injection vulnerabilities | Junior |
| 3-5 | Assess access control, auth, and secret handling | Junior → Mid |
| 6-8 | Scale security with tooling and harden systems | Mid → Senior |
| 9 | Design security in and own detection | Senior |

## Build it for real

Every project linked above is a live ticket on [HeyDevJob](https://heydevjob.com/security) - a real vulnerable system in a cloud workspace you attack, patch, or harden from your browser. The junior tier is free, no card, no setup. Each one you complete lands on a portfolio you can show.

**Start your portfolio →** [heydevjob.com/security](https://heydevjob.com/security)
