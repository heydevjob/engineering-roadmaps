[Home](../../README.md) › [Security Projects](README.md) › **Scrub a Secret From Git History**

# Scrub a Secret From Git History

`Security` · `🟢 Junior` · `🩹 Patch` · `Ticket SE-116`

**Skills** — git · secret management · history rewrite

> A teammate committed `.env` with a live `AWS_SECRET_ACCESS_KEY`. It hasn't been pushed yet - but it's sitting in past commits, one `git log` away. Deleting the file now does nothing; you have to rewrite history before it leaves the machine.

> [!NOTE]
> **The scenario**
> A `.env` file containing a real `AWS_SECRET_ACCESS_KEY` was committed. It isn't pushed yet, but the secret lives in earlier commits, so a normal delete leaves it fully recoverable. You remove it from *all* of history, add `.env` to `.gitignore` so it can't come back, and clean up the refs that still hold the old history - and you treat the key as compromised and rotate it.

## 🧩 The code

The vulnerable state - the secret sitting in history:

```text
$ git log --all -p | grep AWS_SECRET_ACCESS_KEY
+AWS_SECRET_ACCESS_KEY=AKIA................EXAMPLE     # present in past commits
```

(This is a git/incident task - the "vulnerable code" is a committed `.env` recoverable from history.)

## 🛠️ How you'll approach it

1. **Stop the bleeding going forward.** Add `.env` to `.gitignore` so it's never committed again.
2. **Rewrite history.** Use `git filter-branch` (or `git filter-repo`) to drop `.env` from every commit, not just the latest.
3. **Delete the backups.** filter-branch leaves backup refs (`refs/original/...`) that still contain the secret - remove them and expire the reflog so it's truly gone.
4. **Rotate.** A secret that ever existed in a repo must be considered compromised - rotate the AWS key regardless. Verify `git log --all -p | grep` finds nothing.

## 🎓 What you'll learn

- Why a secret in any past commit is exposed until history is rewritten
- Rewriting history with filter-branch/filter-repo and cleaning backup refs
- Why rotation is mandatory, not optional - scrubbing hides it, rotation neutralizes it
- Preventing recurrence with `.gitignore` and secret scanning

## 🏆 What it proves

You understand that a committed secret is compromised the instant it exists in history, and you handle it correctly - purge *and* rotate - rather than just deleting the visible file.

> [!TIP]
> **Resume-ready** — *Removed a committed AWS credential from all git history with filter-branch, cleaned backup refs, and rotated the key, closing a secret-leak vector before push.*

## 🗺️ On the roadmap

Part of the [Security Engineer Roadmap](../../roadmaps/security.md) - **Stage 5: Secrets & Credentials** → Secrets in git history.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **SE-116** on HeyDevJob - the real repo with a leaked secret in history, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup.
> [Scrub a Secret From Git History on HeyDevJob →](https://heydevjob.com/security)

**Explore Security** · [📍 Roadmap](../../roadmaps/security.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/security.md) · [✅ Checklist](../../checklists/security.md)

[◀ Prev: Harden Session Cookie Security Flags](harden-session-cookie-flags.md) · [Next: Fix an IDOR Exposing Other Users' Invoices ▶](fix-an-idor.md)
