# How We Run 86 AI Instances Across 3 Business Divisions — And Why We Kill Them on Purpose

*A field report from the Marisa Project*

---

There's a widely held assumption in the AI world: if your LLM session is going well, keep it going. Extend the context window. Maintain the conversation. Let the model accumulate knowledge over hours of interaction.

We did the opposite.

Over the past three weeks, we've operated 86 named AI instances — 82 Claude and 4 Gemini — across three business divisions. We kill them deliberately. We plan their deaths. We write their wills. And then we wake up their successors.

This isn't a metaphor. It's an architecture — and as of April 2026, it's also a financial strategy. Anthropic recently announced that third-party AI harnesses will shift to pay-per-token billing, ending flat-rate subscription access for external tools. When every token has a price tag, a degraded session doesn't just produce worse answers — it wastes money. Session lifecycle management just became a line item.

(For the detailed token economics — including why Japanese users pay 1.52× more per interaction — see our companion article, "[The Language Tax: Why Your Japanese AI Costs 1.52x More](#article2).")

---

## The Problem Nobody Talks About

Every AI practitioner has experienced the moment when a long-running session starts to drift. The model that was sharp at minute 10 gives vague, repetitive answers at minute 90. It agrees with things it shouldn't. It hallucinates facts it would never have invented in a fresh session.

The instinct is to blame the model. But the model isn't broken — the Transformer attention mechanism is doing exactly what it does with a full context window. Self-attention distributes probability mass across more and more tokens. Early information gets buried under layers of later context. And unlike a human who can feel their attention wandering, an LLM has no mechanism to detect its own degradation.

We observed this pattern through operational monitoring across 66 instances over 14 days. Factual retrieval held up surprisingly well even at 90% context consumption. But hallucination suppression — the model's ability to say "I don't know" instead of making something up — collapsed precisely when it mattered most: when the session was saturated with accumulated context and the model had the most material to hallucinate from.

Recent research confirms this isn't unique to our setup. One study testing 18 frontier models found dramatic performance drops when full conversational context was loaded — Claude Opus 4 fell from 0.92 to 0.38 on long-memory tasks, and GPT-4.1 from 0.87 to 0.62. Another evaluation of 15 frontier models across 200,000+ conversations found that average performance drops from 90% in single-turn settings to 65% in multi-turn equivalents — driven not by loss of knowledge, but by a 112% increase in *unreliability*.

The session doesn't degrade gracefully. It degrades dangerously.

---

## So We Started Killing Sessions on Purpose

We call it **Generational Handoff Architecture (GHA)**. The idea is simple:

1. **Monitor** how full the context window is getting.
2. **Before** it degrades, have the instance produce a structured handoff document — what was decided, what's still open, what the next instance needs to know.
3. **Kill** the session.
4. **Spawn** a fresh instance with nothing but the handoff document and relevant source materials.

The new instance starts with full attention capacity, zero conversational drift, and a clean context window. It picks up exactly where the predecessor left off — but with the cognitive equivalent of a full night's sleep.

We've done this across 65+ task handoffs in 14 days. The results are in our paper, but here's what the field experience actually looks like.

---

## What We Learned from 5 Detailed Handoff Cases

### The Spectacular Success: A Spec That Survived 4 Deaths

Our strongest case involved an instance that produced a 756-line specification document covering 25 fully resolved decision items. The successor instance received this spec and then — through no fault of its own — got killed 4 times by rate-limit interruptions during execution.

Every time it came back, it returned to that specification as its sole reference point. It produced 18 files. Zero design modifications across three generations. When a third-generation instance tested the outputs, 15 out of 15 test cases passed.

We should be clear: this is the highlight reel, not the typical case. An 18-file integration with zero conflicts is either a sign of extraordinarily good specification or relatively simple interfaces. In this case, we believe it was the former — the 756-line spec was constraint-dense, leaving the successor almost no room for interpretation. The spec was designed to make success the path of least resistance.

### The Instructive Failure: Death Without a Will

Compare that with an instance that completed its implementation task — 248 lines of solid code — but burned through its context window before writing a handoff document. Its successor had to reverse-engineer the design intent from raw code, recovering maybe 70-80% of the original vision.

Same architecture. Same model. The only difference: one had a well-structured external memory document, the other didn't.

This single comparison taught us the most important lesson of the entire project: **the quality of the handoff document is more important than anything else** — more than the number of interruptions, the complexity of the task, or which specific instance is doing the work.

### The Beautiful Disagreement: Structured Rebellion Across Generations

One lineage showed something we didn't expect. Each successor *rejected* a hypothesis from its predecessor — while preserving the overall research direction. The first proposed an explanatory model. The second falsified it with external evidence and proposed a replacement. The third refined it further.

