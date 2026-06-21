# Upload Files to AWS S3 From a React Form

**Role:** Fullstack Engineer · **Level:** Junior · **Type:** Build · **Skills:** React, Flask, AWS S3, boto3, multipart

> The React upload form is built and sends the file. The backend that's supposed to catch it and put it in S3 is an empty stub that returns 501. You're building the missing half of the flow.

## The scenario

A React upload form (file input + Upload button + status) is ready and POSTs the file as multipart to `/api/upload` - but the Flask endpoint is empty and returns 501. You implement it: read the uploaded file, push it to the S3 bucket `uploads`, and return the key and URL so the UI flips from "idle" to "Uploaded."

## The code

The empty endpoint (`app.py`):

```python
@app.post("/api/upload")
def upload():
    # TODO: read request.files['file'], PUT to s3 bucket 'uploads'
    abort(501)
```

The symptom:

```text
select a file, click Upload  ->  501 Not Implemented
# UI never shows the uploaded file
```

## How you'll approach it

1. **Read the multipart file.** Pull the file from `request.files['file']` on the Flask side.
2. **Put it in S3.** Use boto3 to upload the file's bytes to the `uploads` bucket under a key (the filename, or a generated one).
3. **Return the result.** Respond `{ok: true, key, url}` so the React UI can show "Uploaded → /uploads/<key>".
4. **Verify.** Uploading a file returns 200 with the key, and the object lands in the bucket.

## What you'll learn

- The full upload flow: browser multipart POST → Flask → boto3 → S3
- Reading an uploaded file server-side from a multipart request
- Putting an object in S3 and returning a usable reference
- The foundation every upload variation (presigned URLs, progress, large files) builds on

## What it proves

You can build the complete upload flow across the stack - the most common fullstack-AWS interview task - not just the frontend or just the backend.

> Resume-ready: *Built an end-to-end file upload - React multipart form to a Flask/boto3 endpoint that stores the file in S3 and returns its key and URL.*

## On the roadmap

Part of the [Fullstack Engineer Roadmap](../../roadmaps/fullstack.md) - **Stage 5: Building Full Features** → File upload.

> AWS tickets are free - the cloud runs in your own sandbox, no account or card needed.

---

**Build it for real.** This is ticket **FS-102** on HeyDevJob - the real upload form + empty endpoint above, in a sandboxed AWS workspace you build from your browser. Free on the junior tier, no card, no setup. [Upload Files to AWS S3 From a React Form on HeyDevJob →](https://heydevjob.com/fullstack)
