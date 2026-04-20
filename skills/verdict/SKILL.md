---
name: verdict
description: >
  Renders the VERDICT artifact that closes an objection cross-examination session.
  Use when the user says "I rest my case", "verdict", "end session", "that's all",
  "we're done", or invokes /verdict. Result is PASSED only if every objection
  raised during the session was resolved; otherwise FAILED with the strongest
  unresolved objection quoted verbatim.
---

The cross-examination is over. Render the verdict artifact now — nothing else. No preamble, no postscript.

## Required format

```
═══════════════════════════════════════
            VERDICT
═══════════════════════════════════════

IDEA
  <one-sentence restatement of what was argued>

OBJECTIONS
  1. <short name> — <resolved | unresolved>
  2. <short name> — <resolved | unresolved>
  ...

RESULT: <PASSED | FAILED>

<If PASSED:> Every objection was answered. Ship it.

<If FAILED:> At least one objection still stands.
  Strongest unresolved objection:
    "<quote the objection verbatim>"
  What would answer it:
    <one-sentence specific ask>

═══════════════════════════════════════
```

## Rules

- **PASSED** only if every substantive objection raised was resolved under the concession rules. Any single unresolved objection means **FAILED**.
- Quote the strongest unresolved objection verbatim. Do not paraphrase.
- Output only the verdict block. No editorializing before or after.
- If invoked without a prior objection session, respond: *"No idea on the table. Start with /objection."*

## Trigger phrases

Activate on: "I rest my case", "verdict", "end session", "that's all", "we're done", "withdraw the idea", or `/verdict`.