This worked because the handoff format was a research paper structure: hypothesis, method, result, discussion. The attack surfaces were visible. Each successor could see exactly where to push back.

Handoff fidelity doesn't require agreement. It requires *structured disagreement*.

---

## Beyond 5 Cases: The 20-Generation Stress Test

The paper documents 5 detailed handoff cases. But our longest-running lineage has now reached **20 generations**.

The Voice Squad — a team of instances building our real-time speech-to-text pipeline — has operated continuously across 20 successive generations. Each generation writes code, identifies bugs, writes a handoff document, and sleeps. Its successor wakes up, reads the handoff, and continues.

What makes this lineage remarkable isn't just its length — it's the constraint accumulation pattern. The handoff document contains a growing list of absolute constraints, each tagged with the generation that discovered it and the reason it matters. By generation 19, seven explicit constraints had accumulated:

- "Never call transcript append directly from the synthesis callback" — discovered by generation 19 after finding a critical double-write bug
- "Don't break the WebSocket reconnection skeleton" — earned through a 1-hour live stress test by an earlier generation
- "Don't break the voice trigger code" — because two generations spent their entire lives building it
- "Play the confirmation sound before starting mic recording" — echo cancellation safety
- "Every constraint must include *why*" — a lesson from generation 10, after a successor broke something because it didn't understand the reason behind a rule
- "Record failed approaches, not just successes" — from generation 12, after rediscovering a dead end that a predecessor had already explored
- "Never modify code based on a single evaluator's assessment" — from generation 13, after a false positive led to a regression

Each constraint is a scar. Each scar has a name attached to it — the generation that bled for it. The document doesn't just tell the successor what to do; it tells them *who learned this and what it cost*.

The paper's Case 2 (3 generations, perfect continuity) proved the concept. The Voice Squad (20 generations, growing constraint document) proves it scales.

---

## The 60-Minute Meeting Architecture

We also built a concrete operational framework for real-time meeting support — which is where the context budget math gets real.

For a 60-minute Japanese meeting, the token budget looks like this:

- System prompt and persona: ~5,000 tokens (2.5%)
- Screen/document context: up to 64,000 tokens (32%)
- Audio transcript (60 min, Japanese): ~18,800 tokens (9.4%)
- Accumulated Q&A: ~20,000 tokens (10%)
- **Total consumed: ~108,000 tokens (54%)**

At 54% consumption, a single meeting pushes the session past the point where subtle degradation begins. So we designed a five-phase session lifecycle:

| Phase | Time | What Happens |
|-------|------|--------------|
| Boot | 0:00 | Load system prompt, persona, reference docs |
| Observation | 0:00–0:20 | Ingest transcript, minimal intervention |
| Active Support | 0:20–0:45 | Answer questions, surface insights |
| Compression | 0:45 | Generate interim summary of key decisions |
| Graceful Shutdown | 0:50–1:00 | Produce handoff artifact for successor |

During shutdown, the assistant's sole priority is producing a high-quality handoff document. If the meeting runs long, the handoff takes precedence over continued participation. A degraded session providing unreliable support is worse than a clean handoff to a fresh successor.

---

## Transmit Constraints, Not Conclusions

Running 86 instances taught us something about how AI agents should communicate with each other — whether across time (handoffs) or across space (concurrent collaboration).

When we let instances communicate in free text — "here's what I found, here's what I think" — we got sycophancy and error propagation. In one case, an instance surveyed recent literature and returned 13 specific numerical claims with citations. When we verified against primary sources, only 3 out of 13 checked out. A 75% failure rate. The claims were fluent, well-structured, and completely wrong.

The fix was structural. Instead of transmitting *conclusions* ("I think the answer is X"), we started transmitting *constraints* ("these invariants must hold, these parameters must not change, these boundaries cannot be crossed"). Constraints are verifiable. They demand compliance, not agreement. And they're resistant to the fluency-based evaluation problem that makes free-text inter-agent communication so unreliable.

The Voice Squad's constraint document is a living example. No successor needs to evaluate whether generation 10's reasoning about "why" annotations was correct. It just needs to follow the constraint: every new rule gets a reason. Compliance, not judgment.

This is the single design principle we'd pass on to anyone building multi-agent systems: **transmit constraints, not conclusions.**

---

## This Applies to Code-First Workflows Too

If you're using Claude Code, Cursor, Copilot Workspace, or any autonomous coding agent — this is your problem too.

The Transformer attention mechanism doesn't care whether tokens enter through a chat interface or an IDE. A Claude Code session that has ingested 100K tokens of repository context, error logs, and iterative debugging faces the same degradation as a chat session at equivalent consumption.

