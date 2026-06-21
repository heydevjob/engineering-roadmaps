[Home](../../README.md) › [Backend Projects](README.md) › **Stop a Double-Charge Race Condition**

# Stop a Double-Charge Race Condition

`Backend` · `🟡 Mid` · `🔍 Debug` · `Ticket BE-204`

**Skills** — concurrency · transactions · PostgreSQL

> A wallet has $100. Two $80 charges arrive at the same instant. Both read the balance, both see enough, both succeed. The balance is now -$60 and the customer was charged twice. You're closing the gap.

> [!NOTE]
> **The scenario**
> `POST /api/charge` reads a wallet balance, checks it's sufficient, then updates it - in three separate steps. Two concurrent requests both read the same balance, both pass the check, and both write, so a user gets charged twice when only one charge was affordable. You make the check-and-debit atomic.

## 🧩 The code

The read-check-write with the race gap (`main.py`):

```python
# Read
cur.execute("SELECT balance FROM wallets WHERE user_id = %s", (user_id,))
row = cur.fetchone()
balance = float(row[0])
# Check
if balance < amount:
    raise HTTPException(status_code=400, detail="insufficient balance")
# Write (separate query - gap is where the race happens)
cur.execute(
    "UPDATE wallets SET balance = balance - %s WHERE user_id = %s",
    (amount, user_id),
)
```

The symptom:

```text
# two concurrent $80 charges on a $100 wallet
# both return 200; final balance = -$60 (should reject the second)
```

## 🛠️ How you'll approach it

1. **Reproduce under concurrency.** One request is fine; two simultaneous charges expose the gap between the read and the write.
2. **Make it atomic.** Wrap the read-check-write in a transaction and lock the row with `SELECT ... FOR UPDATE` so the second request waits for the first to commit - or push the check into a single conditional `UPDATE ... WHERE balance >= amount`.
3. **Reject correctly.** The second charge should now see the debited balance and fail.
4. **Prove it.** Fire concurrent charges and confirm exactly one succeeds and the balance never goes negative.

## 🎓 What you'll learn

- Recognizing a read-modify-write race and reproducing it deterministically
- The fixes and trade-offs: `SELECT FOR UPDATE`, a conditional atomic `UPDATE`, isolation levels
- Why "it passed testing" hides concurrency bugs - they only appear under simultaneous load
- Proving the fix by hammering it concurrently, not by reading the code

## 🏆 What it proves

You can find and fix a concurrency bug - the category that's invisible in single-threaded testing and expensive in production. Senior interviewers love this one.

> [!TIP]
> **Resume-ready** — *Fixed a read-modify-write race causing double-charges by making the balance check-and-debit atomic with row-level locking, guaranteeing correctness under concurrent load.*

## 🗺️ On the roadmap

Part of the [Backend Engineer Roadmap](../../roadmaps/backend.md) - **Stage 6: Concurrency & Reliability** → Race conditions.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **BE-204** on HeyDevJob - the real double-charge bug above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup.
> [Stop a Double-Charge Race Condition on HeyDevJob →](https://heydevjob.com/backend)

**Explore Backend** · [📍 Roadmap](../../roadmaps/backend.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/backend.md) · [✅ Checklist](../../checklists/backend.md)

[◀ Prev: Fix the N+1 Query](fix-the-n-plus-1-query.md) · [Next: Rename a Postgres Column With Zero Downtime ▶](rename-a-postgres-column-zero-downtime.md)
