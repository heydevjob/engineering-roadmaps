[Home](../../README.md) › [Security Projects](README.md) › **Patch the JWT None-Algorithm Bypass**

# Patch the JWT None-Algorithm Bypass

`Security` · `🟡 Mid` · `💥 Exploit` · `Ticket SE-201`

**Skills** — JWT · authentication · cryptography

> The token verifier accepts whatever algorithm the token *says* to use - including `"none"`, which means "no signature." So an attacker just writes `{"role": "admin"}`, sets `alg: none`, and walks in without a key. You're taking that option away.

> [!NOTE]
> **The scenario**
> The JWT verification accepts any algorithm named in the token header, including `none` (no signature required). An attacker forges a valid-looking admin token without knowing the secret by setting `alg: none`. You patch `jwt.decode()` to accept only `HS256` via an explicit allowlist, so unsigned and confused-algorithm tokens are rejected.

## 🧩 The code

The verifier trusting `none` (`app.py`):

```python
payload = jwt.decode(
    token,
    JWT_SECRET,
    algorithms=["HS256", "none"]   # VULNERABLE: "none" should not be here
)
```

The exploit:

```text
# attacker forges, without signing:
header  = {"alg": "none", "typ": "JWT"}
payload = {"sub": "admin", "role": "admin"}
# token passes authentication - no secret needed
```

## 🛠️ How you'll approach it

1. **Forge the token.** Build a token with `alg: none` and an admin payload, no signature - and watch it authenticate.
2. **See the footgun.** The decode call lists `"none"` as an accepted algorithm, so the library skips signature verification entirely.
3. **Lock the allowlist.** Restrict `algorithms` to exactly `["HS256"]` so anything unsigned or signed with a different scheme is rejected.
4. **Verify.** The forged `none` token now fails; legitimately-signed tokens still pass.

## 🎓 What you'll learn

- The classic JWT attacks: `alg: none` and algorithm confusion
- Forging a privilege-escalated token when verification is weak
- Correct verification: pin the algorithm, verify the signature, check claims
- Why "it has a JWT" is not the same as "it's authenticated"

## 🏆 What it proves

You can break weak token authentication the way an attacker would, then implement verification that holds - a high-impact, decade-old bug that still ships in production.

> [!TIP]
> **Resume-ready** — *Exploited a JWT alg:none bypass to forge an admin token, then patched verification to an explicit HS256 allowlist, closing an authentication-bypass.*

## 🗺️ On the roadmap

Part of the [Security Engineer Roadmap](../../roadmaps/security.md) - **Stage 4: Authentication & Session Security** → JWT verification flaws.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **SE-201** on HeyDevJob - the real bypassable verifier above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup.
> [Patch the JWT None-Algorithm Bypass on HeyDevJob →](https://heydevjob.com/security)

**Explore Security** · [📍 Roadmap](../../roadmaps/security.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/security.md) · [✅ Checklist](../../checklists/security.md)

[◀ Prev: Block an SSRF in a URL Fetcher](block-an-ssrf.md) · [Next: Patch Vulnerable npm Dependencies ▶](patch-vulnerable-npm-dependencies.md)
