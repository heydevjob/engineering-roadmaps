# Reconcile Terraform State Drift

**Role:** DevOps Engineer · **Level:** Mid · **Type:** Debug · **Skills:** Terraform, IaC, state management, incident response

> An on-call engineer hand-edited a file during last night's incident. Now `terraform plan` wants to undo their change. The team decided to keep it - so you have to make the code agree with reality, carefully.

## The scenario

During an incident, an on-call engineer manually edited `/tmp/app.conf` on the box. Now the real file no longer matches what Terraform's state and config expect, so `terraform plan` shows drift and wants to revert it. The team has agreed to *keep* the manual change, which means updating the Terraform code to match what's actually on disk - the opposite of letting `apply` stomp it.

## The code

The resource as declared (`main.tf`):

```terraform
resource "local_file" "app_config" {
  content  = "version=1.0\n"
  filename = "/tmp/app.conf"
}
```

The symptom:

```text
$ terraform plan
  ~ resource "local_file" "app_config" {
      ~ content = "version=1.0\n" -> "<the actual edited content on disk>"
    }
# drift: the file on disk differs from what main.tf declares
```

## How you'll approach it

1. **Read the plan precisely.** The `~ content` line shows exactly how the real file differs from the declared `content`. Understand which direction you want to win.
2. **See the actual content.** Check what's really in `/tmp/app.conf` - that's the value the team wants to keep.
3. **Update the code to match reality.** Set `main.tf`'s `content` to the actual on-disk value so the code reflects the decision.
4. **Confirm a clean plan.** Re-run `terraform plan` until it reports no changes - state, code, and reality now agree.

## What you'll learn

- Reading a `terraform plan` diff and what `~` (in-place change) really means
- Reconciling drift by deciding which side is the source of truth
- Making code match a deliberately-kept manual change without an `apply` that reverts it
- Why "no changes" is the only plan you should trust after reconciling

## What it proves

You can recover Terraform from drift the safe way - aligning code to a deliberate change - which is the judgment skill at the heart of infrastructure-as-code work.

> Resume-ready: *Reconciled Terraform state drift from an out-of-band change by aligning the configuration to the kept value, restoring a clean, no-op plan.*

## On the roadmap

Part of the [DevOps Engineer Roadmap](../../roadmaps/devops.md) - **Stage 7: Infrastructure as Code** → Terraform fundamentals.

---

**Build it for real.** This is ticket **DO-212** on HeyDevJob - the real drifted state above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Reconcile Terraform State Drift on HeyDevJob →](https://heydevjob.com/devops)
