[Home](../../README.md) › [Security Projects](README.md) › **Move JWT to httpOnly Cookies**

# Move JWT to httpOnly Cookies (XSS + CSRF)

`Security` · `🔴 Senior` · `🛡️ Harden` · `Ticket SE-304`

**Skills** — XSS · CSRF · cookies · JWT · secure design

> The login response hands the JWT back for the client to stash in `localStorage` - where any XSS on the domain can read it. Move it into an httpOnly cookie and you stop the theft, but now the browser auto-sends it, so a malicious site can forge requests. You have to defend both at once.

> [!NOTE]
> **The scenario**
> The app returns the JWT in the `/login` body for storage in `localStorage`, readable by any XSS. You migrate it to an `httpOnly` + `Secure` + `SameSite=Strict` cookie and add CSRF protection: `/login` returns a CSRF token in the body and stores `session_id → csrf_token`; state-changing endpoints like `/transfer` require both the session cookie *and* a matching `X-CSRF-Token` header (the synchronizer-token pattern).

## 🧩 The code

The token handed back for `localStorage` (`app.py`):

```python
@app.route("/login", methods=["POST"])
def login():
    body = request.get_json(force=True) or {}
    user = body.get("username")
    if not user:
        return jsonify({"error": "bad request"}), 400
    session_id = secrets.token_urlsafe(16)
    token = pyjwt.encode({"sub": user, "sid": session_id}, JWT_SECRET, algorithm="HS256")
    return jsonify({"token": token})   # <-- XSS-readable in localStorage
```

The exploit surface:

```text
# XSS on the domain reads the token from localStorage
# AND a malicious site can POST /transfer (no CSRF check) and succeed
```

## 🛠️ How you'll approach it

1. **Move the token into a cookie.** Set the JWT as `httpOnly` (JS can't read it), `Secure` (HTTPS only), `SameSite=Strict` (not sent on cross-site requests) - this closes the XSS-theft and most of CSRF.
2. **Add the synchronizer token.** Return a CSRF token in the login body and store `session_id → csrf_token` server-side.
3. **Require both on writes.** `/transfer` must present the session cookie (auto-sent) *and* an `X-CSRF-Token` header matching the stored token - a forged cross-site POST has the cookie but not the header.
4. **Verify.** XSS can no longer read the token; a cross-site forged `/transfer` fails; the legitimate flow works.

## 🎓 What you'll learn

- Why `localStorage` tokens are XSS-stealable and httpOnly cookies aren't
- The cookie flags - `httpOnly`, `Secure`, `SameSite` - and what each defends
- The CSRF risk that cookie-auth introduces and the synchronizer-token pattern
- Defending two vulnerability classes (XSS + CSRF) together

## 🏆 What it proves

You can do real secure-design work - migrating an auth scheme to defend multiple vulnerability classes at once, the way mature frameworks build in but hand-rolled servers must implement.

> [!TIP]
> **Resume-ready** — *Migrated JWT auth from localStorage to httpOnly+SameSite cookies and added synchronizer-token CSRF protection, closing XSS token-theft and cross-site request forgery together.*

## 🗺️ On the roadmap

Part of the [Security Engineer Roadmap](../../roadmaps/security.md) - **Stage 8: Hardening & Secure Configuration** → Secure cookies & CSRF.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **SE-304** on HeyDevJob - the real localStorage-token app above, waiting in a cloud workspace you harden from your browser. Each fix lands on a portfolio you can show.
> [Move JWT to httpOnly Cookies on HeyDevJob →](https://heydevjob.com/security)

**Explore Security** · [📍 Roadmap](../../roadmaps/security.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/security.md) · [✅ Checklist](../../checklists/security.md)

[◀ Prev: Tighten Over-Permissive AWS IAM Policies](tighten-aws-iam-policies.md)
