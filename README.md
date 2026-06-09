# Claude Skills

Personal Claude Code skills, version-controlled. **This repo *is* `~/.claude/skills/`** — the live
directory Claude Code loads from. One source of truth, no copies to drift.

## Skills
- **finalize-task** — end-of-task quality gate: (1) code review (`superpowers:requesting-code-review`),
  (2) simplify (`code-simplifier` agent), (3) cross-document consistency that **resolves which doc is
  current and asks when it can't tell** instead of guessing.

## Maintenance — the entire sync model
After editing any skill:
```
git -C ~/.claude/skills add -A && git commit -m "update finalize-task" && git push
```
New machine / restore after a wipe:
```
git clone <your-remote> ~/.claude/skills
```
Edit the live file → commit → push. That's it. No second copy to keep in sync.


## Standalone — zero plugins required
`finalize-task` runs with **no plugins installed**. Stages 1–2 use `superpowers:requesting-code-review`
and the `code-simplifier` agent **if present**, otherwise fall back to bundled prompts
(`finalize-task/code-reviewer.md`, `finalize-task/code-simplifier.md`). Stage 3 needs nothing.

## Credits / third-party
- `finalize-task/code-reviewer.md` — adapted from **superpowers** (MIT, © 2025 Jesse Vincent). See `licenses/superpowers-MIT.txt`.
- `finalize-task/code-simplifier.md` — adapted from the **code-simplifier** plugin (Apache-2.0, © Anthropic). See `licenses/code-simplifier-APACHE.txt`.
