[Home](../../README.md) › [Fullstack Projects](README.md) › **Add Optimistic UI With Rollback**

# Add Optimistic UI With Rollback to a Todo List

`Fullstack` · `🔴 Senior` · `⚡ Optimize` · `Ticket FS-306`

**Skills** — React · optimistic UI · rollback · UX

> Click Add and nothing happens for almost a second - the code waits for the server before showing the todo. You're making it instant: show the item now, and quietly pull it back out if the save fails. The hard part is the rollback nobody remembers.

> [!NOTE]
> **The scenario**
> Adding a todo feels sluggish because the code awaits a simulated server save before rendering the item. You make it optimistic: insert the todo immediately, then await the save - and if it rejects, roll the item back out and show an error. (The `save()` helper rejects when the text is `"fail"` so you can test the rollback path.)

## 🧩 The code

The pessimistic handler that blocks on save (`App.tsx`):

```typescript
async function add() {
    const text = inputRef.current!.value.trim();
    if (!text) return;
    inputRef.current!.value = '';
    setError(null);

    // BUG: pessimistic - blocks on save before showing anything
    try {
      await save(text);
      setTodos((t) => [...t, { id: Date.now(), text }]);
    } catch {
      setError('Could not save');
    }
}
```

The symptom:

```text
click Add -> ~800ms lag before the todo appears
a failed save (text "fail") leaves no trace it was ever attempted
```

## 🛠️ How you'll approach it

1. **Show it first.** Insert the new todo into the list *before* awaiting `save()`, so the UI updates instantly.
2. **Await in the background.** Fire `save(text)` after the optimistic insert.
3. **Roll back on failure.** If `save()` rejects, remove the optimistically-added todo and surface the error - so the UI never claims something saved when it didn't.
4. **Verify.** A normal add feels instant; adding `"fail"` briefly shows then removes the item with an error.

## 🎓 What you'll learn

- The optimistic update pattern: apply locally, confirm or roll back
- Tracking the optimistic item so a failure can undo exactly it
- Why forgetting the rollback makes the UI lie about what persisted
- The UX payoff (Linear, Gmail send/undo) of instant-feeling interactions

## 🏆 What it proves

You can make an app feel instant without lying to the user - implementing optimistic updates *and* the rollback most implementations forget.

> [!TIP]
> **Resume-ready** — *Implemented optimistic UI with rollback for a todo list, making adds feel instant while correctly reverting and surfacing failed saves.*

## 🗺️ On the roadmap

Part of the [Fullstack Engineer Roadmap](../../roadmaps/fullstack.md) - **Stage 8: Real-Time & Interactivity** → Optimistic UI.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **FS-306** on HeyDevJob - the real sluggish todo list above, waiting in a cloud workspace you fix from your browser. Each fix lands on a portfolio you can show.
> [Add Optimistic UI With Rollback on HeyDevJob →](https://heydevjob.com/fullstack)

**Explore Fullstack** · [📍 Roadmap](../../roadmaps/fullstack.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/fullstack.md) · [✅ Checklist](../../checklists/fullstack.md)

[◀ Prev: Stream the AI Chat Response](stream-the-ai-chat-response.md) · [Next: Build Real-Time Chat Broadcast With SSE ▶](build-realtime-chat-with-sse.md)
