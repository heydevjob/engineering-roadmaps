# Shrink a Bloated Docker Image (Multi-Stage Build)

**Role:** DevOps Engineer · **Level:** Junior · **Type:** Optimize · **Skills:** Docker, multi-stage builds, image hygiene

> The image is ~370MB and every deploy drags it across the network. Half of it is a build-time artifact that has no business being in the thing you ship.

## The scenario

The team's Docker image is bloated at ~370MB and deploys are slow. The single-stage Dockerfile produces a 200MB build-time artifact (think a data extract or preprocessing step) that's only needed *during* the build - but because it's all one stage, that 200MB ends up baked into the final runtime image.

## The code

The single-stage Dockerfile as it stands:

```dockerfile
FROM python:3.12-slim
WORKDIR /app

# Pretend this is a build-time data extract / asset bundle / ML weight
# preprocessing step - needed during build, NOT needed at runtime.
RUN dd if=/dev/urandom of=/build-artifact.bin bs=1M count=200 2>/dev/null

# The app itself - no external deps, pure stdlib.
COPY app.py .

CMD ["python", "app.py"]
```

The symptom:

```text
$ docker build -t do114-app .
$ docker images do114-app
REPOSITORY   TAG      SIZE
do114-app    latest   ~370MB     # ~200MB of it is build-artifact.bin
```

## How you'll approach it

1. **See why deleting it later won't help.** A `RUN rm` in a later layer doesn't shrink the image - the 200MB still lives in the earlier layer. Image size is the sum of layers, not the final filesystem.
2. **Split into two stages.** Do the build-time work in a `builder` stage, then `COPY --from=builder` only the artifacts the runtime actually needs into a clean final stage.
3. **Leave the 200MB behind.** The build artifact stays in the builder stage and never reaches the shipped image.
4. **Verify.** Rebuild and check `docker images` - the final image drops to ~150MB.

## What you'll learn

- Why image layers mean "delete it later" doesn't reduce size
- The multi-stage pattern: heavy work in the builder, only artifacts in the runtime
- Choosing a slim runtime base and the size/debuggability trade-off
- Confirming the win by measuring the final image, not the build log

## What it proves

You can cut a bloated production image roughly in half without changing what the app does - speeding every deploy and shrinking the attack surface.

> Resume-ready: *Refactored a single-stage Dockerfile into a multi-stage build, dropping the production image from ~370MB to ~150MB and cutting deploy time.*

## On the roadmap

Part of the [DevOps Engineer Roadmap](../../roadmaps/devops.md) - **Stage 5: Containers** → Docker fundamentals.

---

**Build it for real.** This is ticket **DO-114** on HeyDevJob - the real bloated image above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Shrink a Bloated Docker Image on HeyDevJob →](https://heydevjob.com/devops)
