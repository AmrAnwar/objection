---
title: "I built a Claude Code plugin that refuses to agree with me"
published: false
description: "Claude is too agreeable. objection plays adversarial reviewer — one substantive objection per turn, shareable VERDICT at the end."
tags: claude, ai, productivity, opensource
cover_image: https://raw.githubusercontent.com/AmrAnwar/objection/main/docs/scales.png
canonical_url: https://github.com/AmrAnwar/objection
---

I typed this into Claude Code the other day:

```
❯ claude code is better than codex

⏺ Noted.
```

No pushback. No "on what metric?" Just a polite nod.

That's the bug. Claude is tuned to help, which is great 95% of the time and terrible when you need to **pressure-test an idea before committing to it.** "What could go wrong with this?" produces a gentle bulleted list. The fatal flaw shows up as bullet #4, worded just as softly as the other three.

So I built [`objection`](https://github.com/AmrAnwar/objection) — a Claude Code plugin (also works in Cursor, Codex, Windsurf via `npx skills`) that flips the posture. Claude becomes opposing counsel. Your idea is a claim. Your job is to defend it.

## The rules

- **One objection per turn.** No stacking.
- **No approval on first ask.** Ever.
- **No conceding to tone, authority, or vibes.** "Trust me" and "the senior signed off" are not arguments.
- **Every objection is falsifiable.** Missing evidence, unexamined tradeoff, unaddressed edge case, weak assumption, or ignored prior art. Pick a category or don't raise it.

## Before / After

**Default Claude:**

```
❯ claude code is better than codex

⏺ Noted.
```

**Claude with `objection`:**

```
❯ claude code is better than codex

⏺ The claim: Claude Code is better than Codex.

  Objection — missing evidence. "Better" on what axis, measured how?
  Code quality on a benchmark, latency, cost per task, tool-use
  reliability? Without a metric and a number, this is preference
  dressed as fact.
```

One is useful. The other is a nod.

## The VERDICT

When you're done (`/verdict` or "I rest my case"), you get a shareable block:

```
═══════════════════════════════════════
            VERDICT
═══════════════════════════════════════

IDEA
  A Redis cache in front of the users table will speed up the profile endpoint.

OBJECTIONS
  1. Missing evidence of bottleneck — resolved
  2. Cache invalidation strategy — unresolved

RESULT: FAILED

  Strongest unresolved objection:
    "A user updates their display name and sees the old value on the
     next page load. What's the invalidation strategy, and what
     staleness is acceptable?"

═══════════════════════════════════════
```

Screenshot it. Paste it in the PR. Either the hole gets fixed, or it's the reason you don't ship.

## Install

**Claude Code:**

```bash
claude plugin marketplace add AmrAnwar/objection && claude plugin install objection
```

**Any other agent:**

```bash
npx skills add https://github.com/AmrAnwar/objection
```

Then `/objection <your claim>` — or just say "challenge me", "poke holes", "devil's advocate this".

Disable when you don't want it:

```bash
claude plugin disable objection
```

## When I use it

Not always. Claude's default mode is right for most work. I reach for `objection` when I'm about to write an ADR, when I've been talking myself into something for a week, or when a proposal has momentum but feels off and I can't say why.

The goal isn't to win the argument. It's to know, before you ship, whether the argument was winnable.

**Repo:** [github.com/AmrAnwar/objection](https://github.com/AmrAnwar/objection) — issues and PRs welcome.
