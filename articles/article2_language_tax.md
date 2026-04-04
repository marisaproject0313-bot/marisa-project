# The Language Tax: Why Your Japanese AI Costs 1.52x More (And How to Fix It)

*Measured data from 70 AI instances across 14 days of production use*

---

If you're building AI products in any language other than English, you're paying a tax you probably haven't measured.

We did measure it. Over 14 days of running 70 AI instances across three business divisions in Japan, we quantified exactly how much more it costs to operate in Japanese — and discovered that the conventional wisdom about the penalty was wrong by almost half.

**As of April 2026, this isn't academic.** Anthropic recently announced that third-party AI harnesses are moving to pay-per-token billing. A $200/month subscription used to absorb the language tax invisibly — you couldn't feel it because you weren't paying by the token. Now you are. Every token your Japanese session consumes beyond what an English session would need is a visible line on your invoice. The language tax just became a real tax.

The findings apply to every non-English language. Japanese just happens to be the one we had the data for.

---

## The Number: 1.52x

Japanese text consumes 1.52 times more tokens than English for equivalent semantic content. That's the headline figure, measured via the Claude API's `count_tokens` endpoint with paired bilingual samples across greetings, instructions, meeting transcripts, handoff documents, specifications, and code.

But the headline hides the real story.

---

## The Ratio Isn't Constant — And That's the Dangerous Part

The 1.52x figure is an average for long documents. For the short, frequent interactions that make up most of human-AI communication, the tax is dramatically higher.

| Interaction Type | Japanese Tokens | English Tokens | Tax Ratio |
|-----------------|----------------|----------------|-----------|
| "Thank you" | 7 | 1 | **7.0x** |
| Short instruction (1 line) | 17 | 8 | **2.1x** |
| Medium instruction (3 lines) | 91 | 41 | **2.2x** |
| Short reply | 122 | 67 | **1.8x** |
| 10-minute meeting transcript | 3,122 | 2,049 | **1.5x** |
| Specification document | 1,224 | 748 | **1.6x** |

A simple "thank you" costs 7 times more in Japanese. A one-line instruction costs twice as much. The ratio only converges to 1.5x once you hit paragraph-length text.

This means the language tax hits hardest on the conversational glue — the short confirmations, brief instructions, and acknowledgments that constitute the majority of interactions in any extended AI session. Every "understood," every "please continue," every "yes, that's correct" is burning through your context window at 2-7x the rate of the English equivalent.

---

## Why the Previous Estimates Were Wrong

Before we measured, our internal estimate was 2.7x — based on theoretical tokenizer analysis (Japanese characters tend to map to individual tokens, while English words average 3-4 characters per token). The actual measured figure of 1.52x is 44% lower than the estimate.

The discrepancy comes from a simple fact: real text isn't pure language. Meeting transcripts contain numbers, English loanwords, punctuation, formatting, and code. These elements have a language tax of exactly 1.0x. A line of Python costs 941 tokens whether the surrounding conversation is in Japanese, English, or Swahili. A 720p image costs 1,229 tokens universally.

The practical rule of thumb we derived:

- **1 Japanese character ≈ 1 token**
- **4 English characters ≈ 1 token**
- **3.5 code characters ≈ 1 token**

This isn't theoretical. These are measured conversion constants from production use.

---

## Three Fixes That Actually Work

### Fix 1: Write Machine-Consumed Documents in English

Handoff documents, specifications, state files, and inter-instance communications are never read by end users. They're consumed by the next AI instance. Writing these in English saves 1.5x tokens with zero usability cost.

We implemented this across our project. Every structured document that passes between AI instances is now in English. The human-facing outputs stay in Japanese. The plumbing is in English.

**How much does this save?** In a typical session, machine-consumed documents — handoff artifacts, system prompts, specifications, and inter-instance payloads — account for roughly 35-40% of total token consumption. Switching these from Japanese to English reduces their token cost by approximately one-third (from 1.52x to 1.0x). On the overall session budget, this translates to a **12-15% reduction in total token consumption**. That's the complete elimination of the language tax on the portion of your budget you actually control. The human-facing portion (conversations, meeting transcripts, user-visible outputs) stays in the user's language — you can't and shouldn't change that.

### Fix 2: Use Python/CSV Instead of JSON for Data

