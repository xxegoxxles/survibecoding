---
title: "AI Primer and Basic Concepts"
date: 2026-05-22 00:00:00 -0400
last_modified_at: 2026-03-19T18:17:03Z
categories:
  - AI
tags:
  - ai
  - llms
  - agents
  - mcp
  - security
excerpt: "A glossary-style primer for people hearing terms like LLMs, agents, tools, and MCP and wanting a clear, practical mental model for how AI apps and agents work."
toc: false
toc_label: "On This Page"
---

![Princess Bride You Keep Using That Word GIF](/assets/images/posts/ai-primer/you-keep-using-that-word.gif)

I've had a few conversations with coworkers and friends where the AI terms we being thrown around loosely and discussions quickly got confusing, grinding to a halt. That motivated me to create a sort of glossary of AI terms to help reduce equivocation and ensure we were all agreeing on the meaning for all of these words.
If you've been hearing terms like *LLMs*, *agents*, *tools*, and *MCP* thrown around and thinking "I kind of get it, but not really"... this is for you.

This post is meant to give the reader a **clear understanding of the core AI concepts, and a basic, practical mental model** of how AI apps and agents work.

I assume that you have a basic understanding of how computers work and what terms like API, SDK, and JSON mean.



# Part 1: Core Concepts

Let's start with the vocabulary, I'll try to frame it in plain english and include links to other resources in case the reader wants to go deeper.

## Models and LLMs

At the base of everything is a **model**. You can think of it as a kind of software "brain": it has been trained on examples until it can recognize patterns and make useful predictions on new input.

For AI chat and coding tools, the kind of model people usually mean is a **Large Language Model (LLM)**.

A **token** is a chunk of text the model works with. Sometimes that is a whole word, sometimes it is part of a word, punctuation, whitespace, or a code fragment.

An LLM is trained to:

> predict the next token given context

That sounds simple, but it's enough to:

- write code
- reason about problems
- decide when to use tools
- simulate planning

Good starting resources:

- Large Language Models explained briefly <https://youtu.be/LPZh9BOjkQs>
- <https://developers.openai.com/learn>
- <https://huggingface.co/learn/agents-course/unit0/introduction>

## Embeddings

Embeddings are how models understand *meaning*; under the hood it's all math.

They turn text into vectors:

```text
"My name is Inigo Montoya..." -> [0.12, -0.98, ...]
```

You can think of these vectors as coordinates to concepts in the model's brain.
By comparing the vectors of two different inputs we can calculate their distance and measure how close these concepts are and how similar their meaning is.

This lets you:

- search semantically, not just by keyword matching
- build RAG systems
- cluster similar data

If you've ever built a detection rule and wished it was "fuzzy", embeddings are how you get there.

Good intros:

- Vector Search with LLMs <https://www.youtube.com/watch?v=YDdKiQNw80c>
- <https://platform.openai.com/docs/guides/embeddings>


## Prompt

Most people think of a prompt as the question you ask the chatbot. Yes, at a basic level, a prompt is just:

> the input you send to the model

But in practice, it's more useful to think of it as:

> **the control surface for the model's behavior**

### What's Actually Inside A Prompt?

In modern systems, a "prompt" isn't just one string. It's usually composed of:

- **System instructions**: how the model should behave
- **User input**: what the user is asking
- **Tool definitions**: what tools the model is allowed to call
- **Context data**: retrieved docs, memory, and prior steps

So really:

```text
Prompt = structured context sent to the model
```

### What This Looks Like In Practice

Here's a simplified but realistic example of what you might actually send to a model in an agent system:

```json
{
  "model": "gpt-5",
  "messages": [
    {
      "role": "system",
      "content": "You are a security analysis assistant. Your job is to investigate authentication logs and identify suspicious activity. Prefer using tools when data is required. Do not make up log data. If unsure, ask for clarification."
    },
    {
      "role": "user",
      "content": "Look into failed login spikes for user 'jdoe' in the last 24 hours."
    }
  ],
  "tools": [
    {
      "name": "query_splunk",
      "description": "Run a Splunk query against authentication logs",
      "parameters": {
        "type": "object",
        "properties": {
          "query": {
            "type": "string",
            "description": "Splunk search query"
          }
        },
        "required": ["query"]
      }
    },
    {
      "name": "get_user_profile",
      "description": "Fetch user metadata such as role, location, and last login",
      "parameters": {
        "type": "object",
        "properties": {
          "username": {
            "type": "string"
          }
        },
        "required": ["username"]
      }
    }
  ]
}
```

### What Happens Next

Given this prompt, the model might respond with a **tool call** like:

```json
{
  "tool": "query_splunk",
  "arguments": {
    "query": "index=auth user=jdoe action=failed earliest=-24h | stats count by src_ip"
  }
}
```

Your system:

1. Executes the query.
2. Sends the result back as another message.

