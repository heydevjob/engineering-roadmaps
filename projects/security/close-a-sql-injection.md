# Close a SQL Injection in a Search Endpoint

**Role:** Security Engineer · **Level:** Junior · **Type:** Exploit/Patch · **Skills:** SQL injection, parameterized queries, Flask

> The search box builds its query with an f-string. Type `' OR 1=1--` and the database hands back every user. You're going to prove it, then close it the only way that actually works.

## The scenario

The `/search` endpoint builds a SQL query by interpolating unsanitized user input with a Python f-string, so input can change the query's meaning - SQL injection. You confirm the exploit (an auth-bypass / full-table dump), then fix it with parameterized queries so payloads are treated as literal strings, not SQL.

## The code

The vulnerable query construction (`app.py`):

```python
# VULNERABLE: user input directly interpolated into SQL
query = f"SELECT id, username, email, department FROM users WHERE username LIKE '%{q}%'"
cur.execute(query)
```

The exploit:

```text
$ curl "http://localhost:8000/search?q=' OR 1=1--"
# returns ALL users instead of treating the payload as a literal search term
```

## How you'll approach it

1. **Prove it's injectable.** Send `' OR 1=1--` and watch the whole table come back - the input is being parsed as SQL.
2. **Understand why escaping fails.** Filtering quotes or blocklisting keywords is a losing game; the only real fix is to stop concatenating.
3. **Parameterize.** Use `cur.execute(sql, params)` with `%s` placeholders so the driver sends the value separately from the query - input can never become SQL.
4. **Verify.** Re-run the payload; it now returns nothing (a literal search for that odd string), and normal searches still work.

## What you'll learn

- Spotting an injectable parameter and confirming the exploit safely
- Exploiting SQLi to bypass auth or dump data - thinking like the attacker
- Why input filtering and escaping fail, and why parameterized queries don't
- Verifying the fix holds against the original payloads

## What it proves

You can find, exploit, and remediate SQL injection - the textbook high-severity vulnerability that still appears constantly in real code.

> Resume-ready: *Identified and exploited a SQL injection in a search endpoint, then remediated it with parameterized queries, closing a full-table-disclosure path.*

## On the roadmap

Part of the [Security Engineer Roadmap](../../roadmaps/security.md) - **Stage 2: Injection** → SQL injection.

---

**Build it for real.** This is ticket **SE-101** on HeyDevJob - the real injectable endpoint above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Close a SQL Injection in a Search Endpoint on HeyDevJob →](https://heydevjob.com/projects/sql-injection-fix-portfolio-project)
