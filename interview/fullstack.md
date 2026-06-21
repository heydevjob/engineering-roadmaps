# Full Stack Engineer Interview Questions

The fullstack interview is a stack-trace in disguise: the interviewer walks a feature from the browser, through the API, into the database, and back, and at every seam they ask "what breaks here, and how do you fix it." Getting hired means you can answer at every layer without bluffing. The questions below follow the [Fullstack Engineer Roadmap](../roadmaps/fullstack.md) from junior to senior, with a tight, correct answer for each. Where a question maps to a real ticket you can ship on HeyDevJob, there's a "Practice it live" pointer - because the fastest way to answer "have you done this?" is to have actually done it.

## React & component state (junior)

**Why does a `setCount(count + 1)` called three times in one handler only add 1?**
Because `count` is captured once per render, so all three calls read the same stale value and write the same result. State updates batch and are based on the value at render time, not on each other. The fix is the functional updater form - `setCount(c => c + 1)` - which queues each update against the latest pending state, so three calls add 3.

Practice it live: [Fix a React useState Stale-State Bug](https://heydevjob.com/fullstack)

**What is a stale closure and where does it bite in async code?**
A closure captures variables from the render it was created in. When that closure runs later - inside a `setTimeout`, a `fetch` callback, or an event listener you set up once - it still sees the old values. It bites in search boxes, polling loops, and any callback registered in a `useEffect` with a wrong dependency array. The fixes are the functional updater, correct effect dependencies, or a ref for values you need fresh.

**When does React re-render a component, and why does that matter for performance?**
React re-renders when a component's state changes, its props change, or its parent re-renders. It matters because an unnecessary re-render cascades to children, and an expensive child that re-renders on every keystroke causes jank. You control it by keeping state local, memoizing expensive children with `React.memo`, and not recreating object or function props inline when a memoized child depends on referential equality.

**What is the difference between a controlled and an uncontrolled input?**
A controlled input's value is driven by React state - `value={x}` plus an `onChange` that calls `setX`. An uncontrolled input keeps its own value in the DOM and you read it with a ref. Controlled is the default for forms because state is the single source of truth, enables validation as the user types, and lets you reset or pre-fill the field programmatically.

## Forms & user input (junior)

**Why does a form submit reload the whole page and wipe React state?**
Because the browser's native form submit issues a full-page navigation by default. React state lives in memory, so the reload throws it all away. You stop it by calling `e.preventDefault()` in the `onSubmit` handler, then doing the submit yourself with `fetch`. The same default-action gotcha hits anchor clicks and drag-and-drop.

Practice it live: [Stop the Form From Reloading the Page on Submit](https://heydevjob.com/fullstack)

**How do you validate a form on the client without trusting the client?**
Client-side validation is for UX - instant feedback, fewer round-trips - but it is bypassable, so the server must validate again. Treat the client check as a courtesy and the server check as the contract. Never let a request reach your database on the assumption the frontend already screened it.

## Connecting to the backend (junior to mid)

**A React dashboard renders blank with no error. The API returns 200. What's your first move?**
Log the actual response shape. The most common cause is a mismatch between what the API returns (`{ data: [...] }`) and what the component reads (`response` directly), so `.map` runs on `undefined` and renders nothing. Confirm the shape in the network tab, then either unwrap the response correctly or fix the state you set from it. A 200 with a blank screen is almost always a shape bug, not a fetch failure.

Practice it live: [Fix a Blank React Dashboard (Failed Fetch)](https://heydevjob.com/fullstack)

**What is CORS, and why does the fix go on the server?**
CORS is the browser's same-origin policy: a page on one origin cannot read a response from another origin unless that server opts in with an `Access-Control-Allow-Origin` header. The browser enforces it; tools like curl and Postman do not, which is why "works in curl, blocked in the browser" is the tell. The fix is always a response header on the API, never a frontend change.

Practice it live: [Fix the CORS Error](https://heydevjob.com/fullstack)

**What is a CORS preflight, and when does the browser send one?**
For any request that isn't "simple" - a custom header like `Authorization`, a `PUT`/`DELETE`, or a non-form content type - the browser first sends an `OPTIONS` preflight to ask the server what's allowed. The server must answer with `Access-Control-Allow-Methods`, `Access-Control-Allow-Headers`, and the origin. If the preflight fails, the real request never goes out, so a CORS bug can look like the request "silently disappearing."

**How should the frontend handle a non-2xx response from `fetch`?**
`fetch` does not throw on a 4xx or 5xx - it only rejects on a network failure. So you must check `response.ok` (or the status) yourself and branch: show a validation message on 400, redirect to login on 401, show a generic error on 500. Forgetting this is why a "failed" request often silently sets your UI state to an error body parsed as success.

## API correctness & validation (mid)

**A request with a missing field returns a 500. Why is that a bug, and what should it return?**
A 500 means "the server crashed unexpectedly" - it pages on-call and hides a client mistake behind a scary server error. A missing or malformed field is the caller's fault, so the correct response is a `400 Bad Request` with a message naming the problem. Validate input at the edge and fail cleanly with the right `4xx` before any code that assumes the field exists.

Practice it live: [Return 400 on a Missing Field Instead of 500](https://heydevjob.com/fullstack)

**When do you use 401 versus 403?**
`401 Unauthorized` means "I don't know who you are" - no valid credentials, so authenticate. `403 Forbidden` means "I know who you are and you still can't do this" - authenticated but not permitted. Returning 403 when you mean 401 (or vice versa) misleads clients and breaks retry-after-login flows.

**Why must you use parameterized queries instead of building SQL with string interpolation?**
Because interpolating user input into a SQL string is SQL injection - an attacker submits `'; DROP TABLE users; --` and your query executes it. Parameterized queries send the SQL and the values separately, so the database treats input as data, never as code. This is non-negotiable on every query that touches user input, not just login.

## Building full features (mid)

**Walk me through a browser-to-S3 file upload.**
The browser sends the file as `multipart/form-data` to your API. The server reads the stream, validates type and size, then uploads to object storage (S3) - either by streaming the bytes through, or by handing the browser a presigned URL so it uploads directly and the server only records the key. You store the resulting key or URL in the database, never the raw bytes in a row. Validate on the server, because the client's content-type header is a suggestion, not a guarantee.

Practice it live: [Upload Files to AWS S3 From a React Form](https://heydevjob.com/fullstack)

**Build the CRUD loop end to end - what are the layers?**
A table in the database, parameterized SQL for each of create/read/update/delete, an API route per operation returning the right status (201 on create, 200 on read, 204 on delete), and the React UI that calls them and reflects the result. The discipline is keeping the contract consistent across layers: the shape the API returns is the shape the UI expects, and every write is validated server-side.

Practice it live: [Wire React to a Database (CRUD)](https://heydevjob.com/fullstack)

**Where does business logic belong - the React component, the API, or the database?**
In the API layer, as the single source of truth. The component should render and collect input; the database should store and enforce constraints. Putting business rules in the frontend means anyone calling the API directly bypasses them, and putting them only in the database makes them hard to test and reuse. The API is the one gate every client passes through.

## Data & persistence (mid to senior)

**Why is offset pagination (`LIMIT 20 OFFSET 40`) the wrong choice for an infinite feed?**
Because offsets drift when rows are inserted or deleted between page loads - the user sees duplicates or skips items, and large offsets force the database to scan and discard every skipped row. Cursor pagination instead passes the last seen key (a timestamp plus id) and queries `WHERE (created_at, id) < (?, ?) ORDER BY ... LIMIT 20`. It's stable under writes and uses the index, so it stays fast at any depth.

Practice it live: [Implement Cursor Pagination for a Load More Feed](https://heydevjob.com/fullstack)

**What's the N+1 query problem and how does it show up in a fullstack feature?**
You load a list (1 query), then loop and fire one query per row to fetch related data (N queries). It works in dev with 5 rows and falls over in production with 5,000. In a fullstack app it usually hides behind an ORM or a "just fetch the author for each post" loop. You fix it with a join or a single batched `WHERE id IN (...)` query.

**How do you keep a write idempotent so a double-submit doesn't double-charge?**
Have the client send an idempotency key with the request, and store it server-side with the result of the first attempt. On a retry with the same key, return the stored result instead of executing again. Combined with a unique constraint at the database level, this makes "user clicked twice" or "the network retried" safe.

## Authentication end-to-end (mid)

**Why is hiding a route in React not authentication?**
Because a frontend route guard only controls what renders in the browser - the API is still reachable directly with curl. Anyone can skip your UI and call `/api/me` themselves. Real authentication verifies the token on the server, on every protected request. Frontend guards are UX (don't show a view the user can't use); the server check is security.

Practice it live: [Add a JWT Login and a Protected Route](https://heydevjob.com/fullstack)

**Walk through the JWT login loop.**
The user posts credentials to `/login`; the server verifies them, signs a JWT (HS256 with a server secret, or RS256 with a private key) carrying the user id and an expiry, and returns it. The client sends it on every request as `Authorization: Bearer <token>`. The server verifies the signature and expiry on each protected route and rejects missing or invalid tokens with `401`. The token is stateless proof of identity - the server never has to look it up in a session store.

**Where should you store the JWT in the browser, and what's the tradeoff?**
`localStorage` is convenient but readable by any JavaScript on the page, so an XSS bug leaks the token. An `httpOnly` cookie can't be read by JS, which closes the XSS leak, but cookies are sent automatically, so you then need CSRF protection (SameSite plus a token). The senior answer names the tradeoff: httpOnly cookie plus CSRF defense is the more secure default for a browser app.

**Why must a JWT verifier pin the algorithm?**
Because the "none" algorithm bypass: a library that trusts the token's own `alg` header will accept an unsigned token if the attacker sets `alg: none`. Always specify the allowed algorithm(s) explicitly when verifying, and never let the token dictate how it's checked. The same care applies to not confusing HS256 and RS256 verification.

## Real-time & interactivity (senior)

**SSE works against curl but the browser sees nothing until the request ends. What's wrong?**
Something in the chain is buffering. Streaming is end to end or it isn't: the server must flush each chunk (disable response buffering, set `Content-Type: text/event-stream`, send `\n\n`-delimited events), and every proxy in front of it must not buffer the response (`X-Accel-Buffering: no` for nginx). One buffering link collects the whole stream and delivers it at the end, killing the typing-it-out effect.

Practice it live: [Stream the AI Chat Response](https://heydevjob.com/fullstack)

**How do you broadcast a message to every connected SSE client?**
Keep a per-subscriber queue for each open connection. When a message arrives, the server pushes it onto every queue, and each connection's handler drains its own queue to its client. The bug to avoid is "stored but not broadcast" - the message saves to the database but never reaches the live clients - and the cleanup to remember is removing a subscriber's queue when its connection drops, or you leak memory.

Practice it live: [Build Real-Time Chat Broadcast With SSE](https://heydevjob.com/fullstack)

**What is optimistic UI, and what's the one thing people forget?**
Optimistic UI updates the interface immediately - before the server confirms - so the app feels instant (a todo appears the moment you hit enter). The thing people forget is the rollback: if the request fails, you must revert the UI to its previous state and surface the error, or the interface lies about what actually saved. The pattern is: snapshot, apply optimistically, reconcile on success, roll back on failure.

Practice it live: [Add Optimistic UI With Rollback](https://heydevjob.com/fullstack)

**SSE vs WebSockets - when do you reach for each?**
SSE is one-directional (server to client) over plain HTTP, with automatic reconnect built into the browser's `EventSource`. It's the right, simpler choice for streaming tokens, live feeds, and notifications. WebSockets are bidirectional and the right choice when the client also needs to push frequently and with low latency - a collaborative editor, a multiplayer game, a chat where typing indicators flow both ways.

## Senior: scale & system design

**Design a resumable upload for large files. What are the options and how do you choose?**
The candidates are S3 multipart upload (split into parts, upload independently, retry only the failed part, S3 assembles them), client-side chunking with your own resume bookkeeping, or the tus protocol for a standard resumable layer. You choose by constraints: S3 multipart is the least code if you're already on S3 and want presigned per-part URLs; tus buys you a portable, standardized resume story if storage may change. The senior signal is naming the tradeoff (operational simplicity vs portability vs control) and defending the pick, not reciting one answer.

Practice it live: [Design a Resumable Large-File Upload](https://heydevjob.com/fullstack)

**How do you ship a feature across the stack without downtime when the database schema changes?**
Use the expand-then-contract pattern. First expand: add the new column or table and deploy code that writes to both old and new shapes while still reading the old. Backfill the new shape in batches. Then switch reads to the new shape, verify, and only then contract by dropping the old column. The app keeps serving throughout because no single deploy assumes a shape that isn't there yet.

## Build it for real

Reading the answer is not the same as having shipped it. Every question above maps to a real broken system you can fix in a live cloud workspace, from your browser, no setup. Each fix lands on a portfolio a hiring manager can open.

**Start your portfolio →** [heydevjob.com/fullstack](https://heydevjob.com/fullstack)
