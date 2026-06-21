[Home](../../README.md) › [Backend Projects](README.md) › **Add JWT Authentication to an API**

# Add JWT Authentication to an API

`Backend` · `🟢 Junior` · `🔨 Build` · `Ticket BE-112`

**Skills** — JWT · authentication · FastAPI · security

> The login endpoint is a `TODO` and every `/api/*` route is wide open. You're building the auth layer: sign a token on login, verify it on every protected request, reject everything without one.

> [!NOTE]
> **The scenario**
> The API has a login endpoint that returns a "not implemented" placeholder, and all `/api/*` routes are unprotected. You implement JWT issuance on login (sign HS256, expire in an hour, `401` on bad credentials) and an auth dependency that validates the `Authorization: Bearer` header so protected routes return `401` without a valid token.

## 🧩 The code

The stubbed login and the unprotected route (`main.py`):

```python
@app.post("/auth/login")
def login(credentials: dict):
    """
    TODO: validate `username` + `password` against FIXTURE_USER, then
    return {"token": "<signed jwt>"}. Sign HS256 with JWT_SECRET, expire
    in 1 hour. Return 401 on bad credentials.
    """
    raise HTTPException(status_code=501, detail="JWT login not implemented")

# TODO: write a `require_auth` dependency that validates the
# `Authorization: Bearer <token>` header. Use it on the /api/* routes.

@app.get("/api/users")
def list_users():
    """Returns the users list. Should require a valid JWT."""
    return {"users": USERS, "count": len(USERS)}
```

The symptom:

```text
$ curl /api/users          # 200 OK - no token required (should be 401)
$ curl -X POST /auth/login # 501 Not Implemented
```

## 🛠️ How you'll approach it

1. **Issue the token.** Validate credentials, then sign a JWT (HS256, `JWT_SECRET`, 1-hour expiry) and return it. Bad credentials → `401`.
2. **Verify on every request.** Write the `require_auth` dependency: read the `Bearer` token, verify the signature and expiry, reject anything invalid with `401`.
3. **Protect the routes.** Apply the dependency to the `/api/*` endpoints.
4. **Prove it both ways.** No token → `401`; valid token → `200`; tampered/expired token → `401`.

## 🎓 What you'll learn

- The JWT structure and what the signature does (and doesn't) protect
- Signing on login and the load-bearing step: verifying on every request
- Expiry handling and protecting the signing secret
- The footguns: accepting `alg: none`, skipping expiry, trusting an unverified token

## 🏆 What it proves

You can build authentication that actually authenticates - not a token that looks secure but verifies nothing. This is on every backend interview.

> [!TIP]
> **Resume-ready** — *Built JWT authentication for a FastAPI service - token issuance on login and middleware verification with expiry - protecting previously-open routes.*

## 🗺️ On the roadmap

Part of the [Backend Engineer Roadmap](../../roadmaps/backend.md) - **Stage 3: Authentication & Authorization** → JWT & sessions.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **BE-112** on HeyDevJob - the real open API above, waiting in a cloud workspace you secure from your browser. Free on the junior tier, no card, no setup.
> [Add JWT Authentication to an API on HeyDevJob →](https://heydevjob.com/projects/jwt-authentication-api-portfolio-project)

**Explore Backend** · [📍 Roadmap](../../roadmaps/backend.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/backend.md) · [✅ Checklist](../../checklists/backend.md)

[◀ Prev: Return the Correct HTTP Status Code](return-the-correct-http-status-code.md) · [Next: Add Server-Side Form Validation ▶](add-server-side-validation.md)
