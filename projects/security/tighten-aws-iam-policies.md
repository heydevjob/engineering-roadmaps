[Home](../../README.md) › [Security Projects](README.md) › **Tighten AWS IAM Policies**

# Tighten Over-Permissive AWS IAM Policies

`Security` · `🔴 Senior` · `🛡️ Harden` · `Ticket SE-305`

**Skills** — AWS IAM · least privilege · Terraform · cloud security

> Three roles each carry `Action: "*"` on `Resource: "*"` - full account access. They work, which is why nobody fixed them. The day one of those credentials leaks, the attacker owns everything. You're scoping each to exactly what it uses.

> [!NOTE]
> **The scenario**
> Three IAM policies (api, worker, cron) grant `*` on `*` - anyone assuming the role has the whole account. You scope each to least privilege: the exact actions and resource ARNs each role actually needs, so a leaked credential's blast radius shrinks from "entire account" to "one operation on one resource."

## 🧩 The code

The wildcard policy (`main.tf`):

```terraform
resource "aws_iam_policy" "api" {
  name   = "api-policy"
  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect   = "Allow"
      Action   = "*"
      Resource = "*"
    }]
  })
}
```

The risk:

```text
# any principal with the api / worker / cron role
# can perform ANY action on ANY resource in the account
```

## 🛠️ How you'll approach it

1. **Find what each role actually does.** api reads from one S3 bucket; worker receives/deletes from one SQS queue; cron invokes one Lambda.
2. **Scope the actions.** Replace `Action: "*"` with the specific calls - e.g. api gets `s3:GetObject`, worker gets `sqs:ReceiveMessage` + `sqs:DeleteMessage`, cron gets `lambda:InvokeFunction`.
3. **Scope the resources.** Replace `Resource: "*"` with the exact ARNs (`arn:aws:s3:::app-uploads/*`, the queue ARN, the function ARN).
4. **Verify it still works.** The role can do its job and nothing more - `terraform plan` shows the tightened policy, the workload is unaffected.

## 🎓 What you'll learn

- Why over-permissioned roles are the highway attackers use to escalate
- Determining the real permissions a workload needs
- Writing least-privilege policies: specific actions, scoped resource ARNs
- Tightening without breaking the workload

## 🏆 What it proves

You can apply least privilege to real cloud infrastructure - the single highest-leverage cloud-hardening control - shrinking the blast radius of a credential compromise without breaking production.

> [!TIP]
> **Resume-ready** — *Refactored three wildcard AWS IAM policies to least privilege - scoped actions and resource ARNs per role - cutting the blast radius of a credential leak from full-account to a single operation.*

## 🗺️ On the roadmap

Part of the [Security Engineer Roadmap](../../roadmaps/security.md) - **Stage 8: Hardening & Secure Configuration** → Least privilege & IAM.

> [!TIP]
> AWS tickets are free - the cloud runs in your own sandbox, no account or card needed.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **SE-305** on HeyDevJob - the real wildcard policies above, in a sandboxed AWS environment you fix from your browser. Each fix lands on a portfolio you can show.
> [Tighten Over-Permissive AWS IAM Policies on HeyDevJob →](https://heydevjob.com/security)

**Explore Security** · [📍 Roadmap](../../roadmaps/security.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/security.md) · [✅ Checklist](../../checklists/security.md)

[◀ Prev: Build a Security Scan Pipeline (SAST + SCA)](build-a-security-scan-pipeline.md) · [Next: Move JWT to httpOnly Cookies (XSS + CSRF) ▶](move-jwt-to-httponly-cookies.md)
