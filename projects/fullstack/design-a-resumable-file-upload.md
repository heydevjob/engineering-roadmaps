# Design a Resumable Large-File Upload

**Role:** Fullstack Engineer · **Level:** Senior · **Type:** Design · **Skills:** system design, AWS S3, multipart, resumable uploads

> Users upload 2 GB videos. The connection drops at 1.8 GB and they start over from zero. You're designing the fix - and the senior part isn't the code, it's picking from three real approaches and defending the call.

## The scenario

The current upload (POST the whole file to the server, server PUTs to S3) can't resume - drop near the end and you restart. You read `design.md` (which compares S3 Multipart Upload, client-chunking with server reassembly, and the tus protocol), choose one in `decision.md` with a short justification, then implement the core endpoints for that approach in `impl.py`.

## The code

The skeleton with three sketched approaches, none implemented (`impl.py`):

```python
# ─── APPROACH A: S3 Multipart
# POST /init → {uploadId}
# POST /sign-part → {url}
# POST /complete → {ok, location}
#
# ─── APPROACH B: chunking + server reassembly
# POST /chunk?uploadId=... → {ok}
# POST /finalize → {ok}
#
# ─── APPROACH C: tus
# POST /files → 201 Location: /files/<id>
# HEAD /files/<id> → Upload-Offset
# PATCH /files/<id> → 204

# TODO: implement only the endpoints for your chosen approach.
```

The task:

```text
no endpoints implemented; three architectures on the table; no decision made
```

## How you'll approach it

1. **Weigh the three.** S3 Multipart + presigned part URLs (offload bytes to S3), client-chunking + server reassembly (full control, more server work), or tus (standard protocol, client libraries). Each ships in production.
2. **Decide and justify.** Write a 3-5 sentence rationale in `decision.md` - the senior skill is defending the trade-off, not knowing one "right" answer.
3. **Implement the core endpoints.** Build the three endpoints for your chosen approach (e.g. `/init`, `/sign-part`, `/complete` for Multipart).
4. **Make resume work.** A resumed upload should list what's already done and skip it - that's the whole point.

## What you'll learn

- The resumable-upload approaches and their trade-offs
- Why offloading parts to S3 with presigned URLs scales better than proxying bytes
- Designing endpoints that let a client resume from where it dropped
- Defending an architecture choice - the core of senior interviews

## What it proves

You can take a vague requirement, choose among plausible architectures, justify the call, and implement the core - exactly what a senior "what would you do for X" interview tests.

> Resume-ready: *Designed and built a resumable large-file upload, selecting S3 Multipart with presigned part URLs over chunking and tus, and implementing init/sign/complete with resume support.*

## On the roadmap

Part of the [Fullstack Engineer Roadmap](../../roadmaps/fullstack.md) - **Stage 9: Senior - Scale & System Design** → Designing for scale.

> AWS tickets are free - the cloud runs in your own sandbox, no account or card needed.

---

**Build it for real.** This is ticket **FS-302** on HeyDevJob - the real design-and-build task above, in a sandboxed AWS workspace you work from your browser. Each fix lands on a portfolio you can show. [Design a Resumable Large-File Upload on HeyDevJob →](https://heydevjob.com/fullstack)
