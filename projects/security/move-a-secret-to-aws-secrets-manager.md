# Move a Plaintext Secret to AWS Secrets Manager

**Role:** Security Engineer · **Level:** Mid · **Type:** Harden · **Skills:** secrets management, AWS Secrets Manager, boto3

> The database password lives in an environment variable - which means it's also in the deployment manifest, the container's ENV layer, and anything that runs `docker inspect`. You're moving it somewhere it can actually be controlled.

## The scenario

`app.py` reads `DB_PASSWORD` from `os.environ`, but env vars leak through manifests, container image layers, `docker inspect`, and CI logs. You move the password into AWS Secrets Manager (already staged at `app/db/password`) and refactor the app to fetch it at boot, so the secret never lives in the deployment config.

## The code

The env-var read (`app.py`):

```python
def load_password() -> str:
    return os.environ["DB_PASSWORD"]
```

The symptom:

```text
$ docker inspect <container> | grep DB_PASSWORD
"DB_PASSWORD=s3cr3t..."     # visible in manifests, ENV layers, CI logs
```

## How you'll approach it

1. **See the leak surface.** A secret in env is in every place that captures env - manifests, image layers, `docker inspect`, logs.
2. **Fetch from the manager.** Rewrite `load_password()` to pull the secret from Secrets Manager (boto3) at boot using the staged path `app/db/password`.
3. **Cache it.** Fetch once at startup and hold it in memory rather than calling the API on every request.
4. **Verify.** The password is gone from env; the app boots and connects using the value retrieved from Secrets Manager.

## What you'll learn

- Why plaintext-in-env is the most common credential-leak vector
- Fetching a secret at runtime from a secrets manager (or SSM SecureString)
- Caching the fetched value instead of re-fetching per request
- How secrets managers centralize access control (IAM) and enable rotation

## What it proves

You can get credentials out of the deployment surface and into managed, access-controlled storage - a core operational-security refactor every serious team expects.

> Resume-ready: *Migrated a database credential from plaintext environment variables to AWS Secrets Manager with a boot-time fetch, removing the secret from deployment manifests and container layers.*

## On the roadmap

Part of the [Security Engineer Roadmap](../../roadmaps/security.md) - **Stage 5: Secrets & Credentials** → Secrets management.

> AWS tickets are free - the cloud runs in your own sandbox, no account or card needed.

---

**Build it for real.** This is ticket **SE-214** on HeyDevJob - the real plaintext-secret app above, in a sandboxed AWS environment you fix from your browser. Free on the junior tier, no card, no setup. [Move a Plaintext Secret to AWS Secrets Manager on HeyDevJob →](https://heydevjob.com/security)
