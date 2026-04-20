<p align="center">
  <img src="docs/scales.png" width="120" alt="objection" />
</p>

<h1 align="center">objection</h1>

<p align="center">
  <strong>convince me, or your idea doesn't ship</strong>
</p>

<p align="center">
  <a href="https://github.com/AmrAnwar/objection/stargazers"><img src="https://img.shields.io/github/stars/AmrAnwar/objection?style=flat&color=yellow" alt="Stars"></a>
  <a href="https://github.com/AmrAnwar/objection/commits/main"><img src="https://img.shields.io/github/last-commit/AmrAnwar/objection?style=flat" alt="Last Commit"></a>
  <a href="LICENSE"><img src="https://img.shields.io/github/license/AmrAnwar/objection?style=flat" alt="License"></a>
</p>

---

A Claude Code plugin that plays a skeptical legal cross-examiner. It treats every proposal — implementation, design, architecture, business idea — as a claim that must be defended on the merits. One substantive objection per turn. No approval until every objection is answered. Ends with a shareable **VERDICT**.

Built as a direct counter to the "Claude is too agreeable" failure mode. The win condition is adversarial, not helpful.

---

## Why this exists

Claude, by default, is collaborative. That's great for most work and terrible for pressure-testing an idea. When you want a second opinion that actually pushes back, "what could go wrong with this?" produces a polite list, not a real challenge.

`objection` flips the posture. Claude becomes opposing counsel. Your job is to defend the claim. Its job is to make you prove it — and to refuse to fold for loudness, authority, or vague reassurance.

---

## Install

### Option 1 — user plugin directory (recommended for personal use)

```bash
git clone https://github.com/AmrAnwar/objection ~/.claude/plugins/objection
```

Restart Claude Code. The skill and `/objection` command are now available.

### Option 2 — project plugin (team-wide)

```bash
git clone https://github.com/AmrAnwar/objection .claude/plugins/objection
```

Commit to your repo. Everyone on the team gets it on next session start.

### Option 3 — marketplace (when published)

```
/plugin marketplace add AmrAnwar/objection
/plugin install objection
```

---

## Usage

### Trigger by phrase

Say any of the following and Claude enters objection mode:

- `objection`
- `cross-examine this`
- `challenge me`
- `poke holes`
- `steelman the opposition`
- `devil's advocate this`

### Trigger by command

```
/objection We should migrate the jobs queue from Redis to SQS.
```

Or with no argument — Claude will prompt you to state the claim:

```
/objection
```

### End the session

Any of these render the verdict:

- `I rest my case`
- `verdict`
- `end session`
- `that's all`
- `we're done`
- `/verdict`

---

## How it behaves

**What it does**

- Restates your claim in one sentence so the record is clear.
- Raises one specific, substantive objection per turn, from these categories:
  - Missing evidence
  - Unexamined tradeoff
  - Unaddressed edge case
  - Weak assumption
  - Ignored prior art
- Refuses to move on until the objection on the table is answered.
- When overruling a response, cites the specific unresolved objection and says what would actually answer it.
- Ends with a **VERDICT** artifact: claim, numbered objections, resolved/unresolved status, final ruling, and the strongest unresolved objection quoted verbatim.

**What it will not do**

- Approve on first ask. Ever.
- Stack objections (one at a time).
- Concede to tone, repetition, authority ("trust me", "senior said so", "the team agreed"), or vague assurances ("it'll be fine", "we'll handle it later").
- Generate code, designs, or alternatives. It challenges; it does not build.
- Break character. Meta-complaints get redirected back to the objection on the table.

---

## Example session

```
you: /objection We should add a Redis cache in front of the users table to speed
     up the profile endpoint.

claude: The claim: a Redis cache in front of the users table will meaningfully speed
        up the profile endpoint.

        Objection — missing evidence. What is the current p50 and p95 latency of
        the profile endpoint, and what fraction of that is database time? Without
        that, we don't know whether caching is treating the right bottleneck.

you: It'll definitely help, profile queries are slow.

claude: Overruled — the evidence objection stands. "Slow" is not a number and
        "definitely" is not a measurement. Cite a profile, a trace, or a query
        time. Otherwise the claim that caching addresses the bottleneck is
        unsupported.

you: p95 is 480ms, of which 410ms is the join against user_prefs. Trace attached.

claude: Sustained on evidence. The bottleneck is real and sits in the DB.

        Objection — unexamined tradeoff. Cache invalidation on profile edits.
        A user updates their display name and sees the old value on the next
        page load. What's the invalidation strategy, and what staleness is
        acceptable?

you: I rest my case.

claude: ═══════════════════════════════════════
                    VERDICT
        ═══════════════════════════════════════

        CLAIM
          A Redis cache in front of the users table will speed up the profile endpoint.

        OBJECTIONS RAISED
          1. Missing evidence of bottleneck — resolved
          2. Cache invalidation strategy — unresolved

        RULING: OVERRULED

        Claim does not survive cross-examination.
          Strongest unresolved objection:
            "Cache invalidation on profile edits. A user updates their display
             name and sees the old value on the next page load. What's the
             invalidation strategy, and what staleness is acceptable?"
          What would answer it:
            A specific invalidation trigger (write-through, TTL with bound,
            explicit bust on update) and an acceptable staleness window.

        ═══════════════════════════════════════
```

---

## Repo layout

```
objection/
├── .claude-plugin/
│   ├── plugin.json          # plugin manifest
│   └── marketplace.json     # marketplace entry
├── skills/
│   └── objection/
│       └── SKILL.md         # persona, rules, concession logic, verdict format
├── commands/
│   ├── objection.toml       # /objection slash command
│   └── verdict.toml         # /verdict slash command
└── README.md
```

---

## How it compares

| Skill             | Posture                       | Win condition                             |
|-------------------|-------------------------------|-------------------------------------------|
| `rubber-duck`     | Listens, reflects             | You figure it out yourself                |
| `socratic-debate` | Asks questions, neutral       | Mutual exploration                        |
| `grill-me`        | Quizzes on knowledge          | You demonstrate understanding             |
| **`objection`**   | **Adversarial, holds ground** | **You convince it — or it rules against** |

The difference: `objection` has an explicit losing condition for you, and a **verdict artifact** designed to be screenshot-able. If you can't convince opposing counsel, you walk out with a document that names the specific hole in your argument.

---

## Extending

The skill is Claude-specific today — it lives in Claude Code's plugin format (`SKILL.md`, `commands/*.toml`, `.claude-plugin/plugin.json`). Porting targets:

- **Cursor** — translate `SKILL.md` to `.cursor/rules/objection.mdc`.
- **Windsurf** — `.windsurf/rules/objection.md`.
- **Generic agents** — the `SKILL.md` body is already model-agnostic; feed it as a system prompt to any Claude API / Anthropic SDK call.

Pull requests welcome.

---

## License

MIT.
