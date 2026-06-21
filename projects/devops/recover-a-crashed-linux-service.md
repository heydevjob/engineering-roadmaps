[Home](../../README.md) › [DevOps Projects](README.md) › **Recover a Crashed Linux Service**

# Recover a Crashed Linux Service

`DevOps` · `🟢 Junior` · `🔍 Debug` · `Ticket DO-102`

**Skills** — Linux · process management · environment variables · log reading

> "It worked on my machine." The inventory API is down, the code is fine, and the app dies the instant it starts. The reason it crashes is also the reason it's easy to fix - if you read the error.

> [!NOTE]
> **The scenario**
> An inventory API isn't responding. The developer swears the code is fine, and they're right - it ran on their laptop. On the server it crashes immediately on startup because something it needs from the environment was never set. Run it by hand and it tells you exactly what's missing.

## 🧩 The code

The app reads a required value straight from the environment (`app.py`):

```python
DB_NAME = os.environ['INVENTORY_DB']
```

The symptom when you run it:

```text
$ python3 app.py
Traceback (most recent call last):
  ...
KeyError: 'INVENTORY_DB'
```

`os.environ['INVENTORY_DB']` raises `KeyError` because the variable was never exported on this server - it only existed on the developer's machine.

## 🛠️ How you'll approach it

1. **Run it by hand and read the crash.** Don't guess from "it's down" - start the app in the foreground and let it tell you. The `KeyError: 'INVENTORY_DB'` is the whole case.
2. **Confirm the variable is missing.** `printenv INVENTORY_DB` returns nothing - it was never set in this environment.
3. **Set it and restart.** Export the variable (and make it persist so a reboot doesn't undo you), then start the app and confirm it stays up.

A crash-on-startup with a clear error is the *good* case - it fails loudly and immediately instead of breaking silently later.

## 🎓 What you'll learn

- Why "works on my machine" is usually an environment difference, not a code difference
- Reading a startup crash to the exact missing dependency
- How environment variables are set, exported, and made to persist
- Confirming a service is genuinely recovered, not just started once

## 🏆 What it proves

You can recover a crashed service by reading the failure instead of guessing - the most common first task handed to a junior DevOps engineer, and the fastest way to show you debug methodically.

> [!TIP]
> **Resume-ready** — *Recovered a crashed production service by diagnosing a missing required environment variable from the startup error and restoring it durably.*

## 🗺️ On the roadmap

Part of the [DevOps Engineer Roadmap](../../roadmaps/devops.md) - **Stage 1: Linux & the Command Line** → Processes, signals & the boot path.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **DO-102** on HeyDevJob - the real crashing service above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup.
> [Recover a Crashed Linux Service on HeyDevJob →](https://heydevjob.com/devops)

**Explore DevOps** · [📍 Roadmap](../../roadmaps/devops.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/devops.md) · [✅ Checklist](../../checklists/devops.md)

[◀ Prev: Fix the Nginx 502 Bad Gateway](fix-the-nginx-502-bad-gateway.md) · [Next: Bind a Node App to 0.0.0.0 ▶](bind-a-node-app-to-all-interfaces.md)
