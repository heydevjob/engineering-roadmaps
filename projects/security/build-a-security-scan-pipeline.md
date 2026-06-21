# Build a Security Scan Pipeline (SAST + SCA)

**Role:** Security Engineer · **Level:** Senior · **Type:** Audit · **Skills:** SAST, SCA, SBOM, semgrep, trivy

> Finding vulnerabilities one at a time doesn't scale. You're building the gate that scans every merge - static analysis for the bugs developers write, dependency scanning for the CVEs they import, and an SBOM for the day an incident asks "what was in this build?"

## The scenario

You write `security-check.sh` that runs three scans and blocks merges on serious issues: **semgrep** (SAST - finds code bugs like SQL injection, hardcoded secrets, dangerous `eval`), **trivy fs** (SCA - finds CVEs in dependencies, HIGH/CRITICAL only), and **trivy sbom** (generates a software bill of materials). It exits non-zero if semgrep or trivy find issues, but always writes `sbom.json`.

## The code

You build the scan script; the repo has planted issues for it to catch:

```python
# Planted in app/api.py for semgrep to find:
def get_user(username):
    query = "SELECT * FROM users WHERE name = '" + username + "'"
    return conn.execute(query).fetchall()
```

```text
# Dependencies with known CVEs for trivy to flag:
flask==2.0.0
requests==2.20.0
```

(Build-from-scratch task: you author `security-check.sh` to orchestrate the three scans.)

## How you'll approach it

1. **SAST with semgrep.** Run it at ERROR severity over the code; it should flag the string-concatenated SQL and any hardcoded secrets.
2. **SCA with trivy fs.** Scan the dependencies for HIGH/CRITICAL CVEs (the pinned flask/requests versions).
3. **SBOM with trivy.** Generate `sbom.json` - the machine-readable record of what's in the build - and write it *regardless* of pass/fail.
4. **Gate the exit code.** Exit non-zero if semgrep or trivy found issues so the pipeline blocks the merge; still emit the SBOM either way.

## What you'll learn

- The three layers: SAST (your bugs), SCA (dependency CVEs), SBOM (the paper trail)
- Wiring scanners into a script that gates a merge on severity
- Why "always write the SBOM" matters for later incident response
- Catching issues pre-merge, which is ~100x cheaper than post-ship

## What it proves

You can build the automation that scales security across a codebase and a team - not finding one bug, but ensuring whole classes get caught before they ship.

> Resume-ready: *Built a CI security gate combining semgrep SAST, trivy SCA, and SBOM generation, blocking merges on HIGH/CRITICAL findings while preserving a build manifest.*

## On the roadmap

Part of the [Security Engineer Roadmap](../../roadmaps/security.md) - **Stage 7: Security Testing & Automation** → SAST + SCA pipelines.

---

**Build it for real.** This is ticket **SE-301** on HeyDevJob - the real codebase with planted issues above, waiting in a cloud workspace you build the gate for from your browser. Each fix lands on a portfolio you can show. [Build a Security Scan Pipeline on HeyDevJob →](https://heydevjob.com/security)