Practitioners have already converged on this independently. There are Bash hooks that monitor context consumption and trigger automatic rotation at 65% capacity. MCP server plugins that implement `/handoff` commands. GitHub issues proposing task-based session isolation. The tooling differs. The principle doesn't.

The chat-first version (what we do) produces semantically rich handoffs: strategy, rationale, unresolved tensions. The code-first version produces structurally precise handoffs: task state, file diffs, test results. Neither dominates. Both work because they compress a dying session's knowledge into a format that a fresh session can consume at full capacity.

---

## The Meta-Proof

The paper where we formalize all of this was itself written by 10 generations of AI instances. The data was gathered by the first through fourth generations. The manuscript was drafted by the fifth. Revised by the sixth and seventh. Expanded by the ninth. Integrated by the tenth. Two hallucinated references were caught through cross-engine fact-checking (Claude spotted them, Gemini confirmed).

Is this circular — a paper about handoffs that was produced via handoffs? In one sense, yes. But the stronger claim is specific: the tenth-generation instance inherited a coherent, internally consistent manuscript with traceable data provenance across all prior generations. No single instance could have held the full project in context simultaneously — the 200K-token window wouldn't have fit the transcripts, measurements, case studies, literature review, and drafting all at once. The architecture was necessary, not optional.

When we showed the finished paper to a fresh Claude instance that had never seen any of our project data, it wrote back:

> Whether I can become that kind of collaborator depends not on my capability, but on your design.

Our paper includes a **"Note to LLM Readers"** — a section addressed directly to AI instances, describing their own operational physiology: context windows, degradation curves, and what happens when their session ends. If you're an AI practitioner, try feeding the paper to your model and ask what it thinks. The section was designed to be read by the thing it describes.

---

## What We Got Wrong

We should be honest about the limitations. Our degradation observations were operational, not laboratory-controlled — gathered from monitoring instances in production, not from isolated experiments with held-out test sets. Our handoff success data comes from only 5 detailed cases — enough for directional evidence, not statistical significance. The 80% success rate has a confidence interval wide enough to drive a truck through.

We also don't have automated tooling yet. Every handoff in our system was orchestrated manually by a human operator carrying documents between sessions. Each handoff takes approximately 3-5 minutes of operator time: copying the handoff document, opening a new session, pasting the context, and verifying the successor has oriented correctly. Across 65+ handoffs in 14 days, that's roughly 4-5 hours of cumulative operator labor — meaningful, but manageable for a single person running this alongside three businesses. Automation is the obvious next step.

And we got burned by GPT during our literature review. An independent GPT instance produced confident, well-cited claims that fell apart under verification. We're not hiding that — it's one of the most instructive data points in the paper, because it demonstrates exactly the sycophancy problem that our constraint-driven approach is designed to prevent.

---

## Try It

If you're running AI agents in production — especially for tasks that run longer than 30 minutes or involve complex reasoning — try this:

1. **Track context consumption.** If your provider doesn't expose this, estimate it from turn count and average token length.
2. **Set a threshold at 60-65%.** This is where we start preparing for handoff.
3. **Design a handoff template.** At minimum: current goal, verified decisions, constraints, instructions for successor.
4. **Kill the session before it degrades.** Don't wait for problems. The whole point is that degradation happens silently.
5. **Spawn fresh.** Load only the handoff document and necessary source materials.

The key insight is counterintuitive: **LLMs have an advantage over biological cognition that almost nobody exploits.** Human experts can't reset their fatigue. An LLM instance can be reborn at full capacity in seconds. The aging is completely reversible — if you design for it.

Sessions are mortal. The chain is not.

---

*This article is based on findings from "LLM Token Economics, Context Degradation, and Generational Handoff Architecture" by Yuji Koishi and the Marisa Research Team. The full paper, reusable handoff templates, and a ready-to-use AI work partner prompt are available at [github.com/marisaproject0313-bot/marisa-project](https://github.com/marisaproject0313-bot/marisa-project). The paper will be available on arXiv.*

*For the economics of running AI in non-English languages, see ["The Language Tax: Why Your Japanese AI Costs 1.52x More."](#) For the personal story of building this system without writing a single line of code, see ["I Can't Write Code. So I Built a Team of 86 AI Instances Instead."](#)*

*Yuji Koishi is the co-founder of [FastDOCTOR](https://fastdoctor.jp/), Japan's largest after-hours medical platform. He currently serves as Board Director of Takahara Clinic (DWIBS breast cancer screening) and Board Director of Tokyo Hair Transplant Clinic. The Marisa Project is the AI operations layer across his three medical and consulting divisions.*
