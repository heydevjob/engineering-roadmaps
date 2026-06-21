[Home](../../README.md) › [Backend Projects](README.md) › **Return the Correct HTTP Status Code**

# Return the Correct HTTP Status Code

`Backend` · `🟢 Junior` · `🔍 Debug` · `Ticket BE-101`

**Skills** — REST · HTTP status codes · API contracts

> The orders API returns `200 OK` for invalid requests - with the error tucked in the body. Every client that trusts the status code thinks the bad request succeeded. The contract is broken.

> [!NOTE]
> **The scenario**
> An orders API validates input but returns `200 OK` even when validation fails, putting the error message in the response body instead. Clients branch on the status code, not by parsing bodies - so a `200` with an error inside silently breaks their error handling and retry logic. You make the status code tell the truth.

## 🧩 The code

The validation returns `200` on failure (`app.py`):

```python
if not data:
    # BUG: returning 200 instead of 400
    return jsonify({"error": "Request body must be JSON"}), 200

errors = []
if not data.get("customer"):
    errors.append("customer is required")
# ... more validation ...

if errors:
    # BUG: returning 200 instead of 400
    return jsonify({"error": "Validation failed", "details": errors}), 200
```

The symptom:

```text
$ curl -i -X POST /orders -d '{}'
HTTP/1.1 200 OK            # <- should be 400 Bad Request
{"error": "Validation failed", "details": ["customer is required"]}
```

## 🛠️ How you'll approach it

1. **See the contract violation.** A client checking `if response.ok` treats this `200` as success and never reads the error - the bug is the mismatch between status and meaning.
2. **Return the right code.** Validation failures are `400 Bad Request` (or `422`). Change the failure paths to return `400` with the error body.
3. **Keep success honest too.** Confirm the happy path still returns the right `2xx`.
4. **Verify both status and body.** A good test asserts the status code, not just the body - that's what catches this class of bug.

## 🎓 What you'll learn

- Why the status code is the contract and the body is the detail
- Choosing the right code: `400` vs `404` vs `422` vs `200`
- How a status/body mismatch breaks client error handling and retries
- Testing the status line, not just the payload

## 🏆 What it proves

You understand the HTTP contract well enough to make an API behave the way every client expects - the most foundational backend correctness skill, and a common real bug.

> [!TIP]
> **Resume-ready** — *Fixed an API returning 200 on validation failure, restoring correct 4xx status semantics so clients can branch on status code reliably.*

## 🗺️ On the roadmap

Part of the [Backend Engineer Roadmap](../../roadmaps/backend.md) - **Stage 1: HTTP & API Fundamentals** → REST, status codes & response shape.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **BE-101** on HeyDevJob - the real status-code bug above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup.
> [Return the Correct HTTP Status Code on HeyDevJob →](https://heydevjob.com/backend)

**Explore Backend** · [📍 Roadmap](../../roadmaps/backend.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/backend.md) · [✅ Checklist](../../checklists/backend.md)

[Next: Add JWT Authentication to an API ▶](add-jwt-authentication.md)
