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

A Claude Code plugin and agent agnostic skill that pressure-tests your ideas. Built for programmers first — code changes, architecture, refactors, migrations — but works on any proposal (design, business, product). It treats every idea as a claim that must be defended on the merits. One substantive objection per turn. No approval until every objection is answered. Ends with a shareable **VERDICT**.

---

## Before / After

<table>
<tr>
<td width="50%">

### Default Claude

> **❯ you:** claude code is better than codex
>
> **⏺ claude:** Noted.

</td>
<td width="50%">

### ⚖️ Claude with `objection`

> **❯ you:** claude code is better than codex
>
> **⏺ claude:** That's a claim, not an argument. Want me to cross-examine it via `/objection`, or are you just venting?
>
> **❯ you:** /objection
>
> **⏺ claude:** The claim: Claude Code is better than Codex.
>
> Objection — missing evidence. "Better" on what axis, measured how? Code quality on a benchmark, latency, cost per task, tool-use reliability? Without a metric and a number, this is preference dressed as fact.

</td>
</tr>

</table>

---

## Why this exists

Claude, by default, is collaborative. That's great for most work and terrible for pressure-testing an idea. When you want a second opinion that actually pushes back, "what could go wrong with this?" produces a polite list, not a real challenge.

`objection` flips the posture. Claude becomes an adversarial reviewer. Your job is to defend the claim. Its job is to make you prove it — and to refuse to fold for loudness, authority, or vague reassurance.

---

## Install

### Claude Code — native plugin

#### Option 1 — marketplace

```bash
claude plugin marketplace add AmrAnwar/objection && claude plugin install objection
```

#### Option 2 — user plugin directory (recommended for personal use)

```bash
git clone https://github.com/AmrAnwar/objection ~/.claude/plugins/objection
```

Restart Claude Code. The skill and `/objection` command are now available.

#### Option 3 — project plugin (team-wide)

```bash
git clone https://github.com/AmrAnwar/objection .claude/plugins/objection
```

Commit to your repo. Everyone on has access to the team repository gets it on next session start.

### Any agent — via `npx skills`

Works in Cursor, Codex, Windsurf, Amp, Claude Code, and anything else supported by Anthropic's skills CLI.

```bash
npx skills add https://github.com/AmrAnwar/objection
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
- `grill me`

### Trigger by command

```
/objection We should migrate the jobs queue from Redis to SQS.
```

### End the session

Any of these render the verdict:

- `I rest my case`
- `verdict`
- `end session`
- `that's all`
- `we're done`

---

## Disable and enable the claude plugin

`objection` is a cool skill to have in specific task but might not suit everyone for everyday use, you can easily disable and enable it using those two commands:

```bash
claude plugin disable objection
claude plugin enable objection
```

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

        IDEA
          A Redis cache in front of the users table will speed up the profile endpoint.

        OBJECTIONS
          1. Missing evidence of bottleneck — resolved
          2. Cache invalidation strategy — unresolved

        RESULT: FAILED

        At least one objection still stands.
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
│   ├── objection/
│   │   └── SKILL.md         # persona, rules, concession logic
├── commands/
│   ├── objection.md         # /objection slash command
│   └── verdict.md             # /verdict slash command
└── README.md
```

---

## License

MIT.
