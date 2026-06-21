[Home](../../README.md) › [Backend Projects](README.md) › **Add Server-Side Form Validation**

# Add Server-Side Form Validation

`Backend` · `🟢 Junior` · `🛡️ Harden` · `Ticket BE-103`

**Skills** — validation · error handling · security

> The contact endpoint takes whatever it's handed and writes it straight to the database. Empty names, fake emails, one-character messages - all welcome. You're putting a gate at the boundary.

> [!NOTE]
> **The scenario**
> A contact-form API inserts request data into the database with no validation, so spam and garbage flow straight in. You add server-side validation - name (min 2 chars), email (valid format), message (min 10 chars) - that rejects bad input with a structured `400` *before* anything touches the database. The point: server-side validation is the only validation an attacker can't skip.

## 🧩 The code

The endpoint inserts unvalidated input (`app.py`):

```python
data = request.get_json(silent=True)

if not data:
    return jsonify({"error": "Request body must be JSON"}), 400

# NO VALIDATION - accepts anything!
# Add validation here for name, email, and message fields.

name = data.get("name", "")
email = data.get("email", "")
message = data.get("message", "")

conn = get_db()
cur = conn.cursor(cursor_factory=RealDictCursor)
cur.execute(
    "INSERT INTO contacts (name, email, message) VALUES (%s, %s, %s) ...",
    (name, email, message)
)
```

The symptom:

```text
$ curl -X POST /contacts -d '{"name":"","email":"nope","message":"hi"}'
201 Created    # <- garbage accepted and stored
```

## 🛠️ How you'll approach it

1. **Validate at the boundary.** Before the insert, check each field: name length, email format, message length.
2. **Reject clearly.** On any failure return `400` with a structured body naming what was wrong - the client should know exactly which field failed.
3. **Only then write.** The database insert runs only after the data is known-good.
4. **Test the bad paths.** Empty name, malformed email, short message - each should be rejected, not stored.

## 🎓 What you'll learn

- Validating types, formats, required fields, and bounds at the boundary
- Returning a useful `400` that pinpoints the failure
- Why client-side validation is a UX nicety attackers bypass with `curl`
- Keeping bad data out of the database entirely

## 🏆 What it proves

You treat all input as hostile and fail safely - the habit that prevents a whole class of bugs, corrupt data, and injection before it can happen.

> [!TIP]
> **Resume-ready** — *Added server-side validation to an API boundary - field, format, and length checks with structured 400 responses - keeping invalid data out of the database.*

## 🗺️ On the roadmap

Part of the [Backend Engineer Roadmap](../../roadmaps/backend.md) - **Stage 4: Validation & Error Handling** → Input validation.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **BE-103** on HeyDevJob - the real unvalidated endpoint above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup.
> [Add Server-Side Form Validation on HeyDevJob →](https://heydevjob.com/backend)

**Explore Backend** · [📍 Roadmap](../../roadmaps/backend.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/backend.md) · [✅ Checklist](../../checklists/backend.md)

[◀ Prev: Add JWT Authentication to an API](add-jwt-authentication.md) · [Next: Switch to Cursor-Based Pagination ▶](switch-to-cursor-pagination.md)
