[Home](../README.md) › **Security Interview**

# Security Engineer Interview Questions

`Interview prep` · `Click a question to reveal the answer`

The questions below are the ones that actually get asked when a team is deciding whether to hire you to find what's broken before an attacker does. They follow the [Security Engineer Roadmap](../roadmaps/security.md) from junior to senior: web fundamentals, injection, access control, auth and crypto, secrets, supply chain, automation, and secure design.

Knowing the answer is table stakes. Being able to find, exploit, patch, or harden the real thing is what gets you hired. Where a question maps to a live ticket on HeyDevJob, you'll see a **Practice it live** link - a real broken system in a cloud workspace you fix from your browser.

## 🌐 Web Security Fundamentals (OWASP Top 10)

<details>
<summary><b>What is the OWASP Top 10, and why do interviewers keep coming back to it?</b></summary>

<br>

It's a periodically updated list of the most critical web application security risks, currently led by Broken Access Control, Cryptographic Failures, and Injection. It matters because it maps almost one-to-one onto the vulnerabilities you'll actually find in production code, so it's a shared vocabulary between you and the team. Interviewers use it to check that you reason in vulnerability classes, not isolated bugs.

</details>

<details>
<summary><b>What causes cross-site scripting (XSS), and how do you stop it?</b></summary>

<br>

XSS happens when untrusted input is reflected into a page without being treated as data, so the browser executes it as markup or script. The fix is context-aware output encoding at render time (HTML, attribute, JS, URL contexts each differ), plus a Content-Security-Policy as defense-in-depth. Stored XSS is the most dangerous variant because the payload persists and fires for every viewer.