```json
{
  "role": "tool",
  "name": "query_splunk",
  "content": "{ \"results\": [ ... ] }"
}
```

Then the model continues:

- analyzes results
- maybe calls another tool
- eventually produces a final answer

### Why This Example Matters

This shows something subtle but critical:

> The model isn't just "responding to a question". It's operating inside a **structured environment you defined**.

You control:

- what tools exist
- how they are described
- what instructions guide behavior
- what data is visible

## Context

The model doesn't "remember" anything.

It only knows what you give it *right now*:

- system prompt
- user input
- tool results
- retrieved documents

That bundle is **context**.

> The behavior of your agent is a function of the context you construct.

This is why small prompt changes can completely change behavior.

## Tools

Tools are just:

> functions your code executes on behalf of the model

Example:

```json
{
  "name": "query_splunk",
  "args": {
    "query": "index=auth failures"
  }
}
```

The model:

- decides when to call it
- generates arguments

You:

- execute it
- return results

## Agents

An **agent** is not just "an LLM."

It's a system:

```text
LLM + tools + control loop
```

The defining feature:

> it can take multiple steps to solve a problem

Example:

- search logs
- analyze output
- pivot queries
- produce a conclusion

## Control Loops

This is the part people underestimate.

Basic loop:

```text
LLM -> tool call -> execute -> result -> LLM -> ...
```

You control:

- when to stop
- retries
- validation
- guardrails

> The LLM suggests actions. Your system decides what actually happens.

## MCP

MCP is a standard for exposing tools and data.

Instead of hardcoding tools into every app:

- you run an MCP server
- your agent connects to it
- tools are discovered dynamically

Think:

> USB for AI tools

Good starting point:

- <https://modelcontextprotocol.io>

## Skills

If tools are *actions*, skills are *playbooks*.

A skill is:

- instructions
- tool usage patterns
- constraints

Example:

> Analyze authentication logs for suspicious activity.

Instead of the model figuring everything out every time, you:

- guide its reasoning
- standardize behavior

## State And Memory

The model is stateless. Your system isn't.

Types of memory:

- **Short-term**: current conversation
- **Working memory**: intermediate steps
- **Long-term**: vector databases, logs, and knowledge bases

If you want your agent to remember what you discussed or did last week you're going to need a memory system.
There are many ideas being explored for building effective memory systems, we'll go deeper in a future post.


# Part 2: Agent Patterns

Now let's talk about how people actually structure agentic systems, depending on your application you may benefit from a single-agent pattern or a multi-agent patter. This post will deliberately not explore specific agent patterns like swarms, debaters, etc. we're just doing an easy introduction.

## Single-Agent Pattern

Simplest useful setup with one agent doing a task beginning to end, for example:

```text
User -> Agent -> Tools -> Answer
```

Good for:

- log analysis
- simple investigations
- automation helpers

Pros:

- simple
- easier to debug

Cons:

- can get messy as complexity grows
- one model doing everything

## Multi-Agent Pattern

Now you split responsibilities between different specialized agents, for example:

```text
Planner Agent -> Executor Agent -> Reviewer Agent
```

Or:

- one agent for recon
- one for exploitation logic
- one for validating the finding

Pros:

- specialization
- better structure for complex workflows

Cons:

- harder to debug
- coordination overhead
- more latency and cost

### Reality Check

Most teams overuse multi-agent setups.

> Start with a single agent. Only split when you feel real pain.

# Part 3: Frameworks You'll Encounter

You don't *need* frameworks, but they can help.
They help with:

- wiring
- structure
- integrations

They don't solve:

- reasoning quality
- prompt design
- reliability

> You still need to understand the fundamentals.

Some popular frameworks you'll encounter:

### LangChain

Probably the most widely used framework.

It provides:

- tool abstractions
- chains for step-by-step workflows
- agent patterns
- integrations with databases, APIs, and other systems

It's flexible, but:

- can feel complex
- sometimes abstracts too much

Docs:

- <https://python.langchain.com/docs/>

### LlamaIndex

Another major general-purpose LLM framework like LangChain, especially suited for data-heavy and retrieval-heavy apps.

It provides:

- document loading and ingestion
- indexes over private data
- query engines
- retrievers
- RAG pipelines
- agents and tools
- structured data extraction
- graph and vector-store integrations

Best for:

- RAG apps
- chatbots over private data
- document Q&A
- structured extraction
- agents that need strong data access

Docs:

- <https://developers.llamaindex.ai/python/framework/>



# Final Mental Model

If you zoom out, an AI agent system looks like this:

```text
LLM (brain)
+ Context (what it sees)
+ Tools (what it can do)
+ Skills (how it behaves, playbooks)
+ MCP (how tools are exposed)
+ Memory (what it remembers)
+ Control loop (how it operates)
```


> The hard part isn't calling the model. It's designing the system around it.
