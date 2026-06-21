# Fix an IDOR Exposing Other Users' Invoices

**Role:** Security Engineer · **Level:** Mid · **Type:** Exploit · **Skills:** broken access control, IDOR, authorization

> The billing API checks you're logged in, then hands you invoice `/invoices/1002` - which belongs to someone else. Change the number, read the next customer's data. The app authenticated you but never asked whether the invoice was *yours*.

## The scenario

A `/invoices/<id>` endpoint verifies the caller is authenticated but never checks that the invoice belongs to them - an Insecure Direct Object Reference. Any logged-in user can enumerate ids and read every customer's billing data. You prove it by reading another user's invoice, then add the missing server-side ownership check.

## The code

The endpoint that authenticates but never authorizes (`app.py`):

```python
@app.route("/invoices/<int:invoice_id>")
def get_invoice(invoice_id):
    user_id = current_user_id()
    if user_id is None:
        return jsonify({"error": "unauthorized"}), 401
    inv = INVOICES.get(invoice_id)
    if inv is None:
        return jsonify({"error": "not found"}), 404
    # BUG: returns the invoice to ANY authenticated user - no ownership check
    return jsonify(inv)
```

The exploit:

```text
# logged in as user 1, request user 2's invoice:
$ curl -H "X-User-Id: 1" http://localhost:8000/invoices/1002
{"id": 1002, "owner_id": 2, "customer": "Bob", "amount_cents": 9900, ...}
```

## How you'll approach it

1. **Prove the IDOR.** As user 1, request invoice `1002` (owned by user 2) - it returns the data. Changing one number reads someone else's record.
2. **Name the gap.** The endpoint answers "who are you" (authentication) but never "are you allowed this object" (authorization).
3. **Add the ownership check.** Before returning, compare `inv["owner_id"]` to the caller's id and return `403` when they differ.
4. **Verify both ways.** Cross-user access now `403`s; a user can still read their own invoices (no over-blocking).

## What you'll learn

- Finding and exploiting IDOR by manipulating an object identifier
- The crucial distinction between authentication and authorization
- Fixing it with a per-object ownership check enforced server-side
- Why this tops the OWASP list and is so easy to miss in review

## What it proves

You can find and exploit broken access control - the most common serious web vulnerability today - and fix it at the authorization layer rather than by hiding the ids.

> Resume-ready: *Identified and exploited an IDOR exposing other users' invoices, then remediated it with a server-side ownership check returning 403 on cross-user access.*

## On the roadmap

Part of the [Security Engineer Roadmap](../../roadmaps/security.md) - **Stage 3: Broken Access Control** → IDOR.

---

**Build it for real.** This is ticket **SE-216** on HeyDevJob - the real enumerable endpoint above, in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Fix an IDOR on HeyDevJob →](https://heydevjob.com/security)
