[Home](../../README.md) › [Fullstack Projects](README.md) › **Wire React to a Database (CRUD)**

# Wire React to a Database (CRUD)

`Fullstack` · `🟡 Mid` · `🔨 Build` · `Ticket FS-204`

**Skills** — Postgres · CRUD · parameterized queries · Node

> The profiles table exists and the React UI is waiting. Between them are four empty functions: create, read, update, delete. You're building the data layer the whole feature stands on.

> [!NOTE]
> **The scenario**
> You're building a user-profile feature backed by Postgres. The `profiles` table (`id, name, bio`) is seeded, and you implement four CRUD functions in `profile.js` using the `pg` client - always with parameterized queries to prevent SQL injection. This is the fullstack core loop: table → SQL → client → UI.

## 🧩 The code

The empty CRUD stubs (`profile.js`):

```javascript
async function createProfile(id, name, bio) {
  // TODO: INSERT INTO profiles (id, name, bio) VALUES ($1, $2, $3)
}

async function getProfile(id) {
  // TODO: SELECT * FROM profiles WHERE id = $1. Return rows[0] or null.
}

async function updateProfile(id, name, bio) {
  // TODO: UPDATE profiles SET name=$2, bio=$3 WHERE id=$1 RETURNING *.
}

async function deleteProfile(id) {
  // TODO: DELETE FROM profiles WHERE id = $1.
}
```

The symptom:

```text
the functions are empty stubs -> every data operation is a no-op,
the profile feature has nothing behind it
```

## 🛠️ How you'll approach it

1. **Build each operation.** Implement insert / select / update / delete against the `profiles` table.
2. **Always parameterize.** Use `$1, $2, ...` placeholders, never string concatenation - that's both correct and injection-safe.
3. **Return useful shapes.** `getProfile` returns the row or `null`; `update` returns the updated row via `RETURNING *`.
4. **Verify.** Create, read it back, update it, delete it - each reflects in the database.

## 🎓 What you'll learn

- The CRUD loop end to end: table → SQL → client method → UI state
- Writing parameterized queries with the `pg` client
- Returning sensible results (row, null, updated row) per operation
- Why this same shape underlies any data-backed feature (and swaps cleanly to an ORM/Supabase later)

## 🏆 What it proves

You can build the data layer a real feature stands on - the entry ticket to any data-backed fullstack work.

> [!TIP]
> **Resume-ready** — *Built the CRUD data layer for a profile feature with parameterized Postgres queries, covering create, read, update, and delete.*

## 🗺️ On the roadmap

Part of the [Fullstack Engineer Roadmap](../../roadmaps/fullstack.md) - **Stage 5: Building Full Features** → CRUD end to end.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **FS-204** on HeyDevJob - the real CRUD stubs above, waiting in a cloud workspace you build from your browser. Free on the junior tier, no card, no setup.
> [Wire React to a Database (CRUD) on HeyDevJob →](https://heydevjob.com/fullstack)

**Explore Fullstack** · [📍 Roadmap](../../roadmaps/fullstack.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/fullstack.md) · [✅ Checklist](../../checklists/fullstack.md)

[◀ Prev: Upload Files to AWS S3 From a React Form](upload-files-to-s3-from-react.md) · [Next: Implement Cursor Pagination for a Load More Feed ▶](cursor-pagination-load-more.md)
