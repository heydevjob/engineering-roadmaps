# Harden Session Cookie Security Flags

**Role:** Security Engineer · **Level:** Junior · **Type:** Harden · **Skills:** sessions, cookies, Flask, web security

> The session secret is the literal string `"secret"`, and the cookies have no protection flags. Anyone can forge a session, steal one with XSS, or read one off plain HTTP. Three small settings, three closed doors.

## The scenario

The app's session handling has three problems: `app.secret_key` is hardcoded to `"secret"` (so sessions can be forged), and the session cookie has neither `HttpOnly` (so XSS can steal it) nor `Secure` (so it leaks over HTTP). You load a strong secret from the environment and enable the protective flags.

## The code

The weak secret and insecure flags (`app.py`):

```python
# BUG: Weak secret key - easily guessable, allows session forgery
app.secret_key = "secret"

# BUG: Insecure session cookie configuration
app.config["SESSION_COOKIE_HTTPONLY"] = False
app.config["SESSION_COOKIE_SECURE"] = False
app.config["SESSION_COOKIE_SAMESITE"] = "Lax"
```

The symptom:

```text
Set-Cookie: session=...   # no HttpOnly, no Secure
# forgeable with the known "secret"; readable by JS; sent over plain HTTP
```

## How you'll approach it

1. **Fix the secret.** A session cookie is signed with the secret - a guessable secret means anyone can mint a valid session. Load a strong random value from an env var.
2. **`HttpOnly`.** Set it so JavaScript (and therefore XSS) can't read the cookie via `document.cookie`.
3. **`Secure`.** Set it so the cookie is only sent over HTTPS, never interceptable on plain HTTP.
4. **Verify.** Inspect `Set-Cookie` - the flags are present, and a session signed with the old `"secret"` no longer validates.

## What you'll learn

- Why a session cookie is a bearer token and the secret protects it
- The protective flags: `HttpOnly`, `Secure`, `SameSite`, and what each defends
- Why framework defaults are not secure-by-default for cookies
- Loading secrets from the environment instead of hardcoding them

## What it proves

You understand session security at the cookie level - forgery, theft, and interception - and you close all three with correct configuration, a quick win that's missed constantly in real apps.

> Resume-ready: *Hardened session cookies with a strong env-loaded secret plus HttpOnly and Secure flags, closing session-forgery, XSS-theft, and HTTP-interception vectors.*

## On the roadmap

Part of the [Security Engineer Roadmap](../../roadmaps/security.md) - **Stage 4: Authentication & Session Security** → Session cookie security.

---

**Build it for real.** This is ticket **SE-110** on HeyDevJob - the real insecure session config above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Harden Session Cookie Security Flags on HeyDevJob →](https://heydevjob.com/security)