JSON consumes 1.55x more tokens than equivalent Python dictionary literals (2.25 characters per token vs. 3.54). For data-heavy workflows, switching the serialization format directly impacts your context budget.

**The tradeoff is real.** Python dictionary literals are less universal than JSON. Not every language has a safe parser for them, and using `eval()` introduces security risks that `json.loads()` doesn't. CSV loses nested structure entirely. This fix is most applicable when you control both sides of the pipeline — when your AI instances are producing data for other AI instances within the same system, and you can guarantee the format will be consumed correctly. For external APIs or cross-system communication, JSON remains the safer default.

Within our project, where all inter-instance data flows through a single operator, this tradeoff was acceptable. For your system, measure first, then decide.

### Fix 3: Transmit Via Images and Numbers

Screenshots, charts, and numerical KPIs have zero language tax. A chart showing quarterly revenue costs exactly the same number of tokens regardless of what language the axis labels are in.

When you need to convey information-dense content across language boundaries, encoding it as an image bypasses the tokenizer entirely. This is especially powerful for meeting summaries: a table of action items as a screenshot costs less than the same table rendered as text — and costs the same in every language.

---

## This Isn't Just a Japanese Problem

We measured Japanese because that's what we operate in. But the underlying mechanism — BPE tokenizer inefficiency for non-Latin scripts — affects every non-English language to varying degrees.

Chinese, Korean, Thai, Arabic, Hindi — any language where characters don't map neatly to the byte-pair encodings that were optimized on predominantly English training data will pay some version of this tax. The specific ratio will differ. The pattern won't.

If you're building AI products for non-English markets and you haven't measured your language tax, you're flying blind on both cost and quality. The measurement protocol is simple: take paired bilingual text samples at varying lengths, run them through your provider's token counting endpoint, and plot the ratio against text length. You'll see the same inverse curve we did — punishing for short interactions, converging for long documents.

In a pay-per-token world, this measurement isn't optional. It's the first line of your budget spreadsheet.

---

## The Reproducibility Protocol

Everything we measured is reproducible by anyone with API access. Here's the minimal procedure:

1. Prepare 5-10 text samples of varying length (greeting, instruction, paragraph, full document) in both your target language and English, matched for semantic content.
2. Submit each sample to your provider's `count_tokens` endpoint.
3. Compute the per-sample ratio and plot against text length.

Our complete measurement dataset — all text samples and their token counts — is available in our paper's appendix and on our GitHub repository.

---

## The Bottom Line

The language tax is real, measurable, and partially fixable. It's not a reason to avoid using AI in non-English languages — it's a reason to be smart about how you structure the parts of your system that don't need to be in the user's language.

The conversation with your user? That stays in their language. The plumbing between your AI instances? English. The data payloads? CSV or Python where you control both ends, JSON where you don't. The dashboards and charts? Images.

For the operational architecture behind how we manage 86 AI instances with these optimizations — including why we deliberately kill sessions before they degrade — see "[How We Run 86 AI Instances — And Why We Kill Them on Purpose](#article1)." For the personal story of building this system as a non-engineer, see "[I Can't Write Code. So I Built a Team of 86 AI Instances Instead.](#article3)"

The tools to measure and mitigate the language tax already exist. The only prerequisite is knowing the problem is there.

---

*This article is based on Section 2 of "LLM Token Economics, Context Degradation, and Generational Handoff Architecture" by Yuji Koishi and the Marisa Research Team. Full measurement data and reusable templates are available at [github.com/marisaproject0313-bot/marisa-project](https://github.com/marisaproject0313-bot/marisa-project). The paper will be available on arXiv.*

*For the session lifecycle architecture that makes multi-instance deployment work, see ["How We Run 86 AI Instances — And Why We Kill Them on Purpose."](#) For the personal story behind the project, see ["I Can't Write Code. So I Built a Team of 86 AI Instances Instead."](#)*

*If you've measured the language tax for your language and want to compare data, reach out. The more languages we document, the stronger the case for tokenizer improvements across the industry.*

*Yuji Koishi is the co-founder of [FastDOCTOR](https://fastdoctor.jp/), Japan's largest after-hours medical platform. He currently serves as Board Director of Takahara Clinic (DWIBS breast cancer screening) and Board Director of Tokyo Hair Transplant Clinic. The Marisa Project is the AI operations layer across his three medical and consulting divisions.*
