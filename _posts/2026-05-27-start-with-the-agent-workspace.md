---
title: "Start With the Agent's Workspace, Not the Prompt"
date: 2026-05-27 00:00:00 -0400
categories:
  - AI
tags:
  - ai
  - agents
  - coding-agents
  - context-engineering
  - agentic-engineering
excerpt: "A practical starting point for using coding agents: build a small instruction scaffold, use progressive disclosure, and keep the active instruction tree inside a realistic budget."
toc: false
toc_label: "On This Page"
---

![Mr. Miyagi pruning a tree](/assets/images/posts/start-with-the-agent-workspace/mr_miyagi_pruning_tree.gif)

Some teams start with a coding agent by writing one giant prompt.

I think that is the wrong place to begin.

The first step is not the perfect prompt, regardless of the coding agent of your choice. The first useful artifact is a small scaffold of instruction files that teaches the agent how to work inside the project without making it read the whole project every time.

**Start with the agent's workspace, not the prompt.**

## The Front Door

For coding agents, I like starting with `AGENTS.md` or `CLAUDE.md` instruction files at the root of project directory.

Treat them as the front door, not the whole building.

- **`AGENTS.md`** gives repo-level guidance that many coding agents can use.
- **`CLAUDE.md`** is useful for Claude Code-specific project memory and workflow preferences.

The top-level instruction file should answer a few basic questions:

```text
What is this project?
How do I run it?
How do I test it?
What files should I read before changing common areas?
What rules are always true?
Where are the deeper instructions?
```

That last question matters.

**The right model is progressive disclosure.**

The agent should read the general project instructions first, then only load the specialized instructions needed for the task.

For example:

```text
AGENTS.md
CLAUDE.md
instructions/
  frontend.md
  backend.md
  database.md
  security.md
  testing.md
  release.md
```

If the task is a React UI change, the agent should read `AGENTS.md`, then `instructions/frontend.md`, maybe `instructions/testing.md`, and stop there.
It does not need the release checklist, database migration rules, or production incident process in its working context.

**The point is context economy.**

## Context Engineering, Briefly

Context engineering is the practice of deciding what the model sees:

- project instructions
- source files
- tool output
- memory
- conversation history
- task-specific docs

More context is not always better context.

**Better context is the right context at the right point in the workflow.**

This is also why people talk about avoiding the "dumb zone." As the working context fills up with old conversation, raw logs, repeated steering, stale assumptions, and irrelevant files, the agent can start producing worse answers.

Yes, newer models and coding harnesses are getting much better at context window management: server-side compaction, context editing, better retrieval, and smarter tool use all help.

But operators still need discipline because the harness can help manage the context window but it cannot decide which project rules actually matter.

## The Instruction Budget

Dexter Horthy's "instruction budget" framing is useful because it makes prompt discipline concrete.

The failure mode is simple:

> If you keep adding instructions, eventually the important rules become harder for the model to reliably follow.

This gets worse once you remember that project instructions are only one part of the model's working context. The model may also be seeing:

- system instructions
- tool definitions
- MCP server instructions
- conversation history
- retrieved files
- test output
- generated plans

So I like treating project instructions as a budgeted resource.

**My current recommendation: keep the active tree of project instructions around 85 to 100 instructions.**

That is not a law of physics. It is a practical working range.

The important part is the phrase **active tree**. I'm referring to the set of instruction files the agent actually reads for a task. It is not every instruction file in the repo.

For example, if you're working on a UI feature, an active instruction tree like this might be fine:

```text
AGENTS.md: 30 instructions
instructions/frontend.md: 35 instructions
instructions/testing.md: 20 instructions

Total active instruction budget: 85
```

But this, to me, smells:

```text
AGENTS.md: 80 instructions
CLAUDE.md: 70 instructions
instructions/frontend.md: 60 instructions
instructions/testing.md: 40 instructions

Total active instruction budget: 250
```

At that point, you probably do not have a better-oriented agent, you have an overstuffed working context.

## Route Instructions By Workflow

A good scaffold should make routing obvious:

```text
For UI work, read instructions/frontend.md.
For API work, read instructions/backend.md.
For schema changes, read instructions/database.md.
For auth, secrets, or tenant boundaries, read instructions/security.md.
For release work, read instructions/release.md.
```

That keeps the top-level file small and lets each specialized file focus on one kind of work.

**The top-level file should route. The workflow files should instruct.**

## Prefer Affirmative Instructions

Lean towards positive language in instruction files, this is purely a personal preference so try it and see if it works for you.

Tell the agent what good work looks like.

This example is meh...:

```text
Do not introduce a new state management library.
Do not skip tests.
Do not create duplicate form components.
```

The one is better:

```text
Use the existing state management pattern unless the task explicitly calls for a new one.
Run the relevant test and lint commands before handing back changes.
Reuse form components from src/components/forms before creating new ones.
```

Same constraint, better posture.
This is not about being nice to the model, it is about reducing ambiguity. Affirmative instructions usually name the desired behavior more directly.

## Ask The Agent To Audit The Budget

You can also ask the agent to analyze the instruction budget for you.

Here is a prompt I would use:

```text
Goal: Review this repository's agent instruction scaffold starting with AGENTS.md or CLAUDE.md and following linked instructions files, then output a report showing number of instructions across files for each coherent workflow they encode.

Find all project instruction files that an agent is likely to read, including AGENTS.md, CLAUDE.md, nested AGENTS.md files, and linked instruction files.

Identify the main topics or workflows.

Then analyze the active instruction trees across files for each workflows, for example:
- frontend change
- backend/API change
- database/schema change
- security-sensitive change
- release/deployment change

For each active tree:
- List the files the agent would read.
- Count the total actionable instructions across that tree.
- Compare the total against an 85-100 instruction budget.
- Flag duplicate, conflicting, vague, or low-value instructions and recommend what to merge, delete, split, or route differently.
- Identify which instructions are global and which should move into a task-specific file.

Prefer preserving high-signal project knowledge over making the files shorter for its own sake.
```

The per-file count is useful, but the active-tree count is the real signal.

A repo can have 400 total instructions and still be healthy if each workflow only activates 85 useful ones.

A repo can also have 120 total instructions and still be a mess if every task forces the agent to read all of them.

## The TLDR;

The goal is to orient the agent and stop re-teaching the same project constraints every time we open a new task.

Start small:

1. Write the front door.
2. Split deeper instructions by workflow.
3. Count the active instruction tree.
4. Keep the budget tight.

If the agent needs everything to do anything, the project is not agent-ready yet.

## Further Reading

- `AGENTS.md` convention: <https://agents.md/>
- Claude Code memory and `CLAUDE.md`: <https://docs.claude.com/en/docs/claude-code/memory>
- Claude context editing: <https://docs.claude.com/en/docs/build-with-claude/context-editing>
- Dexter Horthy on instruction budget and agent workflows: <https://www.heavybit.com/library/article/whats-missing-to-make-ai-agents-mainstream>
