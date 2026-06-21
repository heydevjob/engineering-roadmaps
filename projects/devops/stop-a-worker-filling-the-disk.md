# Stop a Worker From Filling the Disk

**Role:** DevOps Engineer · **Level:** Mid · **Type:** Debug · **Skills:** Linux, disk space, logging, log rotation

> Services are failing with "No space left on device." Nothing leaked data - a worker has just been quietly writing a log file the size of a movie, one debug line at a time.

## The scenario

The `/workspace` partition is nearly full and services are starting to fail with "No space left on device." A background data-processing worker was redeployed recently with new logging and has run continuously since. Disk usage climbed gradually over a few hours - the tell that this is accumulation, not a single event.

## The code

The worker was deployed logging at `DEBUG` (`worker.py`):

```python
logging.basicConfig(
    level=logging.DEBUG,
    format="%(asctime)s [%(levelname)s] %(message)s",
)
```

The symptom:

```text
$ du -sh /workspace/*
3.9G   /workspace/worker.log     # <- a multi-GB log file, growing
...
OSError: [Errno 28] No space left on device
```

`DEBUG`-level logging on a busy worker writes a torrent of lines, and nothing is rotating or capping `worker.log`.

## How you'll approach it

1. **Find the space hog.** `df -h` confirms the partition is full; `du -sh /workspace/*` points straight at the giant `worker.log`.
2. **Find why it's huge.** The worker is logging at `DEBUG` - far too verbose for production.
3. **Fix the root cause, then reclaim.** Drop the log level back to `INFO`, then rotate or truncate the bloated file to recover the space. (Truncate safely - don't delete a file the worker still holds open.)
4. **Prevent recurrence.** Note where log rotation / retention would have capped this before it broke anything.

## What you'll learn

- Finding what filled a disk fast with `df` and `du`
- Why a held-open log file keeps eating space even after you "delete" it
- Safe reclamation: truncation vs deletion
- The real fix: appropriate log levels plus rotation/retention so logs can't grow unbounded

## What it proves

You can resolve a disk-pressure incident under time pressure and remove the root cause, not just buy an hour by deleting a file.

> Resume-ready: *Resolved a disk-exhaustion incident caused by unbounded DEBUG logging, reclaimed the space safely, and capped log growth to prevent recurrence.*

## On the roadmap

Part of the [DevOps Engineer Roadmap](../../roadmaps/devops.md) - **Stage 1: Linux & the Command Line** → Filesystems, permissions & disk pressure.

---

**Build it for real.** This is ticket **DO-205** on HeyDevJob - the real disk-filling worker above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Stop a Worker From Filling the Disk on HeyDevJob →](https://heydevjob.com/devops)
