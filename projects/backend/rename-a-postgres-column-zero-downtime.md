# Rename a Postgres Column With Zero Downtime

**Role:** Backend Engineer · **Level:** Senior · **Type:** Build/Design · **Skills:** migrations, zero-downtime, expand-contract, SQL

> You need to rename `email_address` to `email` on a live table. A single atomic `ALTER TABLE ... RENAME COLUMN` breaks every running app instance the instant it commits. You can't stop the app - so you do it in three phases instead.

## The scenario

Renaming a column on a live table means coordinating a schema change with running code. A naive rename breaks all app instances still pinned to the old name the moment it commits. You implement the expand-contract pattern in three phases so a running app never sees a missing column.

## The code

You build out the phased migration (`phase1.sql`):

```sql
-- Phase 1: expand. Add new column + trigger that dual-writes both directions.
-- TODO: write the ALTER TABLE + CREATE FUNCTION + CREATE TRIGGER.
```

This is a build-from-scratch ticket: `phase1.sql` and `phase2.sql` are stubs; you write the SQL for each phase.

The constraint:

```text
# atomic rename breaks running instances:
ALTER TABLE users RENAME COLUMN email_address TO email;   # old code -> errors
```

## How you'll approach it

1. **Phase 1 - expand.** Add the new `email` column and a trigger that keeps `email` and `email_address` in sync (dual-write both directions). Now both names work; deploy code that reads/writes the new column.
2. **Phase 2 - backfill.** Copy existing `email_address` values into `email` for rows that predate the trigger.
3. **Phase 3 - contract.** Once all code uses the new column, drop the trigger and the old `email_address` column.
4. **Verify continuity.** At no phase does a running app version see a missing column.

## What you'll learn

- The expand-contract (parallel-change) pattern, phase by phase
- Using a trigger to dual-write during the transition window
- Backfilling existing rows safely
- Coordinating schema and code deploys so no version ever sees a broken schema

## What it proves

You can evolve a production schema under load with no downtime - one of the clearest senior-vs-mid signals in backend work, and a question every senior interview asks.

> Resume-ready: *Executed a zero-downtime column rename using the expand-contract pattern - dual-write trigger, backfill, and contract - with no disruption to running app instances.*

## On the roadmap

Part of the [Backend Engineer Roadmap](../../roadmaps/backend.md) - **Stage 10: Senior - System Design & Scale** → Zero-downtime schema migrations.

---

**Build it for real.** This is ticket **BE-307** on HeyDevJob - the real live-table migration above, waiting in a cloud workspace you build from your browser. Each fix lands on a portfolio you can show. [Rename a Postgres Column With Zero Downtime on HeyDevJob →](https://heydevjob.com/backend)
