[Home](../../README.md) › [Fullstack Projects](README.md) › **Fix a React useState Stale-State Bug**

# Fix a React useState Stale-State Bug

`Fullstack` · `🟢 Junior` · `🔍 Debug` · `Ticket FS-101`

**Skills** — React · useState · stale closures · TypeScript

> The "+3" button only adds 1. The handler calls `setCount(count + 1)` three times - and all three see the same stale `count`. This is the React state bug everyone hits exactly once and never forgets.

> [!NOTE]
> **The scenario**
> A counter app has a "+3" button that increments by only 1. The `addThree` handler calls `setCount(count + 1)` three times, but each call captures the same `count` from render time, so React collapses them into a single update. You fix it with the functional updater form so each call sees the latest value.

## 🧩 The code

The three updates that all read the same stale `count` (`App.tsx`):

```tsx
// each call captures the same `count` from this render - React batches
// the three identical updates into one, so +3 only adds 1
function addThree() {
  setCount(count + 1);
  setCount(count + 1);
  setCount(count + 1);
}
```

The symptom:

```text
click "+3"  ->  count goes from 0 to 1   (expected 3)
```

## 🛠️ How you'll approach it

1. **See why it's 1, not 3.** All three `setCount(count + 1)` calls close over the same `count` (say `0`), so each computes `1`, and React batches them into a single `setCount(1)`.
2. **Use the updater form.** `setCount(c => c + 1)` receives the *latest* state each time, so three calls compound to +3.
3. **Recognize the pattern.** The same closure trap hits async callbacks, rapid typing, and any "update based on previous state."
4. **Verify.** "+3" now adds 3; "+1" still adds 1.

## 🎓 What you'll learn

- How React batches state updates and why stale closures form
- The functional updater form `setState(prev => ...)` and when it's required
- Why `setState(value)` looks fine on single calls but fails when compounded
- Spotting this class of bug in callbacks and event handlers

## 🏆 What it proves

You understand React state at the level that separates "I followed a tutorial" from "I can debug a real state bug" - the most common frontend interview gotcha.

> [!TIP]
> **Resume-ready** — *Fixed a React stale-state bug by switching to the functional updater form, correcting an update that batched three increments into one.*

## 🗺️ On the roadmap

Part of the [Fullstack Engineer Roadmap](../../roadmaps/fullstack.md) - **Stage 1: React & Component State** → State updates & re-renders.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **FS-101** on HeyDevJob - the real stale-state counter above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup.
> [Fix a React useState Stale-State Bug on HeyDevJob →](https://heydevjob.com/projects/react-usestate-stale-state-project)

**Explore Fullstack** · [📍 Roadmap](../../roadmaps/fullstack.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/fullstack.md) · [✅ Checklist](../../checklists/fullstack.md)

[Next: Stop the Form From Reloading on Submit ▶](stop-the-form-reloading-on-submit.md)
