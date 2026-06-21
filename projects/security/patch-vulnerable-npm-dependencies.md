[Home](../../README.md) › [Security Projects](README.md) › **Patch Vulnerable npm Dependencies**

# Patch Vulnerable npm Dependencies

`Security` · `🟡 Mid` · `🩹 Patch` · `Ticket SE-207`

**Skills** — dependency scanning · SCA · CVEs · supply chain

> Two findings landed: your pinned `express` has a published CVE, and a package nobody even imports is dragging in a transitive XSS. Most of your code is someone else's - and right now their bugs are yours.

> [!NOTE]
> **The scenario**
> A scan flags two supply-chain issues: `express 4.17.1` is affected by CVE-2024-29041 (open redirect, fixed in 4.19.2+), and `left-pad@1.3.0` is declared but never imported while its transitive deps pull in a `marked` version with known XSS. You triage both, bump express to a safe version, and remove the unused dependency entirely.

## 🧩 The code

The vulnerable `package.json`:

```json
{
  "dependencies": {
    "express": "4.17.1",
    "pg": "^8.11.0",
    "left-pad": "1.3.0"
  }
}
```

The findings:

```text
express@4.17.1   CVE-2024-29041  (open redirect)        -> fixed in >=4.19.2
left-pad@1.3.0   unused; pulls transitive marked XSS    -> remove entirely
```

## 🛠️ How you'll approach it

1. **Read the advisories.** For each finding: what's the severity, which versions are affected, is it even reachable in your app.
2. **Patch the real one.** Bump `express` to `^4.19.2` - the minimum version that fixes the CVE without an unnecessary major jump.
3. **Remove the dead weight.** `left-pad` isn't imported anywhere, so deleting it removes its vulnerable transitive `marked` for free - the cheapest fix is deleting code you don't use.
4. **Re-scan.** Confirm the tree comes back clean and the app still runs.

## 🎓 What you'll learn

- Running dependency/SCA scanning and reading a CVE advisory
- Triaging by severity and reachability instead of blindly upgrading everything
- Patching transitive (indirect) dependencies you don't directly control
- That removing an unused dependency is often the best fix

## 🏆 What it proves

You can manage supply-chain risk - find vulnerable dependencies, judge whether they matter, and patch them without breaking the build - which is a huge share of real-world AppSec.

> [!TIP]
> **Resume-ready** — *Triaged and remediated supply-chain CVEs - patched a vulnerable express version and removed an unused dependency dragging in a transitive XSS - returning a clean dependency scan.*

## 🗺️ On the roadmap

Part of the [Security Engineer Roadmap](../../roadmaps/security.md) - **Stage 6: Dependency & Supply Chain Security** → Vulnerable dependencies.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **SE-207** on HeyDevJob - the real flagged dependencies above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup.
> [Patch Vulnerable npm Dependencies on HeyDevJob →](https://heydevjob.com/security)

**Explore Security** · [📍 Roadmap](../../roadmaps/security.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/security.md) · [✅ Checklist](../../checklists/security.md)

[◀ Prev: Patch the JWT None-Algorithm Bypass](patch-the-jwt-none-algorithm-bypass.md) · [Next: Move a Plaintext Secret to AWS Secrets Manager ▶](move-a-secret-to-aws-secrets-manager.md)
