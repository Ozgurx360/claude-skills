# Claude Skills

Personal Claude Code skills, version-controlled.

**This repo _is_ `~/.claude/skills/`** — the live directory Claude Code loads skills from. One source of
truth, no copies to drift.

---

## What's inside

### `finalize-task` — an end-of-task quality gate
Run it before you call any task "done." Three stages, each delegated to subagents so your main context stays clear:

1. **Review** — dispatches a code reviewer over the work product; fix Critical/Important before moving on.
2. **Simplify** — a behavior-preserving simplify pass over just the recently-changed code.
3. **Cross-document consistency — _with authority resolution_.** Fans out parallel agents to find where your
   docs / code / notes disagree, then **decides which one is actually current** — by `CONFIRMED` / `SUPERSEDED` /
   date / version markers, canonical-over-derived — and when it genuinely can't tell, it **stops and asks you
   instead of guessing.**

Stage 3 is the point: most consistency checks only say "these disagree." This one works out _which is right_, or asks.

**Trigger it** by typing `/finalize-task`, or just say "wrap it up" / "finalize" / "end the task."

---

## Install / use

```bash
git clone https://github.com/Ozgurx360/claude-skills.git ~/.claude/skills
```
Then **restart Claude Code** (skills are discovered at startup) and run `/finalize-task`.

Already have a `~/.claude/skills/`? Copy in `finalize-task/` **plus** `licenses/` (so the attribution links resolve).

### Zero plugins required
`finalize-task` runs standalone. Stages 1–2 use the **superpowers** and **code-simplifier** plugins **if you
have them**, otherwise fall back to bundled prompts (`finalize-task/code-reviewer.md`,
`finalize-task/code-simplifier.md`). Stage 3 needs nothing. On a machine that _does_ have the plugins, it
automatically uses those first-party tools instead. (The standalone path is pressure-tested.)

---

## Updating (the whole sync model)

Edit a skill → commit → push:
```bash
git -C ~/.claude/skills add -A && git commit -m "update finalize-task" && git push
```
Restore on a new machine:
```bash
git clone https://github.com/Ozgurx360/claude-skills.git ~/.claude/skills
```
One live file, version-controlled — no second copy to keep in sync.

---

## Layout
```
finalize-task/
  SKILL.md            — the skill: when to use + the 3 stages
  code-reviewer.md    — bundled fallback for stage 1
  code-simplifier.md  — bundled fallback for stage 2
licenses/             — third-party license texts
```

## Credits
- `finalize-task/code-reviewer.md` — adapted from **superpowers** (MIT, © 2025 Jesse Vincent) · `licenses/superpowers-MIT.txt`
- `finalize-task/code-simplifier.md` — adapted from the **code-simplifier** plugin (Apache-2.0, © Anthropic) · `licenses/code-simplifier-APACHE.txt`
