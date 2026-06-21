[Home](../../README.md) › [DevOps Projects](README.md) › **Automate a Node.js Deploy Pipeline With Rollback**

# Automate a Node.js Deploy Pipeline With Rollback

`DevOps` · `🔴 Senior` · `🔨 Build` · `Ticket DO-307`

**Skills** — deployment automation · health checks · rollback · Bash

> Right now people deploy by SSHing in and running commands from memory. Steps get skipped, health checks get forgotten, and the result is outages. You're replacing the ritual with a script that can't forget - and that rolls itself back when the new version is sick.

> [!NOTE]
> **The scenario**
> The Node.js API has no deployment automation. Engineers deploy by hand, which has caused repeated outages when a step is skipped or the health check is forgotten. You build a pipeline script that runs the whole lifecycle - test, back up the current version, stop it, install the new one, start it, health-check it, and **automatically roll back to the previous version if the health check fails**.

## 🧩 The code

You start from a requirements stub (`pipeline.sh`):

```bash
#!/bin/bash
# Deploy Pipeline Script
# 1. Runs tests (npm test) in the new version directory
# 2. If tests pass, backs up the current version
# 3. Stops the currently running application
# 4. Copies new version files into place
# 5. Installs dependencies (npm install)
# 6. Starts the new application
# 7. Runs a health check (GET /health returns 200 with status "healthy")
# 8. If health check fails, rolls back to the previous version
APP_DIR="/workspace/app"
RELEASES_DIR="/workspace/releases"
BACKUP_DIR="/workspace/backup"
HEALTH_URL="http://localhost:3000/health"
NEW_VERSION="v2"

# Your implementation goes here...
echo "TODO: Implement deploy pipeline"
```

This is a build-from-scratch ticket: the requirements and variables are given, the logic is yours.

## 🛠️ How you'll approach it

1. **Gate on tests.** Run `npm test` first - if it fails, never touch production.
2. **Back up before you break.** Snapshot the current version so "the last good one" actually exists to return to.
3. **Deploy and probe.** Stop old, install and start new, then poll `GET /health` until it's `healthy` or you hit a timeout.
4. **Roll back on failure.** If health never goes green, restore the backup and bring the old version back up - automatically, no human in the loop.
5. **Test both paths.** Prove it deploys a healthy v2 *and* that a deliberately-broken v2 triggers a clean rollback.

## 🎓 What you'll learn

- Turning a fragile manual deploy into a repeatable, fail-safe script
- Health checks as the gate between "started" and "actually serving"
- Implementing automatic rollback so a bad release is a non-event
- Why the same test → deploy → health-check → rollback logic is exactly what CI tools run for you

## 🏆 What it proves

You can build deployment automation that fails safe - the difference between a bad release being a blip and being an outage - and you handle both the happy path and the rollback.

> [!TIP]
> **Resume-ready** — *Built an automated Node.js deploy pipeline with test gating, health checks, and automatic rollback, eliminating manual-deploy outages.*

## 🗺️ On the roadmap

Part of the [DevOps Engineer Roadmap](../../roadmaps/devops.md) - **Stage 8: CI/CD** → Pipelines & deployment strategies.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **DO-307** on HeyDevJob - the real deploy script above, waiting in a cloud workspace you build from your browser. Each fix lands on a portfolio you can show.
> [Automate a Node.js Deploy Pipeline on HeyDevJob →](https://heydevjob.com/devops)

**Explore DevOps** · [📍 Roadmap](../../roadmaps/devops.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/devops.md) · [✅ Checklist](../../checklists/devops.md)

[◀ Prev: Reconcile Terraform State Drift](reconcile-terraform-state-drift.md) · [Next: Triage a SEV1 Production Incident ▶](triage-a-sev1-production-incident.md)
