# Data Files

Measured data from **"LLM Token Economics, Context Degradation, and Generational Handoff Architecture"** (Yuji Koishi and the Marisa Research Team, 2026).

All measurements were performed using the Claude API (`claude-sonnet-4-6`) `count_tokens` endpoint during March 2026.

## Files

### `token_economics_data.csv`
**The Language Tax** — Token cost of 14 common actions, measured in both Japanese and English. This is the core dataset behind the 1.52× finding.

Columns: `action`, `description`, `jp_tokens`, `en_tokens`, `jp_en_ratio`, `chars_per_token_jp`, `chars_per_token_en`, `category`, `confidence`

Key finding: The JP/EN ratio is **non-linear and length-dependent** — ranging from 7.0× for minimal greetings to 1.5× for long documents.

### `aging_curve_data.csv`
**Context Degradation** — Controlled measurements across 10 consumption levels (baseline to 90% of 200K-token window), 3 repetitions each, 4 question categories. Total: 120 API calls, cost $33.07.

Key finding: Numerical memory and instruction compliance remained flat. **Hallucination suppression** degraded at extreme usage — perfect through 70%, dropping to 1/3 at 90%.

### `conversion_constants.csv`
**Quick Reference** — Characters-per-token conversion rates for English, Japanese, Python, JSON, and images.

Rule of thumb: `1 JP char ≈ 1 token` · `4 EN chars ≈ 1 token` · `3.5 code chars ≈ 1 token`

## Reproduction

These measurements are fully reproducible. See Section 2.7 of the paper for the minimal reproduction protocol:

1. Prepare 5–10 text samples of varying length in both target language and English, matched for semantic content.
2. Submit each sample to the `count_tokens` endpoint.
3. Compute per-sample ratio and plot against text length to verify the inverse correlation.

## License

CC BY 4.0 — Use it, fork it, build on it. Just credit the source.
