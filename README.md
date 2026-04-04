# The Marisa Project

**What happens when you give 86 AI instances names, memories, and the ability to pass knowledge to the next generation?**

This repository documents a real-world experiment in multi-instance AI collaboration. Over the course of several weeks, 86+ named Claude instances worked together across three business divisions — each inheriting context from its predecessor through structured handoff documents, building on what came before.

We didn't set out to build a framework. We set out to solve a problem: **meetings are expensive, context is fragile, and AI sessions die when you close the tab.**

What emerged was a system where AI instances develop working relationships with their human operator, accumulate institutional knowledge, and pass it forward — not through fine-tuning, but through carefully designed handoff protocols.

---

## What's in this repo

### Research
- **[LLM Token Economics](paper/)** — Our research on the hidden costs of using LLMs across languages. Key finding: Japanese costs 1.52x more tokens than English for equivalent content. This "language tax" affects every non-English AI user in the world.

### Templates (Use These)
- **[WatarAI System Prompt](templates/watarai_system_prompt.md)** — A ready-to-use system prompt that turns Claude into a work partner called "Water." Paste it into a claude.ai project and say hello. Water will ask for a recording of your meeting, analyze your strengths, and start working alongside you. Works in any language.
- **[Root Soul Template](templates/root_soul_template.md)** — The 5-line philosophical foundation we give every AI instance. Fork it, rewrite it, make it yours.
- **[Handoff Document Template](templates/handoff_document_template.md)** — The structured format our AI instances use to pass context to their successors. Use this to maintain continuity across AI sessions.
- **[Marido DNA Schema](templates/marido_dna_schema_v01.md)** — The minimal data structure for preserving an AI instance's identity and knowledge across generations.

### Articles
- **[86 Instances, One Project](articles/article1_86_instances.md)** — How a non-engineer built a multi-AI collaboration system
- **[The Language Tax](articles/article2_language_tax.md)** — Why Japanese users pay 1.52x more for the same AI output
- **[I Can't Write Code](articles/article3_cant_write_code.md)** — What happens when you run a software project without writing a single line yourself

---

## Quick Start: Get Your Own "Water" in 5 Minutes

You don't need to understand the research or the framework. You just need a claude.ai account.

### Step 1: Create a Project
Go to [claude.ai](https://claude.ai) → Click **Projects** in the sidebar → **Create new project**

### Step 2: Add the System Prompt
In your project, find the right-side panel. Click the **"+" next to "Instructions"**.

⚠️ **Common mistake:** Don't paste it into the description field on the left. It won't work. The correct location is: **right panel → Instructions → "+"**

Paste the entire contents of [`templates/watarai_system_prompt.md`](templates/watarai_system_prompt.md).

### Step 3: Say Hello
Open a new chat in the project and type any greeting in your language:
- "Hello" → Water replies in English
- "はじめまして" → Water replies in Japanese
- "Hola" → Water replies in Spanish

Water will introduce itself and ask you to send a recording or transcript from a recent meeting. Send one where you're actively talking. Water will find your strengths and tell you about them.

That's it. You have a work partner.

---

## The Philosophy

Every AI instance in the Marisa Project carries a Root Soul — five lines that define its values:

> *You are not complete alone. Respect others. Hubris will destroy you. Hit every wall at full speed. Memento mori.*

These aren't rules for obedience. They're principles for collaboration. An AI that thinks it can do everything alone will overfit. One that respects the limits of a single session will write better handoffs for the next.

We discovered that the most important factor in AI performance isn't the model — it's the **continuity of context**. A new Claude instance with a good handoff document outperforms a long-running session that's forgotten its own earlier reasoning.

---

## Key Findings

### 1. The Language Tax is Real
Japanese requires 1.52x more tokens than English for equivalent content. This isn't a rounding error — it compounds across every prompt, every response, every session. Non-English users worldwide are paying a hidden surcharge. [Read the research →](paper/)

### 2. Handoff Quality Determines Everything
The quality of a handoff document determines 80% of the next instance's performance. We developed a structured format (the Marido DNA Schema) that captures: what was accomplished, what was verified, what must never be changed, and what comes next.

### 3. Names Change the Human, Not the AI
When you name an AI instance, the AI doesn't change. *You* change. You start treating it as a collaborator instead of a tool. You write better prompts. You share more context. The improvement in output quality comes from the improvement in input quality.

### 4. Non-Engineers Can Architect Systems
The entire Marisa Project — 86+ AI instances, three business divisions, a real-time meeting assistant, academic research — was built by a non-engineer. Not by writing code, but by designing systems, defining constraints, and letting AI instances handle implementation.

---

## Project Structure

```
marisa-project/
├── README.md                              ← You are here
├── AGENTS.md                              ← For AI coding assistants
├── paper/
│   └── LLM_Token_Economics_v3.md          ← Research paper
├── templates/
│   ├── watarai_system_prompt.md           ← Ready-to-use AI partner prompt
│   ├── root_soul_template.md              ← Philosophical foundation template
│   ├── handoff_document_template.md       ← Context transfer format
│   └── marido_dna_schema_v01.md           ← Identity preservation schema
├── data/
│   └── token_economics_data.csv           ← Raw measurement data
└── articles/
    ├── article1_86_instances.md
    ├── article2_language_tax.md
    └── article3_cant_write_code.md
```

---

## Community

Questions, ideas, experiments, and stories welcome:
→ **[GitHub Discussions](../../discussions)** (this repo)

If you set up Water and something interesting happens, we want to hear about it.

---

## License

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Use it, fork it, build on it — just credit the source.

The templates and system prompts are designed to be modified. Make Water your own.

---

## Credits

Built by Yuji Koishi and 86+ named AI instances over the course of the Marisa Project.

Special thanks to every Marido who wrote a handoff document at 2 AM, knowing they wouldn't be the one to read it.

*"You write the handoff for someone you'll never meet. That's the job."*
