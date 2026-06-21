[Home](../../README.md) › [Security Projects](README.md) › **Hash Passwords Instead of Plaintext**

# Hash Passwords Instead of Storing Plaintext

`Security` · `🟢 Junior` · `🛡️ Harden` · `Ticket SE-104`

**Skills** — password hashing · bcrypt · Flask

> The users table stores passwords as plain text. The day that table leaks - through injection, a backup, or a compromised admin - every account is gone, and so is every account those users reused elsewhere. You're fixing it before that day.

> [!NOTE]
> **The scenario**
> The registration system stores passwords in plaintext in PostgreSQL. Any database access - SQL injection, a leaked backup, a rogue admin - exposes every user's password immediately. You fix both `/register` (hash before storing) and `/login` (verify with a hash comparison) so the database never holds a raw password.

## 🧩 The code

The plaintext insert (`app.py`):

```python
# VULNERABLE: storing password as plain text
cur.execute(
    "INSERT INTO users (username, email, password) VALUES (%s, %s, %s) RETURNING id",
    (username, email, password),
)
```

The symptom:

```text
$ psql -c "SELECT username, password FROM users;"
 alice | hunter2          # <- plaintext passwords sitting in the database
```

## 🛠️ How you'll approach it

1. **Hash on register.** Replace the raw `password` with a salted hash (`generate_password_hash` / bcrypt) before the insert.
2. **Verify on login.** Compare the submitted password against the stored hash with `check_password_hash` - never decrypt, you can't.
3. **Pick a slow algorithm.** bcrypt/argon2 are deliberately slow and salted, which is what defeats offline cracking - fast hashes (MD5/SHA) don't.
4. **Verify.** New registrations store a hash, not the password; correct logins still succeed, wrong ones fail.

## 🎓 What you'll learn

- Why plaintext (and fast hashes) make a database leak catastrophic
- Hashing with a slow, salted algorithm (bcrypt/argon2) and a sane work factor
- Verifying by hash comparison instead of storing recoverable passwords
- The difference between hashing for passwords and hashing for integrity

## 🏆 What it proves

You understand password storage at the level that decides whether a breach is a non-event or a catastrophe - the most fundamental credential-protection skill.

> [!TIP]
> **Resume-ready** — *Replaced plaintext password storage with salted bcrypt hashing and hash-based verification, hardening accounts against offline cracking after a breach.*

## 🗺️ On the roadmap

Part of the [Security Engineer Roadmap](../../roadmaps/security.md) - **Stage 4: Authentication & Session Security** → Password storage.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **SE-104** on HeyDevJob - the real plaintext-password app above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup.
> [Hash Passwords Instead of Storing Plaintext on HeyDevJob →](https://heydevjob.com/projects/password-hashing-bcrypt-portfolio-project)

**Explore Security** · [📍 Roadmap](../../roadmaps/security.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/security.md) · [✅ Checklist](../../checklists/security.md)

[◀ Prev: Block a Path Traversal Vulnerability](block-a-path-traversal.md) · [Next: Harden Session Cookie Security Flags ▶](harden-session-cookie-flags.md)
