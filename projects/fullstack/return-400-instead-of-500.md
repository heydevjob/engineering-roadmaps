# Return 400 on a Missing Field Instead of Crashing With 500

**Role:** Fullstack Engineer · **Level:** Junior · **Type:** Harden · **Skills:** validation, error handling, HTTP status codes, Flask

> Submit the signup form without an email and the server throws a 500. The client made a mistake, but the API reports it as *its own* crash - and pages on-call for nothing. You're turning that into a clean 400.

## The scenario

`POST /api/users` reads `data["email"]` directly, so a missing field raises a `KeyError` that Flask turns into a `500 Internal Server Error`. A client mistake (omitting a field) is being reported as a server failure. You validate the field first and return a `400` with a clear message.

## The code

The unchecked field access (`app.py`):

```python
@app.post("/api/users")
def create_user():
    data = request.get_json(silent=True) or {}
    # BUG: no validation - data["email"] raises KeyError
    email = data["email"]
    user_id = next(_next_id)
    USERS[user_id] = {"id": user_id, "email": email}
    return jsonify(USERS[user_id]), 201
```

The symptom:

```text
$ curl -XPOST localhost:8000/api/users -H 'Content-Type: application/json' -d '{}'
500 Internal Server Error      # should be 400 with a clear message
```

## How you'll approach it

1. **See the misattribution.** `data["email"]` on missing input throws `KeyError` → `500`, which says "the server broke" when really the client sent bad input.
2. **Validate before use.** Check `email` is present (and non-empty) before touching it.
3. **Return the right code.** Missing/invalid input is `400 Bad Request` with a message naming the problem - so the client knows exactly what to fix.
4. **Verify.** An empty body now returns `400`; a valid body still returns `201`.

## What you'll learn

- Why a `500` on bad input hides a client mistake behind a server error
- Validating required fields before using them
- Returning a clear `400` that pinpoints the missing field
- Keeping on-call noise down by reporting client errors as client errors

## What it proves

You return the right status for the right failure - a small correctness habit that keeps clients honest and on-call quiet.

> Resume-ready: *Replaced a 500 crash on missing input with field validation and a clear 400 response, correctly attributing client errors.*

## On the roadmap

Part of the [Fullstack Engineer Roadmap](../../roadmaps/fullstack.md) - **Stage 4: API Correctness & Validation** → Validation & status codes.

---

**Build it for real.** This is ticket **FS-110** on HeyDevJob - the real crashing endpoint above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Return 400 on a Missing Field on HeyDevJob →](https://heydevjob.com/fullstack)
