[Home](../../README.md) › [Fullstack Projects](README.md) › **Add a JWT Login and a Protected Route**

# Add a JWT Login and a Protected Route in React

`Fullstack` · `🟡 Mid` · `🔨 Build` · `Ticket FS-207`

**Skills** — JWT · auth · React · Flask · protected routes

> The "profile" view only shows after login - in the UI. But `/api/me` hands the profile to anyone who asks with `curl`. Hiding a view in the frontend isn't security. You're putting the gate where it belongs: the server.

> [!NOTE]
> **The scenario**
> The app has a login form and a profile view that should only appear once signed in. `POST /api/login` returns a JWT, but `GET /api/me` returns the profile to *anyone* - no token check. You fix `/api/me` to read the `Authorization: Bearer` header, verify the JWT (HS256) with the server secret, and return the profile only for a valid token, else `401`.

## 🧩 The code

The unprotected endpoint (`app.py`):

```python
@app.get("/api/me")
def me():
    # BUG: returns the profile to ANYONE - no Authorization header check
    return jsonify(username="alice", role="admin")
```

The symptom:

```text
$ curl localhost:8000/api/me
{"username":"alice","role":"admin"}     # no login required
```

## 🛠️ How you'll approach it

1. **See the real gap.** The frontend hides the view, but the API is wide open - anyone can call `/api/me` directly.
2. **Read the token.** Pull the `Authorization: Bearer <token>` header on the server.
3. **Verify it.** Decode and verify the JWT with the secret (HS256); reject missing/invalid/expired tokens with `401`.
4. **Verify.** `/api/me` without a valid token returns `401`; after login, the token grants access and the protected view renders.

## 🎓 What you'll learn

- The end-to-end auth loop: login → token → verified request
- Why frontend route guards are UX, not security
- Verifying a JWT server-side on every protected request
- Returning `401` correctly for missing/invalid tokens

## 🏆 What it proves

You understand that authentication lives on the server, not in the UI - and you can build a real protected-route flow across React and the API.

> [!TIP]
> **Resume-ready** — *Secured a protected API route with server-side JWT verification, closing a gap where the endpoint returned data to any caller regardless of login.*

## 🗺️ On the roadmap

Part of the [Fullstack Engineer Roadmap](../../roadmaps/fullstack.md) - **Stage 7: Authentication End-to-End** → JWT login & protected routes.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **FS-207** on HeyDevJob - the real open `/api/me` above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup.
> [Add a JWT Login and Protected Route on HeyDevJob →](https://heydevjob.com/fullstack)

**Explore Fullstack** · [📍 Roadmap](../../roadmaps/fullstack.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/fullstack.md) · [✅ Checklist](../../checklists/fullstack.md)

[◀ Prev: Implement Cursor Pagination for a Load More Feed](cursor-pagination-load-more.md) · [Next: Stream the AI Chat Response ▶](stream-the-ai-chat-response.md)
