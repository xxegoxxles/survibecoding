---
title: "Is There a Method to the Madness?"
date: 2026-05-31 00:00:00 -0400
categories:
  - AI
tags:
  - ai
  - agents
  - coding-agents
  - agentic-engineering
  - development-methodology
excerpt: "Coding agents can help you build faster, but only after you know what problem you are solving. Plan Mode is where the human and the agent align before choosing a development methodology."
toc: false
toc_label: "On This Page"
---

![Joker with a plan GIF](/assets/images/posts/choosing-a-coding-agent-methodology/joker-with-a-plan.gif)

When I first started building with coding agents, I mostly went straight into Act Mode.

I would tell the agent to just do things, the agent would start writing code, and only later would I realize that we were not aligned. Sometimes the agent understood the shape of the app differently than I did. Sometimes I had not fully understood the thing I was trying to build myself. Sometimes we were both moving quickly in a direction that felt productive until the implementation made the misunderstanding concrete.

That is the part I have been trying to get better at.

## The Hard Way

The evidence is sitting in my development folder.

For one project, I have versions named `arti`, `arti_v2`, `arti_v3`, `v4`, `v5`, `v6`, `v7`... it was going to be a chatbot, no wait, an agent, a virtual TPM, a report writer, an email drafter, a ticket monitor... it would run out of my laptop, no wait, a cloud microservice, a VPS?
Actually it was just garbage because I had changed the goals and requirements on the fly so many times. Finally `v8`, that's where I started getting output that matched what I actually wanted; the app got better as I got better at the work before the code.

With project Barragan, I discarded v1 and v2 for similar reasons; I had just vibed aimlessly hoping that the agent and I would somehow click and a perfect app would materialize. For v3, I spent a few weeks mostly planning, chatting with colleagues: clarifying the product, working through what the app should do, showing the agent examples, and setting it up to build the right thing instead of just building something.

With Condor, I spent days in Plan Mode before going into the build. So far there is only one project folder, and the pilot version worked surprisingly well. It mostly needed UI improvements. I do not think that happened because the agent magically got perfect. I think it happened because I was finally clearer before I asked it to act.

**The best harness and hypest methodology will not save vague intent but a mediocre harness can still produce useful results if the intent is clear.**

A manual chat loop, a team of agents, or a planner-generator-evaluator harness can all support the same underlying discipline: clarify intent, research reality, decide on a plan, implement, and verify.

There are a lot of emerging methodologies for development with coding agents. They have different acronyms, clubs, and secret handshakes: RPI, QRSPI, Spec-Driven Development, Test-Driven Agentic Development, etc.
Don't worry, I'm not here to tell you that one is the best and the other ones are wrong, that's hardly the point.
What I have learned the hard way is that all of them need to begin in the same way:

**Clarity and Alignment**

I'm referring to the human-agent alignment phase before any coding starts. This is the part where you, the human, get clear on the problem you want to solve and then talk with the agent until you both have the same mental model of the work. That sounds obvious now but I skipped it constantly: I would open the agent, vomit a prompt, and expect the agent to somehow read my mind and know what I wanted. Sometimes I got what I wanted, other times just large diffs, invented architecture, plausible-but-wrong assumptions, and a lot of tokens spent discovering that the first prompt had been underspecified.

An agent can help you build apps faster. It can research the codebase, produce options, write tests, implement boring plumbing, and catch mistakes. But it cannot absolve you of deciding what you are trying to build. **You should not outsource the thinking.**

## The Prerequisite Is Clarity

Before a harness or a methodology matters, you need a clear enough answer to:

```text
What problem am I solving?
Who is it for?
Where is it for?
What should it do, and what should it not do?
What examples show the shape of the outcome I want?
How will I know when v1 is done?
```

You do not need a perfect answer, you do need enough clarity to guide the agent away from generic completion.

Remember that the model was trained on text and code from the Internet written by humans and a lot of it was mediocre. The agent is very good at filling gaps with that mediocrity... that is useful when the gaps are tactical but it is dangerous when the gaps are conceptual.

If you are fuzzy about the product, the model will often produce something that looks like a product. If you are fuzzy about the architecture, it will often produce something that looks like architecture. If you are fuzzy about success criteria, it will often produce something that looks finished.

The problem is not that the model is lazy, the problem is that plausibility is cheap. The human has to provide taste, intent, constraints, and judgment.

## Plan Mode as Alignment Mode

Plan Mode is where you build a shared understanding with the agent about your intent.

This can be as small as a short conversation:

```text
I want to add GitHub login to this app.
Research the auth flow first.
Tell me what files matter, what patterns already exist, and what questions we need to answer before implementation.
Do not write code yet.
```

Or it can be a long collaboration:

```text
Act like a product manager interviewing me about this idea.
Ask one focused question at a time.
Keep going until the product goal, user workflow, non-goals, success criteria, and first implementation slice are clear.
Then summarize what you believe we are building and ask me to correct it.
```

That second version sounds slow, but "slow is smooth and smooth is fast"... Spending an hour getting aligned can be cheaper than spending ten agent runs unwinding the wrong abstraction. The important thing is that Plan Mode should not be treated as bureaucratic ceremony. It is a high-leverage debugging session for the future implementation... you are debugging ambiguity before it turns into code.

## Bring Examples

Examples are one of the strongest forms of context you can give a coding agent.

- If you want to automate report generation, show the agent an example report that already has the structure, tone, sections, and level of detail you want.
- If you want infrastructure automation, show it example Terraform or Ansible files that represent the resources you expect. If the infrastructure already exists, let the agent inspect both the plan and the deployed reality, for example by reading the IaC and using SSH or cloud APIs to understand what was actually delivered.
- If you want a UI, show the agent screenshots, example apps, or links to interfaces with the shape you want.

Examples help because they compress taste and intent.

Instead of saying:

```text
Make the dashboard feel professional.
```

Say:

```text
Help me make a new dashboard, here's an example of a dashboard I like for its information density <link to example>.
My dashboard should feel like a dense operations console.
Use compact tables, clear filters, quiet colors, and predictable navigation.
But do not use oversized hero cards or decorative gradients.
```

For coding tasks, examples can be even more concrete:

```text
Review the existing pattern in src/features/billing.
The new workflow should behave like the invoice retry flow, but for failed webhook deliveries.
Use that as the reference shape unless research shows a better local pattern.
```

Good examples reduce the agent's degrees of freedom but that does not mean the model becomes uncreative, it means its creativity is pointed at the right target.

## What Comes After Plan Mode?

Once you and the agent are aligned, the next step depends on what kind of work you are doing. This is where methodologies become useful; they are not dogma, they are control systems for different kinds of risk.

I'll introduce some popular ones but this is not meant to be a complete survey.

| Methodology | Description | Strengths | References |
|---|---|---|---|
| Ask -> Code | The agent first investigates or explains, then switches into implementation. | Controls mutation risk on small changes. Fast, low ceremony, and useful when you mostly need a clean boundary between discovery and editing. | [Cursor Plan Mode](https://cursor.com/docs/agent/plan-mode)<br>[GitHub Copilot modes](https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/) |
| RPI: Research -> Plan -> Implement | The agent researches the codebase, writes a plan, and only then implements after human review. | Controls codebase misunderstanding and review overload. Useful when the agent needs to learn existing files, patterns, tests, and constraints before making a diff. | [HumanLayer RPI repo](https://github.com/humanlayer/advanced-context-engineering-for-coding-agents/blob/main/ace-fca.md)<br>[RPI anti-vibe-coding writeup](https://htek.dev/articles/research-plan-implement-anti-vibe-coding-workflow) |
| QRSPI | Evolution of RPI: Questions -> Research -> Design -> Structure -> Plan -> Worktree -> Implement. | Controls hidden assumptions and architecture risk. Better for ambiguous, multi-phase, or architecture-sensitive work where uncertainty needs to surface before planning. | [HumanLayer on QRSPI](https://www.heavybit.com/library/article/whats-missing-to-make-ai-agents-mainstream)<br>[Dexter Horthy talk](https://www.youtube.com/watch?v=YwZR6tc7qYg) |
| Spec-Driven Development | Puts a durable specification at the center: spec, plan, tasks, implementation, validation. | Controls requirement drift. Useful when product intent, acceptance criteria, and decisions need to survive across sessions, people, or agents. | [GitHub Spec Kit](https://github.github.io/spec-kit/)<br>[Kiro on Spec-Driven Development](https://kiro.dev/blog/kiro-and-the-future-of-software-development/)<br>[Keeborg SDD guide](https://www.keeborg.com/guides/spec-driven-development) |


You do not need to pick one forever, you pick the one that works for you and controls the actual risk in front of you... or don't pick any of them and invent your own.

The point is: the first phase is essential regardless of the methodology.

Plan Mode is where I stop asking the agent to read my mind. It is where I slow down long enough to turn a vibe into **clarity and alignment**. Once that alignment exists, the methodology is just the next tool. Before that, it is mostly decoration.
