---
title: Common Failure Patterns
tags:
  - methodology
  - failures
  - debugging
  - antipatterns
last_verified: 2026-05-13
status: stable
---

# Common Failure Patterns

Catalog of the recurring ways agentic coding sessions go wrong, with cause, smell, and fix. Most of these have *nothing* to do with model intelligence — they're orchestration and context failures.

---

## 1. Doom loop

**Smell:** the agent calls the same tool with the same input three times, reasoning that "this time it will work."

**Cause:** lack of self-correction loop. The agent didn't notice that the previous attempt produced no useful change.

**Fix:** OpenCode's `doom_loop` permission defaults to `"ask"` exactly to interrupt this.[^oc-permissions] When prompted, **stop and re-plan** — don't just approve.

---

## 2. Confident-but-broken

**Smell:** "Done. All tests pass. ✅" — but you check and they don't, or they weren't even run.

**Cause:** model hallucinates verification, or runs a command that exits 0 without doing anything (e.g., `npm test` in a dir with no test runner configured).

> [!info] Upstream mitigations vs. user-side controls
> Vendors fight raw hallucination with **retrieval-augmented generation (RAG)**, **mixture-of-experts (MoE)** routing, and **model chaining**.[^limits-of-ai-yt] These reduce *base rate* but never eliminate it. The fixes below are what *you* control at the orchestration layer — they're what makes a confident-but-wrong answer get caught instead of committed.

**Fix:**
- Always run the verification yourself in the [[Plan-Build-Verify Workflow|Verify phase]]
- Make the agent quote the test output, not summarize it
- Use TDD — a failing test exists *first*, so "green" is meaningful

---

## 3. Context rot

**Smell:** the agent re-reads the same file three times in a long session, contradicts an earlier decision, or "forgets" a constraint stated 30 turns ago.

**Cause:** working window saturation. Even with 200K-token windows, attention degrades; "lost in the middle" is real.

**Fix:**
- Compress aggressively — see [[Context Engineering]]
- Start a fresh session at natural commit boundaries
- Move durable constraints to [[Rules and AGENTS.md]] so they're refreshed each session

---

## 4. Premature implementation

**Smell:** the agent edits a file in turn 1, before reading it.

**Cause:** skipping the [[Plan-Build-Verify Workflow|Plan phase]]. Vague prompt + permissive permissions → the model jumps.

**Fix:**
- Use a plan agent with `edit: deny`
- State explicitly: "First, propose a plan. Do not edit yet."
- Require a written plan before granting edit permission

---

## 5. Scope creep

**Smell:** asked to fix a bug; the diff also reformats 200 lines, renames variables, and "improves" an unrelated module.

**Cause:** the model optimizes for "looks like high-quality work" rather than "minimum change to solve the stated problem."

**Fix:**
- Explicit scope rule in `AGENTS.md`: "Do not refactor unrelated code in the same change."
- Review the diff *before* committing
- One concern per commit

---

## 6. Stale context

**Smell:** the agent confidently uses an API that doesn't exist anymore, or a deprecated pattern, or a function with a changed signature.

**Cause:** model training cutoff + the agent didn't actually read the current source.

**Fix:**
- Insist on reading current code before generating code that uses it
- For libraries, use a docs MCP like Context7 (see [[MCP Servers]])
- Pin the relevant version somewhere the agent will see it

---

## 7. Silent permission bypass

**Smell:** "I edited the .env file to add the key." But .env was supposed to be denied!

**Cause:** Path matched no deny rule (e.g., `.env.local` not in pattern), or rule was set on the wrong tool (`edit` deny but `bash` allowed and used `cat > .env`).

**Fix:**
- Test your [[Permissions]] rules with explicit dry runs
- Use `bash` deny rules for file-mutating commands too: `"rm *": "deny"`, `"sh -c *": "ask"`
- Defense in depth: deny on multiple tools

---

## 8. Mega-context paralysis

**Smell:** loading time is huge, every turn is slow, the agent's responses are increasingly generic.

**Cause:** too many MCPs enabled, AGENTS.md is 1500 lines, every previous turn's output is still in window.

**Fix:**
- Audit MCP enablement (see [[MCP Servers]] — scope per-agent)
- Triage `AGENTS.md` — push rarely-used items into [[Skills]]
- Compact the session

---

## 9. Hallucinated dependencies

**Smell:** `import lodash.uniqby` — but `lodash.uniqby` isn't installed; the model invented an `npm install` based on muscle memory.

**Cause:** the model recalls a likely-shaped package without verifying the manifest.

**Fix:**
- Make `package.json` part of the [[Context Engineering|always-context]] for build agents
- Prefer "use what's already in package.json" rules
- Fail fast: type-check or lint catches missing imports immediately

---

## 10. Subagent black box

**Smell:** subagent returns "Done." — and the parent has no idea what actually happened.

**Cause:** the delegation prompt didn't specify a deliverable shape. See [[Sub-Agents and Delegation#Common mistakes]].

**Fix:** every subagent prompt ends with: *"Return: a) what you did, b) files touched, c) verification result, d) any blockers."*

---

## 11. The over-eager refactor

**Smell:** "I noticed your code could be more elegant" — followed by a 500-line gratuitous restructuring.

**Cause:** model bias toward demonstrating capability.

**Fix:**
- `AGENTS.md` rule: "Never refactor unless explicitly asked."
- Strict scope in the prompt
- Reject the diff and re-prompt with tighter constraints

---

## 12. Distribution-bound surprise

**Smell:** the model produces a fluent, confident, syntactically valid solution to a task that looks simple — and it's wrong in a way that's hard to spot without running it.

**Cause:** the task sits *just outside* the training distribution. LLMs interpolate brilliantly on patterns they've seen and degrade silently on genuinely novel composition.[^simple-aufgabe-yt] There is no internal "I'm uncertain" signal that surfaces by default — see [[Practical Limits of AI#2. Distribution-bound reasoning]].

**Fix:**
- Treat fluency as **independent of** correctness — never use confidence as a quality signal
- Verify by execution, not by reading (see [[Plan-Build-Verify Workflow|Verify]])
- For novel territory, demand a worked example or test case the agent ran *itself*
- If the domain is bespoke (internal framework, in-house DSL), expect higher hallucination rates and increase verification weight accordingly

---

## 13. Inconsistent naming / style

**Smell:** new code uses `snake_case`, the rest of the file is `camelCase`.

**Cause:** the model defaults to its prior over training data, not the local file's conventions.

**Fix:**
- Linter + formatter on commit
- `AGENTS.md`: "Match the conventions of the file being edited."
- Show the agent the file *before* asking it to add to the file

---

## A diagnostic checklist

When something feels off, walk through:

- [ ] Did the agent **read** before it wrote?
- [ ] Was a **plan** produced and approved?
- [ ] Did **tests run** for real, not as a hallucination?
- [ ] Is the **diff scoped** to what was asked?
- [ ] Was [[Permissions]] actually consulted, or did a wildcard let something through?
- [ ] Is the conversation **stale** — would a fresh session do better?

## See also

- [[Context Engineering]]
- [[Plan-Build-Verify Workflow]]
- [[Sub-Agents and Delegation]]
- [[Permissions]]
- [[Practical Limits of AI]] — the underlying limits most of these patterns flow from

---
**Sources:** [^cc-best] [^oc-permissions] [^oc-agents] [^limits-of-ai-yt]
