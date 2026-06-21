# Close a Command Injection in a Diagnostics API

**Role:** Security Engineer · **Level:** Junior · **Type:** Exploit/Patch · **Skills:** command injection, RCE, subprocess, secure coding

> A diagnostics endpoint runs `du -sh <your path>` by building a shell string. Send `path=/etc;id` and the server runs `id` for you. This is command injection - remote code execution - and the fix is to take the shell away.

## The scenario

A `/diskusage` endpoint reports disk usage for a path by interpolating the user-supplied `path` into a shell command. Because it shells out with a string, an attacker can append their own commands and run them on the host. You confirm the injection, then fix it so the path is a single argument that can never be interpreted as shell syntax.

## The code

The vulnerable shell call (`app.py`):

```python
path = request.args.get("path", "/var/log")

# VULNERABLE: user input interpolated into a shell command string
cmd = f"du -sh {path}"
out = subprocess.check_output(cmd, shell=True, stderr=subprocess.STDOUT,
                              text=True, timeout=5)
```

The exploit:

```text
$ curl "http://localhost:8000/diskusage?path=/etc;id"
{"path": "/etc;id", "usage": "4.2M\t/etc\nuid=0(root) gid=0(root) groups=0(root)"}
#                                          ^ `id` actually ran on the server
```

## How you'll approach it

1. **Prove RCE.** `path=/etc;id` runs `du -sh /etc;id` - the `id` output (`uid=...`) appears in the response. That's arbitrary command execution.
2. **See why filtering fails.** Blocklisting `;` misses `|`, `$()`, backticks, newlines - quoting tricks beat every blocklist.
3. **Remove the shell.** Pass an argument list: `subprocess.check_output(["du", "-sh", path])`. With no shell, `path` is one literal argument and metacharacters lose all meaning.
4. **Verify.** `path=/etc;id` now becomes a literal (invalid) filename - `du` errors, `id` never runs, no `uid=` in the response.

## What you'll learn

- How user input reaching a shell becomes attacker-controlled commands
- Why command injection is remote code execution - the highest-severity class
- The real fix: an argument list (no shell), not metacharacter filtering
- Confirming the injection is dead against the original payloads

## What it proves

You can find and correctly remediate command injection - and you fix it by removing the shell, not by trying to sanitize the input.

> Resume-ready: *Remediated a command-injection (RCE) vulnerability by replacing a shell-string subprocess call with an argument-list call, closing a remote-code-execution path.*

## On the roadmap

Part of the [Security Engineer Roadmap](../../roadmaps/security.md) - **Stage 2: Injection** → Command injection.

---

**Build it for real.** This is ticket **SE-119** on HeyDevJob - the real injectable endpoint above, in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Close a Command Injection on HeyDevJob →](https://heydevjob.com/security)
