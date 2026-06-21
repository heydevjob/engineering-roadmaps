# Honor an Idempotency-Key Header on POST

**Role:** Backend Engineer · **Level:** Junior · **Type:** Harden · **Skills:** idempotency, HTTP, fault tolerance

> A phone on a flaky network retries the order POST. Your API creates a second order. The user paid once and got charged twice. You're implementing the same idempotency-key pattern Stripe uses.

## The scenario

`POST /orders` creates a new order every time it's called, so aggressive client retries over unreliable networks produce duplicate orders. You implement the idempotency-key pattern: cache the first response keyed by the `Idempotency-Key` header and return it verbatim on any retry with the same key, instead of re-executing.

## The code

The endpoint with no idempotency (`app.py`):

```python
# BUG: no idempotency. Add a dict keyed by Idempotency-Key header
# that stores the first response and returns it on subsequent
# POSTs with the same key.

@app.route("/orders", methods=["POST"])
def create_order():
    body = request.get_json(force=True) or {}
    # Always creates a new order - even if the client retries with
    # the same Idempotency-Key, we get a fresh order_id every time.
    order_id = str(uuid.uuid4())
    return jsonify({
        "order_id": order_id,
        "item": body.get("item"),
        "qty": body.get("qty"),
    }), 201
```

The symptom:

```text
$ curl -X POST /orders -H 'Idempotency-Key: abc' -d '{"item":"x","qty":1}'
{"order_id": "11111..."}
$ curl -X POST /orders -H 'Idempotency-Key: abc' -d '{"item":"x","qty":1}'
{"order_id": "22222..."}   # <- different order, duplicate created
```

## How you'll approach it

1. **Key on the header.** Read `Idempotency-Key`; if you've seen it, return the stored response and skip the work.
2. **Store the first result.** On a new key, create the order, then cache the response under that key.
3. **Handle the concurrent retry.** Two identical requests arriving at once must still produce exactly one order.
4. **Verify exactly-once.** Two POSTs with the same key return the *same* `order_id`; different keys create different orders.

## What you'll learn

- Why retries are inevitable and "at-least-once" delivery means you need idempotency
- The idempotency-key pattern: store key + response, short-circuit duplicates
- Handling the race where two identical requests arrive together
- Why every payments API (Stripe, AWS) exposes this as a first-class feature

## What it proves

You can build write operations that survive the real world of retries and duplicate delivery without double-processing - a reliability skill that comes up the moment money or side effects are involved.

> Resume-ready: *Implemented Stripe-style idempotency-key handling on a POST endpoint, eliminating duplicate orders under client retries.*

## On the roadmap

Part of the [Backend Engineer Roadmap](../../roadmaps/backend.md) - **Stage 6: Concurrency & Reliability** → Idempotency.

---

**Build it for real.** This is ticket **BE-123** on HeyDevJob - the real duplicating endpoint above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Honor an Idempotency-Key Header on HeyDevJob →](https://heydevjob.com/backend)
