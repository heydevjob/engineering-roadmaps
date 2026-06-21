# Block a Path Traversal Vulnerability

**Role:** Security Engineer · **Level:** Junior · **Type:** Patch · **Skills:** path traversal, file handling, Flask

> The file endpoint serves from an uploads folder by name. Ask it for `../../../etc/passwd` and it cheerfully hands that over too. "Read a file by name" just became "read any file the server can."

## The scenario

The `/files` endpoint serves files from `/workspace/uploads/` by a `name` parameter, but never checks that the resolved path stays inside that directory. An attacker uses `../` sequences to escape it and read arbitrary files - source, configs, keys, `/etc/passwd`. You fix it by resolving the real path and rejecting anything outside the uploads directory.

## The code

The unvalidated file path (`app.py`):

```python
# BUG: No path validation - user can traverse outside uploads directory
filepath = os.path.join(UPLOAD_DIR, filename)

if not os.path.isfile(filepath):
    return jsonify({"error": "File not found"}), 404

return send_file(filepath)
```

The exploit:

```text
$ curl "http://localhost:8000/files?name=../../../etc/passwd"
root:x:0:0:root:/root:/bin/bash      # <- arbitrary file read
```

## How you'll approach it

1. **Prove the traversal.** `../../../etc/passwd` escapes the uploads dir because `os.path.join` happily follows `..`.
2. **Resolve, then check.** Compute the real absolute path (`os.path.realpath`) and confirm it still starts with the uploads directory - reject (403) if not.
3. **Prefer the safe API.** Note that `send_from_directory` (Flask) / `res.sendFile` with a `root` (Express) do this check for you.
4. **Verify.** The traversal payload now 403s; legitimate filenames still serve.

## What you'll learn

- How `../` turns "serve a file by name" into arbitrary file read
- Validating the *resolved* path, not the raw input (which encoding/`..` defeats)
- Using framework file-serving APIs that bound the path for you
- Confirming the fix against the original traversal payload

## What it proves

You can find and fix path traversal - a broken-access-control vulnerability that exposes whatever the process can read - and you fix it by bounding the resolved path, not by filtering strings.

> Resume-ready: *Remediated a path-traversal vulnerability by validating the resolved file path against the intended directory, closing an arbitrary-file-read path.*

## On the roadmap

Part of the [Security Engineer Roadmap](../../roadmaps/security.md) - **Stage 3: Broken Access Control** → Path traversal.

---

**Build it for real.** This is ticket **SE-106** on HeyDevJob - the real traversable endpoint above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Block a Path Traversal Vulnerability on HeyDevJob →](https://heydevjob.com/security)
