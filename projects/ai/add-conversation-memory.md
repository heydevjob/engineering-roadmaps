# Add Conversation Memory to a Chatbot

**Role:** AI/ML Engineer · **Level:** Junior · **Type:** Build · **Skills:** LLM, chatbots, conversation history, context windows

> Tell the bot your name, ask about it next message - it has no idea. And long chats slow to a crawl. The history is stored but never sent, and it grows without limit. You're giving the model a memory and a budget.

## The scenario

A support chatbot has two bugs: it has no memory (it only sends the latest message to the LLM, ignoring stored history), and long conversations slow down and time out (history grows unbounded). You forward the full conversation each turn and cap it with a sliding window.

## The code

Both bugs (`app.py`):

```python
# BUG 1: only sends the latest message to the LLM, ignoring conversation history.
messages_to_send = [
    {"role": "system", "content": SYSTEM_PROMPT},
    {"role": "user", "content": user_message}
]

# BUG 2: history grows without limit. No cap on stored messages per session,
# so long conversations overflow the context window and slow down or fail.
```

The symptom:

```text
"My name is Alice" ... next turn: "What's my name?" -> "I don't know"
# and long sessions get progressively slower until they time out
```

## How you'll approach it

1. **Send the history.** Build the `messages` array from the session's stored turns (system + prior user/assistant messages + the new one), not just the latest message.
2. **Understand statelessness.** The LLM remembers nothing between calls - "memory" is whatever you resend each turn.
3. **Cap it.** Keep only the last N turns (a sliding window) so context, cost, and latency stay bounded.
4. **Verify.** The bot recalls earlier facts, and long conversations stay responsive.

## What you'll learn

- Why LLMs are stateless and "memory" means resending context
- Building the `messages` array from conversation history
- Capping history with a sliding window to control cost and latency
- The trade-off between remembering more and the context budget

## What it proves

You understand how conversational LLM features actually work - context is resent, not remembered - and you can keep a chatbot both coherent and fast.

> Resume-ready: *Added sliding-window conversation memory to a chatbot, forwarding history for coherent multi-turn context while bounding cost and latency.*

## On the roadmap

Part of the [AI/ML Engineer Roadmap](../../roadmaps/ai.md) - **Stage 5: Stateful & Streaming Chat** → Conversation memory.

---

**Build it for real.** This is ticket **AI-107** on HeyDevJob - the real forgetful chatbot above, in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Add Conversation Memory to a Chatbot on HeyDevJob →](https://heydevjob.com/ai)
