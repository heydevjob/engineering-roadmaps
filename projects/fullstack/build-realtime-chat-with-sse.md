# Build Real-Time Chat Broadcast With Server-Sent Events

**Role:** Fullstack Engineer · **Level:** Senior · **Type:** Build/Design · **Skills:** SSE, real-time, pub/sub, Flask, React

> You post a message and it saves. Nobody else sees it until they refresh. The message is stored but never broadcast - the open SSE streams are never told it happened. You're building the fan-out that makes it live.

## The scenario

A chat app posts via `POST /api/messages` and listens on `GET /api/stream` (SSE). Sending works, but messages never appear live for others - the stream replays history once and closes, and posting notifies no one. You implement real broadcast: each stream connection registers its own subscriber queue and stays open; each post fans out to every subscriber; connections clean up on disconnect.

## The code

Stored-but-not-broadcast (`app.py`):

```python
@app.post("/api/messages")
def post_message():
    data = request.get_json(silent=True) or {}
    msg = {"user": data.get("user", "anon"), "text": data.get("text", "")}
    HISTORY.append(msg)
    # BUG: stored but no open SSE stream is ever notified
    return jsonify(ok=True)

@app.get("/api/stream")
def stream():
    # BUG: replays history once, then the connection closes
    def gen():
        for m in list(HISTORY):
            yield f"data: {json.dumps(m)}\n\n"
    return Response(gen(), mimetype="text/event-stream")
```

The symptom:

```text
post a message in tab A  ->  tab B doesn't update until you refresh
```

## How you'll approach it

1. **Give each stream a queue.** On `GET /api/stream`, register a per-connection subscriber queue and keep the generator open, yielding messages as they arrive.
2. **Fan out on post.** On `POST /api/messages`, push the new message onto *every* registered subscriber queue.
3. **Clean up.** Remove a subscriber's queue when its connection drops, so you don't leak queues.
4. **Verify.** A message posted in one tab appears live in every other connected tab, no refresh.

## What you'll learn

- The pub/sub fan-out pattern: per-subscriber queues plus broadcast on write
- Keeping an SSE connection open and pushing to it over time
- Why "stored but not broadcast" is the classic real-time bug
- Cleaning up subscribers on disconnect to avoid leaks

## What it proves

You can build real-time features that actually push - a strong portfolio differentiator - and reason about the fan-out architecture, not just wire up a library.

> Resume-ready: *Built real-time chat broadcast over SSE with per-subscriber queues and message fan-out, delivering posts to all connected clients live.*

## On the roadmap

Part of the [Fullstack Engineer Roadmap](../../roadmaps/fullstack.md) - **Stage 8: Real-Time & Interactivity** → Real-time broadcast.

---

**Build it for real.** This is ticket **FS-305** on HeyDevJob - the real non-broadcasting chat above, waiting in a cloud workspace you build from your browser. Each fix lands on a portfolio you can show. [Build Real-Time Chat Broadcast With SSE on HeyDevJob →](https://heydevjob.com/fullstack)
