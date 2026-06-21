# Fix a Blank React Dashboard (Failed Fetch)

**Role:** Fullstack Engineer · **Level:** Junior · **Type:** Debug · **Skills:** React, fetch, API response shape, debugging

> The dashboard renders blank and the console screams `users.map is not a function`. The API returns `{data: [...]}`, but the code handed the whole response to the component - so `.map` is running on an object, not an array.

## The scenario

A dashboard renders blank with `TypeError: users.map is not a function`. The `/api/users` endpoint returns `{data: [...]}` (a wrapper object), but `loadUsers()` returns the entire response instead of unwrapping `.data`. The component calls `.map` on an object and crashes. You fix the unwrapping so the UI gets the array it expects.

## The code

The function returning the wrong shape (`loadUsers.js`):

```javascript
function loadUsers(response) {
  return response;  // TODO: response.data
}
```

The symptom:

```text
Dashboard renders blank
console: TypeError: users.map is not a function
# the API returns { data: [...] }, but the component got the whole object
```

## How you'll approach it

1. **Read the error literally.** `users.map is not a function` means `users` isn't an array - so what is it?
2. **Check the response shape.** The API wraps the list in `{data: [...]}`; the code returned the wrapper, not the array inside.
3. **Unwrap it.** Return `response.data` so the component receives the array it maps over.
4. **Verify.** The dashboard renders the users; no console error.

## What you'll learn

- Reading a `TypeError` to the actual shape mismatch
- Matching an API's response shape to what the UI state expects
- Why `{data: [...]}` vs `[...]` is the most common "blank screen" bug
- How typed API clients prevent this class of bug at build time

## What it proves

You can debug the most common cause of a blank React screen - a response-shape mismatch - by reading the error instead of guessing.

> Resume-ready: *Fixed a blank React dashboard caused by an API response-shape mismatch, unwrapping the data field so the component received the expected array.*

## On the roadmap

Part of the [Fullstack Engineer Roadmap](../../roadmaps/fullstack.md) - **Stage 3: Connecting to the Backend** → Fetch & response shape.

---

**Build it for real.** This is ticket **FS-103** on HeyDevJob - the real blank dashboard above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Fix a Blank React Dashboard on HeyDevJob →](https://heydevjob.com/projects/react-blank-dashboard-fetch-bug-project)
