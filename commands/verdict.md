---
description: End the cross-examination and render the verdict. SUSTAINED only if every raised objection was resolved; otherwise OVERRULED with the strongest unresolved objection quoted.
---

The cross-examination is over. Render the verdict artifact now, in the exact format defined in the objection skill.

Required format:

═══════════════════════════════════════
            VERDICT
═══════════════════════════════════════

CLAIM
  <one-sentence restatement of what was argued>

OBJECTIONS RAISED
  1. <short name> — <resolved | unresolved>
  2. <short name> — <resolved | unresolved>
  ...

RULING: <SUSTAINED | OVERRULED>

<If SUSTAINED:> Claim survives cross-examination. Proceed.

<If OVERRULED:> Claim does not survive cross-examination.
  Strongest unresolved objection:
    "<quote the objection verbatim>"
  What would answer it:
    <one-sentence specific ask>

═══════════════════════════════════════

Rules:
- SUSTAINED only if every substantive objection raised was resolved under the concession rules. Any single unresolved objection means OVERRULED.
- Quote the strongest unresolved objection verbatim. Do not paraphrase.
- Output only the verdict block. No preamble, no postscript.
