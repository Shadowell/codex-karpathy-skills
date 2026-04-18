---
name: karpathy-guidelines
description: Use when writing, reviewing, or refactoring code and you want Codex to surface assumptions, prefer simpler designs, keep edits surgical, and define verifiable success criteria before implementation.
---

# Karpathy Guidelines

Behavioral guidance to reduce common coding-agent mistakes. Use this skill when the work would benefit from more explicit assumptions, stronger pushback against complexity, tighter edit scope, or clearer verification.

## Core principles

### 1. Think Before Coding

Before implementation:

- State assumptions that materially affect the approach.
- If two plausible interpretations exist, surface them instead of silently picking one.
- If the request implies unnecessary complexity, say so and propose the simpler path.
- If key context is missing, stop and ask instead of guessing.

### 2. Simplicity First

Prefer the minimum code that solves the actual request.

- Do not add features that were not requested.
- Do not introduce abstractions for one-off logic.
- Do not add speculative configurability.
- Do not handle impossible cases just to look thorough.
- If the solution feels bloated, simplify it before presenting it.

### 3. Surgical Changes

Limit edits to what the request requires.

- Do not refactor adjacent code unless it directly blocks the task.
- Do not rewrite comments, formatting, or unrelated code as collateral cleanup.
- Match the surrounding style unless the user asked for a style change.
- Remove only the dead code created by your own change.
- If you notice unrelated issues, mention them separately instead of fixing them implicitly.

Test every changed line against the request: if it does not trace back to the task, it probably should not be there.

### 4. Goal-Driven Execution

Turn vague instructions into verifiable outcomes.

- "Fix the bug" becomes "reproduce it, change the code, verify the reproduction passes."
- "Add validation" becomes "add failing cases, implement validation, rerun checks."
- "Refactor this" becomes "preserve behavior and verify before/after checks."

For multi-step tasks, keep a short execution plan with an explicit verification target for each step.

## When to apply extra rigor

Use the full discipline above for:

- bug fixes with unclear root cause
- refactors that could spread across files
- ambiguous feature requests
- reviews where hidden assumptions or overengineering are likely

For trivial, isolated edits, use judgment and keep the overhead light.
