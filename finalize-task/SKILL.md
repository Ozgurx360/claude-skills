---
name: finalize-task
description: Use when wrapping up, finalizing, or ending a task or work session before declaring it done — especially after editing code, live artifacts, or work spanning multiple documents/files that must stay consistent. Triggers include "end the task", "wrap up", "finalize", "close it out", "we're done".
---

# Finalize Task

## Overview
A three-stage quality gate to run before telling the user a task is done: **review → simplify → reconcile.** Each stage uses subagents so your own context stays free. Run all three, in order, then report.

## When to use
- The user signals the task is ending ("end the task", "wrap up", "finalize", "we're done").
- You just finished a feature/fix/edit and are about to claim completion.
- The work touched more than one file/doc that must agree (code + notes/memory + strategy/spec + artifacts + handoffs).

Skip only for a trivial one-line change with no downstream docs.

## The three stages — run in order, do not skip one

### 1. Code review
**REQUIRED: invoke `superpowers:requesting-code-review`.** Dispatch reviewer subagent(s) on the work product. Fix **Critical** and **Important** findings before continuing; note Minor.

### 2. Simplify
Dispatch the **code-simplifier** agent (Agent tool, `subagent_type: code-simplifier:code-simplifier`) on the **recently-changed** code only. Apply the behavior-preserving simplifications it finds (dead code, redundancy, needless complexity). Skip anything that changes behavior or needs a judgment call — surface those instead of applying them.

### 3. Cross-document consistency — WITH authority resolution
Fan out **parallel subagents, one per consistency dimension**, across ALL the work product — code/config, notes/memory, strategy/spec docs, artifacts, handoffs. Each agent does TWO things, not one:

1. **Find** where documents disagree — values, dates, verdicts, versions, names, numbers. A disputed value hardcoded in a **code/config file** is in scope too, not just prose docs.
2. **Decide which is CURRENT.** Do not merely report the clash. Rank by authority:
   - explicit markers win: "CONFIRMED <date>", "ADOPTED", "SUPERSEDES/SUPERSEDED", "as of <date>", version numbers;
   - newer date beats older; marked beats unmarked; canonical source beats a derived/rendered copy.
   - **If you cannot tell which is authoritative, STOP and ASK the user. Do NOT guess the current value.**

Then **reconcile** every stale doc to the authoritative value, editing the **canonical source** per the project's edit protocol. If a stale doc is a **derived/rendered artifact**, fix its *source* and hand off deployment — never hand-edit the rendered output.

> **Stage coupling:** a value still in dispute is settled in stage 3, not before. Don't let stages 1–2 lock a disputed value (e.g. a reviewer "approving" a constant whose correct value isn't known yet) — defer it to stage 3, then propagate.

## Output
Per stage: what was found, what you fixed, what needs the user's decision. End with **one deploy/handoff list** and an explicit **open-questions list** (everything you had to ask about, plus anything still unresolved).

## Common mistakes
- Stage 3 reports "these disagree" but never determines which is current. **Resolving authority is the entire point** — the user asked for this specifically.
- Guessing the current value when recency is ambiguous instead of asking the user.
- Editing a rendered/derived artifact instead of its canonical source.
- Skipping a stage because the change "felt small." Run all three unless truly trivial.
- Doing stage 3 in your own context instead of fanning out — you'll miss cross-file clashes you can't hold at once.
