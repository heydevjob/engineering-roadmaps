# Neutralize a Stored XSS in Comments

**Role:** Security Engineer · **Level:** Junior · **Type:** Patch · **Skills:** XSS, output encoding, Express

> The comment box renders whatever users type straight into the page. One comment with `<script>` runs in every visitor's browser. You're escaping the output so the payload becomes text, not code.

## The scenario

A comment feature inserts user-supplied `author` and `text` straight into HTML with no escaping, so an attacker can store a comment containing `<script>` or an event handler that executes in every viewer's browser - stored XSS. You fix it by HTML-escaping every user value before it's inserted into the page.

## The code

The unescaped string interpolation (`server.js`):

```javascript
function renderComments(commentList) {
  return commentList.map(c => `
    <div class="comment">
      <div class="comment-header">
        <strong>${c.author}</strong>
        <span class="date">${c.date}</span>
      </div>
      <div class="comment-body">${c.text}</div>
    </div>
  `).join('');
}
```

The exploit:

```text
comment text: <script>alert(1)</script>
# executes the JavaScript in every viewer's browser instead of showing as text
```

## How you'll approach it

1. **Confirm the sink.** `${c.text}` and `${c.author}` drop raw input into HTML - that's the sink where stored input becomes executable.
2. **Escape at output.** Convert `<`, `>`, `&`, `"`, `'` to HTML entities for every user-controlled value before interpolation, so `<script>` renders as visible text.
3. **Escape the right way.** Encode at output (context-aware), don't try to blocklist-filter the input - that's bypassable.
4. **Verify.** The payload now appears as literal text on the page and runs nothing; legitimate comments still display.

## What you'll learn

- The source-to-sink model: where untrusted input enters and where it becomes dangerous
- Stored vs reflected XSS and how each is triggered
- Fixing it with output encoding, not input blocklists
- Why templating engines that auto-escape (React, Jinja) prevent this by default

## What it proves

You can find and correctly fix one of the most common web vulnerabilities - and you understand why encoding at output beats trying to sanitize input.

> Resume-ready: *Remediated a stored XSS in a comment feature with context-aware HTML output encoding, eliminating script-injection and session-theft risk.*

## On the roadmap

Part of the [Security Engineer Roadmap](../../roadmaps/security.md) - **Stage 1: Web Security Fundamentals** → Cross-site scripting (XSS).

---

**Build it for real.** This is ticket **SE-102** on HeyDevJob - the real vulnerable comment feature above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Neutralize a Stored XSS in Comments on HeyDevJob →](https://heydevjob.com/security)
