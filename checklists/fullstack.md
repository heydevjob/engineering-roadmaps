# Full Stack Engineer Job-Readiness Checklist

Hiring managers don't want "I've watched a React course." They want "I can take a feature from broken or empty to working in production, every layer." This checklist is the concrete skill list that gets you there, grouped by the stages of the [Fullstack Engineer Roadmap](../roadmaps/fullstack.md) and ordered junior to senior. Tick what you can do for real - not recognize, do. Anything you can't tick is your next ticket.

## Stage 1 - React & component state

- [ ] Explain why three `setCount(count + 1)` calls add 1, not 3, and fix it with the functional updater
- [ ] Describe what a stale closure is and where it bites in async callbacks
- [ ] List the three things that trigger a re-render (own state, props, parent render)
- [ ] Use a `useEffect` dependency array correctly and explain what a wrong one breaks
- [ ] Distinguish controlled from uncontrolled inputs and pick the right one
- [ ] Lift state up to share it between sibling components
- [ ] Avoid unnecessary re-renders with `React.memo` and stable prop references

## Stage 2 - Forms & user input

- [ ] Call `e.preventDefault()` to stop a form submit from reloading the page
- [ ] Build a fully controlled form with `value` + `onChange`
- [ ] Validate input on the client for UX feedback
- [ ] Show field-level errors and disable submit while a request is in flight
- [ ] Reset or pre-fill a form programmatically from state

## Stage 3 - Connecting to the backend

- [ ] Call an API with `fetch` and unwrap the response into component state
- [ ] Diagnose a blank screen caused by a response-shape mismatch (`{data:[...]}` vs `[...]`)
- [ ] Check `response.ok` and branch on status, knowing `fetch` doesn't throw on 4xx/5xx
- [ ] Explain CORS as a browser policy and fix it with a response header on the server
- [ ] Identify when the browser sends an `OPTIONS` preflight and what headers satisfy it
- [ ] Scope `Access-Control-Allow-Origin` to a specific origin instead of `*`
- [ ] Handle loading, error, and empty states in a data-fetching component

## Stage 4 - API correctness & validation

- [ ] Return a `400` (not a `500`) on missing or malformed input
- [ ] Validate required fields at the API edge before any code assumes they exist
- [ ] Choose the correct status code per operation (201 create, 200 read, 204 delete)
- [ ] Use 401 vs 403 correctly (unauthenticated vs forbidden)
- [ ] Use parameterized queries on every query that touches user input
- [ ] Return error responses in a consistent JSON shape the frontend can read

## Stage 5 - Building full features

- [ ] Build a `multipart/form-data` upload from a React form to the API
- [ ] Stream or presign an upload to S3 and store the key, not the bytes, in the database
- [ ] Validate file type and size on the server, not just the client
- [ ] Build the full CRUD loop: table → SQL → API route → React UI
- [ ] Keep the API's response shape consistent with what the UI expects across all four operations
- [ ] Put business logic in the API layer, not the component or the database

## Stage 6 - Data & persistence

- [ ] Implement cursor pagination for a "Load More" feed and explain why offset drifts
- [ ] Spot and fix an N+1 query with a join or a batched `WHERE id IN (...)`
- [ ] Read a query plan well enough to know whether an index is being used
- [ ] Make a write idempotent with an idempotency key + a unique constraint
- [ ] Wrap a multi-step write in a transaction so it commits or rolls back as one unit
- [ ] Shape a query to match how the UI reads it, not the other way around

## Stage 7 - Authentication end-to-end

- [ ] Explain why a frontend route guard is UX, not security
- [ ] Build the login loop: credentials → signed JWT → `Authorization: Bearer` on every request
- [ ] Verify a JWT server-side on every protected route and return `401` on missing/invalid
- [ ] Pin the verification algorithm and explain the `alg: none` bypass
- [ ] Name the localStorage vs httpOnly-cookie tradeoff (XSS vs CSRF)
- [ ] Set secure cookie flags (`httpOnly`, `Secure`, `SameSite`) when using cookie auth
- [ ] Hash passwords with bcrypt/argon2 instead of storing them in any reversible form

## Stage 8 - Real-time & interactivity

- [ ] Implement SSE streaming and flush each chunk instead of buffering the whole response
- [ ] Disable proxy buffering so a stream survives end to end (nginx `X-Accel-Buffering: no`)
- [ ] Broadcast a message to every connected client with per-subscriber queues
- [ ] Clean up a subscriber's queue when its connection drops to avoid a memory leak
- [ ] Build optimistic UI that updates before the server confirms
- [ ] Roll the UI back to its prior state when an optimistic write fails
- [ ] Choose SSE vs WebSockets based on directionality and latency needs

## Stage 9 - Senior: scale & system design

- [ ] Compare resumable-upload options (S3 multipart vs client chunking vs tus) and defend a pick
- [ ] Design a schema change that ships with zero downtime (expand → backfill → contract)
- [ ] Decide what to cache, where, and how to invalidate it cleanly
- [ ] Reason about read/write paths and where a bottleneck will appear under load
- [ ] Take a vague requirement to a defended end-to-end design across UI, API, and data
- [ ] Explain the failure modes of your design and what you'd monitor

## Can't tick it? Prove it.

Every box you can't check is a real broken system waiting on HeyDevJob - a live cloud workspace you fix from your browser, no setup. Ship the fix and it lands on a portfolio a hiring manager can open and click. The junior tier is free.

**Start your portfolio →** [heydevjob.com/fullstack](https://heydevjob.com/fullstack)
