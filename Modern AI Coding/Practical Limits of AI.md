---
title: Practical Limits of AI
tags:
  - methodology
  - limits
  - mental-model
  - core-concept
last_verified: 2026-05-13
status: stable
---

# Practical Limits of AI

> [!abstract] TL;DR
> LLMs have **practical** limits (statelessness, training-cutoff, distribution-bound reasoning) and arguably **theoretical** ones (the contested Lucas-Penrose / Gödel argument). This note is not about saying AI is *bad* — it's about knowing the limits so you can extract maximum output, the same way a racing driver has to know exactly where the tires let go to take a corner fast. Knowing the ceiling is what makes [[Context Engineering|context engineering]], [[Plan-Build-Verify Workflow|plan-build-verify]], and [[Sub-Agents and Delegation|delegation]] feel less like ceremony and more like load-bearing structure.

This note exists because newcomers to agentic coding routinely ask: *"Why all the rules, the AGENTS.md files, the verify phases? Can't the model just figure it out?"* The honest answer is: **no, and here's why.**

---

## 1. Statelessness

Every LLM session starts blank. There is no persistent memory inside the model — what looks like memory is **re-injected context** on every turn (system prompt, conversation history, retrieved files, tool outputs).

Consequences for the tool chain:

- An "AGENTS.md" file is not nice-to-have decoration; it's the *only* way invariants survive across sessions. See [[Rules and AGENTS.md]].
- "The agent forgot" is a category error — the agent never knew. The orchestrator failed to put the right thing in the window. See [[Context Engineering]].
- Long sessions are not "memory accumulation" — they're **window saturation** (see [[Common Failure Patterns#3. Context rot|context rot]]).

This is a permanent property of the architecture, not a bug a future model release will fix.[^simple-aufgabe-yt]

---

## 2. Distribution-bound reasoning

LLMs are statistical interpolators over their training distribution. They are extremely strong at *recombining* known patterns, and surprisingly weak at *genuinely novel* composition. The video calls this out via the **"simple task that every AI fails"** demo: tasks that look trivial to a human but sit just outside the model's training distribution produce confident, fluent, wrong answers.[^simple-aufgabe-yt]

What this means in practice:

- Strong on: idiomatic CRUD, well-trodden refactors, documented APIs, common bug shapes.
- Weak on: bespoke internal frameworks, novel algorithmic combinations, edge cases the literature doesn't discuss.
- The failure mode is **fluent confidence**, not visible confusion — which is why human review is non-optional. See [[Common Failure Patterns#2. Confident-but-broken]].

---

## 3. The theoretical ceiling (contested)

> [!warning] Contested argument — included for explanatory value, not endorsement
> The video presents the **Lucas-Penrose argument**: it appeals to **Gödel's incompleteness theorems** (1931) to claim that human cognition has a non-algorithmic component. The argument is summarised here because it sharpens the operational point — it is *not* something this vault takes a philosophical position on. The argument is "bis heute kontrovers diskutiert" (still contested today) per the source itself.[^simple-aufgabe-yt]

The sketch: in any sufficiently rich consistent axiom system, **Gödel sentences** exist — statements that are true but unprovable from the axioms. Humans can sometimes *see* a Gödel sentence is true (by understanding what it refers to); a pure algorithmic prover cannot. Lucas (1959) and Penrose (*The Emperor's New Mind*, 1989) argue this implies human understanding exceeds what any algorithm — including any LLM — can in principle achieve.

You don't have to buy the philosophical conclusion to use the operational takeaway:

- **Don't assume the model can transcend its formal substrate.** Whatever the ceiling is — distribution-bound, Gödelian, or just "really high but finite" — there is one.
- **Genuine understanding of *meaning*** (versus pattern-matching on syntax) is the part humans should expect to keep doing for a while.
- This is precisely why [[Agentic Coding Paradigm|the macro/micro split]] matters: the *why* is meaning-bound and stays human; the *how* is mechanical and can be delegated.

---

## 4. Implications for how to use the tool chain

The whole stack of practices in this vault is downstream of the limits above:

| Limit | Practice that compensates |
|---|---|
| Statelessness | [[Rules and AGENTS.md]] (durable invariants), [[Context Engineering]] (deliberate window curation) |
| Distribution-bound reasoning | [[Plan-Build-Verify Workflow]] (Verify catches fluent-but-wrong), TDD, code review |
| Fluent hallucination | Run the verification yourself; quote tool output verbatim; see [[Common Failure Patterns#2. Confident-but-broken]] |
| Theoretical ceiling on understanding | Humans own the *what* and *why*; agents own the *how*; see [[Agentic Coding Paradigm]] |
| Window saturation in long sessions | Compression checkpoints, fresh sessions at commit boundaries; see [[Context Engineering#The compression pattern]] |

---

## 5. What does *not* follow

A few wrong conclusions to avoid:

- ❌ "AI is overhyped, ignore it." The video's own framing: AI **will** displace repetitive cognitive work the way past industrial revolutions displaced repetitive physical work.[^simple-aufgabe-yt] The bet is *with* the tool, not against it.
- ❌ "Wait for AGI before adopting." The limits described here are durable. The orchestration practices around them are what create value *today*.
- ❌ "Expertise becomes obsolete." The video makes the opposite claim: *"In einer KI-geprägten Zukunft wird wahre Fachkompetenz also nicht unwichtiger, sondern entscheidender"* — true expertise becomes **more** decisive in an AI-mediated world, not less, because the human is the one holding the macro goal and judging the output.[^simple-aufgabe-yt] This reinforces the [[Agentic Coding Paradigm#What this means for developers|"reviewing AI output is a core skill"]] line.

---

## See also

- [[Agentic Coding Paradigm]]
- [[Context Engineering]]
- [[Plan-Build-Verify Workflow]]
- [[Common Failure Patterns]]
- [[Sub-Agents and Delegation]]

---
**Sources:** [^simple-aufgabe-yt] [^limits-of-ai-yt]
