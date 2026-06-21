# Build a Versioned Secret-Rotation System

**Role:** DevOps Engineer · **Level:** Senior · **Type:** Build/Design · **Skills:** secrets management, rotation, system design

> Real secrets rotate. Rotate one naively - overwrite the single value in one place - and every in-flight request still using the old value breaks. The fix is to keep two versions alive at once and retire the old one only when nothing needs it.

## The scenario

You implement a versioned secret store that rotates safely. Each secret keeps a `current` and a `previous` version. Rotation moves current to previous and writes a new current, so requests still holding the old value keep working during the handover. Once nothing is using the previous value, `expire_old` drops it. Get the two-version overlap right and rotation causes zero failed requests.

## The code

You build out the stubbed interface (`vault.py`):

```python
def init_secret(name: str) -> str:
    raise NotImplementedError

def read(name: str, version: str = "current") -> str:
    raise NotImplementedError

def rotate(name: str) -> str:
    raise NotImplementedError

def expire_old(name: str) -> None:
    raise NotImplementedError
```

This is a build-from-scratch ticket: the signatures define the contract, the implementation and the versioning model are yours.

## How you'll approach it

1. **Model two versions per secret.** Store a `current` and a `previous` slot keyed by name.
2. **`rotate` = roll, don't overwrite.** Move current → previous, generate and store a new current, return it. Nothing using the old value breaks because it still resolves via `previous`.
3. **`read` serves the right version.** Default to `current`; allow `version="previous"` so in-flight clients can still resolve the old secret during the overlap window.
4. **`expire_old` retires safely.** Drop the previous version only once you're confident no client needs it - that's the whole point of the overlap.

## What you'll learn

- Why naive rotation (overwrite-in-place) breaks in-flight requests
- The two-version rolling pattern that makes rotation zero-downtime
- Designing a small, correct interface around a real operational problem
- Where the hard part actually lives: the rotation *driver* deciding when it's safe to expire

## What it proves

You can design and build the mechanism behind safe secret rotation - the same `current`/`previous` model AWS Secrets Manager and Vault use - which is a senior systems-design skill most engineers never get to build.

> Resume-ready: *Built a versioned secret store implementing the current/previous rolling-rotation pattern for zero-downtime secret rotation.*

## On the roadmap

Part of the [DevOps Engineer Roadmap](../../roadmaps/devops.md) - **Stage 10: Senior - Reliability, Scaling & Security** → Secrets, least privilege & supply chain.

---

**Build it for real.** This is ticket **DO-317** on HeyDevJob - the real stubbed vault above, waiting in a cloud workspace you build from your browser. Each fix lands on a portfolio you can show. [Build a Versioned Secret-Rotation System on HeyDevJob →](https://heydevjob.com/devops)
