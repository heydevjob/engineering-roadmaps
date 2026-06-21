# Build an Order Saga With Compensating Actions

**Role:** Backend Engineer · **Level:** Senior · **Type:** Design · **Skills:** distributed systems, sagas, compensation

> An order reserves inventory, charges payment, then schedules shipping - across three services with three databases. Payment succeeds, shipping fails. There's no transaction spanning all three, so you have to unwind the work that already happened, in reverse.

## The scenario

You build a saga orchestrator for an order that spans three services: reserve inventory → charge payment → schedule shipping. A single ACID transaction can't cover them, so on any step's failure you must run compensating actions for the prior successful steps in **reverse order**, then fail with the right status. Success returns a `COMPLETED` result.

## The code

You implement the orchestrator (`saga.py`):

```python
def place_order(order: OrderRequest) -> OrderResult:
    """TODO:
    1. reserve inventory → charge payment → schedule shipping
    2. On any failure, run compensations for prior steps in REVERSE order
    3. Raise SagaError(status=OrderStatus.FAILED_<step>, cause=<exc>)
    4. On success, return OrderResult with status=COMPLETED
    """
    raise NotImplementedError
```

This is a build-from-scratch ticket: the interface and `OrderStatus` are given, the orchestration is yours.

## How you'll approach it

1. **Run steps forward, tracking progress.** Reserve inventory, charge payment, schedule shipping - remembering which steps have succeeded.
2. **Compensate in reverse (LIFO).** If shipping fails after payment succeeded, refund the payment, then release the inventory - undo in the opposite order you did them.
3. **Fail with the right status.** Raise `SagaError` tagged with the step that failed.
4. **Succeed cleanly.** If all three pass, return `COMPLETED`. The classic trap: forgetting that compensation order is LIFO.

## What you'll learn

- Why transactions across services can't use a single ACID transaction or 2PC
- The saga pattern: local steps plus compensating actions to undo partial work
- LIFO compensation ordering and why it matters
- Designing compensations and tagging the failure correctly

## What it proves

You can keep data consistent across services without a distributed lock - the central problem of microservice architecture and a senior system-design staple.

> Resume-ready: *Built a saga orchestrator with reverse-order compensating actions, keeping an order workflow consistent across inventory, payment, and shipping under partial failure.*

## On the roadmap

Part of the [Backend Engineer Roadmap](../../roadmaps/backend.md) - **Stage 10: Senior - System Design & Scale** → Distributed transactions.

---

**Build it for real.** This is ticket **BE-309** on HeyDevJob - the real saga stub above, waiting in a cloud workspace you build from your browser. Each fix lands on a portfolio you can show. [Build an Order Saga With Compensating Actions on HeyDevJob →](https://heydevjob.com/backend)
