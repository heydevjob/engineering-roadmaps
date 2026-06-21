[Home](../../README.md) › [Fullstack Projects](README.md) › **Stop the Form From Reloading on Submit**

# Stop the Form From Reloading the Page on Submit

`Fullstack` · `🟢 Junior` · `🔍 Debug` · `Ticket FS-107`

**Skills** — React · forms · preventDefault · TypeScript

> Add an item, the page flashes, the URL grows a `?item=...`, and your list is gone. The browser is doing its default form submit - a full-page GET - and wiping React's state. One line stops it.

> [!NOTE]
> **The scenario**
> A shopping-list form adds items in React, but every submit triggers a full-page reload that wipes the state - you see a flash and the URL gains a query string. The `handleSubmit` handler updates state but never stops the browser's default form submission. You add `e.preventDefault()` so React controls the update.

## 🧩 The code

The handler that forgets `preventDefault` (`App.tsx`):

```tsx
function handleSubmit(e: React.FormEvent) {
  // BUG: missing e.preventDefault()
  setItems((prev) => [...prev, text]);
  setText('');
}
```

The symptom:

```text
submit the form  ->  page flashes/reloads, URL becomes ?item=value,
                     the item never sticks (React state is wiped)
```

## 🛠️ How you'll approach it

1. **Recognize the default action.** A `<form>` submit makes the browser do a real navigation (a GET) unless you stop it - that reload throws away all React state.
2. **Prevent it.** Call `e.preventDefault()` at the top of `handleSubmit` so the browser stays put and React handles the add.
3. **Know the family.** The same default-action gotcha hits `<a>` clicks and drag-and-drop handlers.
4. **Verify.** Items now add with no flash, no URL change, and the list persists.

## 🎓 What you'll learn

- Why a form submit triggers a full-page navigation by default
- `e.preventDefault()` and where it belongs in a submit handler
- How a reload silently wipes client state
- The other places default browser actions bite (links, drag/drop)

## 🏆 What it proves

You understand the boundary between browser default behavior and your JavaScript - a small but constant source of "why did my state disappear" bugs.

> [!TIP]
> **Resume-ready** — *Fixed a React form that reloaded the page on submit by preventing the browser's default action, preserving client state.*

## 🗺️ On the roadmap

Part of the [Fullstack Engineer Roadmap](../../roadmaps/fullstack.md) - **Stage 2: Forms & User Input** → Controlled forms & submit handling.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **FS-107** on HeyDevJob - the real reloading form above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup.
> [Stop the Form From Reloading on Submit on HeyDevJob →](https://heydevjob.com/fullstack)

**Explore Fullstack** · [📍 Roadmap](../../roadmaps/fullstack.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/fullstack.md) · [✅ Checklist](../../checklists/fullstack.md)

[◀ Prev: Fix a React useState Stale-State Bug](fix-a-react-stale-state-bug.md) · [Next: Fix a Blank React Dashboard (Failed Fetch) ▶](fix-a-blank-react-dashboard.md)