🛠️ *Practice it live:* [Neutralize a Stored XSS in Comments](https://heydevjob.com/security)

</details>

<details>
<summary><b>Reflected, stored, and DOM-based XSS - what's the difference?</b></summary>

<br>

Reflected XSS bounces a payload off the server in the immediate response (usually via a crafted link). Stored XSS persists the payload server-side so it executes for anyone who loads the affected record. DOM-based XSS never touches the server-rendered HTML at all - vulnerable client-side JavaScript writes attacker input into the DOM (for example via `innerHTML`), so the fix lives in the front-end code.

</details>

## 💉 Injection

<details>
<summary><b>Why do parameterized queries beat input escaping for SQL injection?</b></summary>

<br>

SQL injection occurs when input is concatenated into a query and changes its meaning. Escaping is a blocklist game you lose, because there's always an encoding or context you missed. Parameterized queries (prepared statements) send the query structure and the values over separate channels, so input is bound as a literal and can never become SQL.

🛠️ *Practice it live:* [Close a SQL Injection in a Search Endpoint](https://heydevjob.com/security)

</details>

<details>
<summary><b>A diagnostics endpoint runs a shell command with user-supplied input. How do you make it safe?</b></summary>

<br>

Stop using a shell. Filtering metacharacters like `;`, `|`, and backticks loses because shell quoting is deep and full of bypasses. Instead, call the binary directly with an argument list (`subprocess.run([...], shell=False)` or `execve`) so the OS never re-parses your arguments as a command line. Validate the input against a strict allowlist on top of that.

🛠️ *Practice it live:* [Close a Command Injection in a Diagnostics API](https://heydevjob.com/security)

</details>

<details>
<summary><b>What is SSRF and why is it so dangerous in cloud environments?</b></summary>

<br>

Server-side request forgery tricks a server into making an HTTP request to a destination the attacker chooses. In the cloud it's severe because the server can reach the instance metadata endpoint (`169.254.169.254`) to steal IAM credentials, plus internal services with no auth. Defend it by validating the resolved IP against an allowlist, blocking link-local and private ranges, disabling redirects, and re-validating after DNS resolution to kill DNS-rebinding.

🛠️ *Practice it live:* [Block an SSRF in a URL Fetcher](https://heydevjob.com/security)

</details>

<details>
<summary><b>How does command injection differ from SQL injection in impact?</b></summary>

<br>

SQL injection compromises the data the database holds - read, modify, or destroy. Command injection compromises the host itself, because input reaches a shell that runs with the application's privileges, which is remote code execution. RCE is the highest-severity class precisely because it gives the attacker the box, not just the data.

</details>

## 🚪 Broken Access Control

<details>
<summary><b>What is an IDOR, and why doesn't authentication fix it?</b></summary>

<br>

An insecure direct object reference is when an endpoint exposes a record by an id the user can change (`/invoices/1042`) without checking that the requester owns it. Authentication proves *who* you are; IDOR is an *authorization* gap, so a logged-in user can simply enumerate ids and read everyone's data. The fix is a server-side ownership check on every object access, ideally scoping the query itself to the current user.

🛠️ *Practice it live:* [Fix an IDOR Exposing Other Users' Invoices](https://heydevjob.com/security)

</details>

<details>
<summary><b>How do you prevent path traversal in a file-serving endpoint?</b></summary>

<br>

Path traversal uses `../` sequences to escape the intended directory and read arbitrary files. Don't blocklist `../` - canonicalize first. Resolve the full real path (`os.path.realpath`) and verify it still starts with the intended base directory; reject if not. Pair that with a strict filename allowlist and a low-privilege process so even a miss has a small blast radius.

🛠️ *Practice it live:* [Block a Path Traversal Vulnerability](https://heydevjob.com/security)

</details>

<details>
<summary><b>Why is Broken Access Control the #1 risk on the current OWASP list?</b></summary>

<br>

Because it can't be caught by a signature scanner the way an injection pattern can - the missing check is invisible in the code that's there. Every endpoint, object, and field needs an authorization decision, and one forgotten check exposes data. It's also trivially exploitable: no special payload, just a different id or a tampered request.

</details>

## 🔑 Authentication & Session Security

<details>
<summary><b>Explain the JWT `alg: none` bypass and how to defend against it.</b></summary>

<br>

A JWT header declares its own signing algorithm, and naive verifiers trust it - including `none`, which means "no signature." An attacker forges an admin token, sets `alg: none`, and it passes without any key. Fix it by pinning verification to an explicit allowlist of expected algorithms (for example `algorithms=["HS256"]`) so unsigned and confused-algorithm tokens are rejected.

🛠️ *Practice it live:* [Patch the JWT None-Algorithm Bypass](https://heydevjob.com/security)

</details>

<details>
<summary><b>What is JWT algorithm confusion (RS256 to HS256)?</b></summary>

<br>

A server that accepts both RS256 and HS256 can be tricked: the attacker takes the public RSA key (which is, by definition, public), and signs a forged token with HS256 using that public key as the HMAC secret. If the verifier picks the algorithm from the token header, it validates the forgery with the same public key. The defense is the same root fix - pin the expected algorithm rather than reading it from attacker-controlled input.

</details>

<details>
<summary><b>How should passwords be stored, and why are fast hashes wrong?</b></summary>

<br>

Use a slow, salted, memory-hard adaptive hash: Argon2id, scrypt, or bcrypt. Fast hashes like MD5 or SHA-256 are wrong because GPUs can compute billions per second, so a leaked database is cracked in hours. The slow, per-password salt and tunable work factor are what make offline cracking economically infeasible, buying time to detect and rotate after a breach.

🛠️ *Practice it live:* [Hash Passwords Instead of Storing Plaintext](https://heydevjob.com/security)

</details>

<details>
<summary><b>Which flags harden a session cookie, and what does each defend?</b></summary>

<br>

`HttpOnly` blocks JavaScript from reading the cookie, defeating XSS-based session theft. `Secure` forces HTTPS so it never crosses the wire in plaintext. `SameSite=Lax` (or `Strict`) stops the browser from attaching it to cross-site requests, which mitigates CSRF. Together with a long random secret and a sensible expiry, they turn a bearer token into something an attacker can't easily steal or replay.

🛠️ *Practice it live:* [Harden Session Cookie Security Flags](https://heydevjob.com/security)

</details>

<details>
<summary><b>Where should a JWT live on the client - localStorage or a cookie?</b></summary>

<br>

Prefer an `HttpOnly`, `Secure`, `SameSite` cookie. A token in `localStorage` is readable by any JavaScript on the page, so a single XSS exfiltrates it. The tradeoff is that cookies are sent automatically, which reintroduces CSRF risk, so you pair the httpOnly cookie with a CSRF token or `SameSite` enforcement. That combination defends two vulnerability classes at once.

🛠️ *Practice it live:* [Move JWT to httpOnly Cookies (XSS + CSRF)](https://heydevjob.com/security)

</details>

## 🔐 Cryptography

<details>
<summary><b>Symmetric vs asymmetric encryption - when do you reach for each?</b></summary>

<br>

Symmetric (AES) uses one shared key, is fast, and fits bulk data encryption at rest or in transit once a session is established. Asymmetric (RSA, ECC) uses a public/private key pair and solves key distribution and signatures, but is slow. Real systems combine them: asymmetric crypto negotiates a symmetric session key (this is what TLS does), then symmetric crypto carries the traffic.

</details>

<details>
<summary><b>Encryption vs hashing vs encoding - candidates confuse these constantly.</b></summary>

<br>

Encoding (Base64) is reversible and provides no security; it's for transport, not secrecy. Hashing is one-way and deterministic, used for integrity and password storage (with a salt). Encryption is reversible *with a key* and provides confidentiality. Calling Base64 "encryption" in an interview is an instant red flag, so be precise.

</details>

<details>
<summary><b>Why must encryption be authenticated, and what does that mean?</b></summary>

<br>

Plain encryption gives confidentiality but not integrity, so an attacker can tamper with ciphertext (bit-flipping, padding-oracle attacks) without detection. Authenticated encryption (AES-GCM, ChaCha20-Poly1305, or encrypt-then-MAC) attaches a tag that fails verification if a single bit changes. Always use an AEAD mode; never hand-roll your own encrypt-then-hash construction.

</details>

## 🗝️ Secrets & Credentials

<details>
<summary><b>A secret was committed to git. Is deleting the line in a new commit enough?</b></summary>

<br>

No. The secret is recoverable in the repository history forever, so the deletion only hides it from the latest checkout. You must rewrite history (`git filter-repo` or BFG) to purge it from every commit, force-push, and have collaborators re-clone. Most importantly, the secret is compromised the moment it was pushed, so you rotate it regardless of cleanup.

🛠️ *Practice it live:* [Scrub a Secret From Git History](https://heydevjob.com/security)

</details>

<details>
<summary><b>Why is a plaintext secret in an environment variable a problem, and what's better?</b></summary>

<br>

Env vars leak: they show up in `docker inspect`, process listings, crash dumps, CI logs, and child processes inherit them. The better pattern is fetching the credential at runtime from a secrets manager (AWS Secrets Manager, Vault) with a short-lived token, so the secret is never baked into an image, manifest, or log. You also get rotation and audit logging for free.

🛠️ *Practice it live:* [Move a Plaintext Secret to AWS Secrets Manager](https://heydevjob.com/security)

</details>

## 📦 Dependency & Supply Chain Security

<details>
<summary><b>How do you handle a critical CVE reported in one of your dependencies?</b></summary>

<br>

First confirm reachability - is the vulnerable function actually called from your code path, or is it dormant? Then patch to the fixed version, or apply a vendor mitigation if no patch exists yet. Generate an SBOM and run software composition analysis so you find every transitive instance, not just the direct dependency. Supply-chain CVEs (Log4Shell-class) are among the most exploited, so triage and patch speed is the signal interviewers want.

🛠️ *Practice it live:* [Patch Vulnerable npm Dependencies](https://heydevjob.com/security)

</details>

<details>
<summary><b>What is an SBOM and why do teams generate one?</b></summary>

<br>

A Software Bill of Materials is a complete inventory of every component and version in your build, including transitive dependencies. It matters because when the next Log4Shell drops, you can answer "are we affected and where" in minutes instead of days. It also underpins SCA tooling and compliance requirements.

</details>

## 🤖 Security Testing & Automation

<details>
<summary><b>SAST vs DAST vs SCA - what does each catch?</b></summary>

<br>

SAST (static analysis) reads source code to find vulnerable patterns like string-built SQL before anything runs. DAST (dynamic analysis) attacks a running application from the outside to find issues that only appear at runtime. SCA (software composition analysis) scans your dependency tree for known CVEs. They're complementary: SAST and SCA gate the pipeline cheaply, DAST validates the deployed surface.

🛠️ *Practice it live:* [Build a Security Scan Pipeline (SAST + SCA)](https://heydevjob.com/security)

</details>

<details>
<summary><b>How do you stop security scanning from becoming noise developers ignore?</b></summary>

<br>

Fail the build only on high-confidence, high-severity findings, and let lower-severity issues report without blocking. Triage and suppress false positives explicitly with a tracked reason so the baseline stays trustworthy. The goal is a gate developers respect because every block is real, which is how security scales across a codebase instead of training people to click past it.

</details>

## 🛡️ Hardening & Secure Configuration

<details>
<summary><b>What defense-in-depth would you add to a public, unauthenticated API endpoint?</b></summary>

<br>

Rate limiting per client to blunt brute-force and credential-stuffing, request-size and payload-depth caps to stop resource-exhaustion bombs, strict input validation, and timeouts. Each layer is cheap and blocks a whole abuse class so no single client can overwhelm or probe the service. "We'll add rate limiting later" is the most common pre-incident sentence.

🛠️ *Practice it live:* [Add Defense-in-Depth to a Public API](https://heydevjob.com/security)

</details>

<details>
<summary><b>An IAM role has `Action: "*"` on `Resource: "*"`. Walk me through fixing it.</b></summary>

<br>

That's the textbook over-permissioned role, and most breaches escalate through exactly this. Scope it to least privilege: enumerate the actions the workload actually performs, restrict `Action` to that list, and pin `Resource` to specific ARNs. Use access analyzer or CloudTrail to discover real usage, then tighten iteratively and add condition keys where they fit. Least privilege is the single highest-leverage hardening control.

🛠️ *Practice it live:* [Tighten Over-Permissive AWS IAM Policies](https://heydevjob.com/security)

</details>

<details>
<summary><b>What is CSRF, and why doesn't HTTPS or a login stop it?</b></summary>

<br>

Cross-site request forgery makes a victim's browser send a state-changing request to a site they're authenticated to, using their cookies automatically. HTTPS and being logged in don't help because the request is legitimately authenticated - the victim just didn't intend it. Defend with a CSRF token the attacker can't read, `SameSite` cookies, and checking the Origin/Referer on state-changing requests.

</details>

## 🎯 Senior: Secure Design & Detection

<details>
<summary><b>Walk me through threat modeling a new feature.</b></summary>

<br>

Decompose the system into components, data flows, and trust boundaries, then enumerate threats systematically (STRIDE - spoofing, tampering, repudiation, information disclosure, denial of service, elevation of privilege). For each, decide mitigate, accept, transfer, or eliminate, and prioritize by likelihood and impact. The value is catching whole vulnerability classes at design time, before a line of code exists - which is far cheaper than patching post-ship.

</details>

<details>
<summary><b>Prevention failed and you suspect a breach. What are your first moves?</b></summary>

<br>

Contain before you investigate: isolate affected systems, rotate credentials and tokens that may be exposed, and preserve evidence (logs, memory, disk) before anything is wiped. Then reconstruct the timeline from logs to establish initial access, lateral movement, and impact, and scope what data was touched. Eradicate, recover, and run a blameless post-incident review so the same class can't recur. Owning detection-and-response is core senior security work.

</details>

<details>
<summary><b>How would you defend against brute-force credential attacks at scale?</b></summary>

<br>

Layer it: slow adaptive password hashing so a leaked database resists cracking, rate limiting and exponential backoff per account and per IP, account lockout or CAPTCHA after repeated failures, and breached-password checks at signup. Add MFA so a correct password alone isn't enough, and monitor for credential-stuffing patterns (many accounts, one source; one account, many sources). No single control is sufficient, which is the senior insight.

</details>

> [!IMPORTANT]
> **Build it for real**
> Reciting answers gets you to the phone screen. Shipping fixes to real broken systems gets you the offer. Every question above with a **Practice it live** link is a real vulnerable system waiting in a cloud workspace - you exploit it, patch it, or harden it from your browser, and each fix lands on a portfolio hiring managers can open and click. The junior tier is free. No card, no setup.
>
> **Start your portfolio →** [heydevjob.com/security](https://heydevjob.com/security)

---

**Explore Security** · [📍 Roadmap](../roadmaps/security.md) · [🛠️ Projects](../projects/security/README.md) · [💬 Interview](security.md) · [✅ Checklist](../checklists/security.md)
