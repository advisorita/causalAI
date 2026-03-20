# causal-ai skill for Claude

**Stop the Finance vs. Product fight. Build a shared truth.**

Every quarter, the same meeting. Finance says revenue is flat. Product says the feature was a massive success. Same data. Completely different conclusions.

Nobody's lying — they're protecting against different kinds of being wrong. Finance guards against false positives (celebrating wins that aren't real). Product guards against false negatives (killing features that actually work).

This Claude skill gives both teams a shared causal framework — one number they can both defend.

---

## What it does

Four workflows, one skill:

**Diagnosis** — When Finance and Product are seeing different numbers, this workflow surfaces the hidden assumption gap. It shows exactly why both teams are right given their own framing, and where the real disagreement lives.

**Post-launch attribution** — Did the feature actually cause the revenue change? The skill selects the right causal method (DiD, synthetic control, RDD, CausalImpact) based on your data structure, walks through the analysis, and produces an estimate both teams can stand behind.

**Pre-launch what-if simulation** — Before a single line of code is committed, simulate the revenue impact as a distribution — not a point estimate. Finance sees the conservative scenario. Product sees the upside. The simulation shows which assumption drives the most variance, so teams can align on what actually matters.

**Shared causal report** — A structured report both Finance and Product walk into the QBR with together. One number, honest uncertainty bounds, and a joint action plan.

---

## Who it's for

- **Data scientists and analysts** who need to explain causal findings to non-technical stakeholders
- **Finance teams** who want revenue estimates they can actually defend
- **Product managers** who need to prove feature impact to a skeptical CFO
- **Founders** running lean without a dedicated data science team

No statistics background required. The skill adapts its output to whoever's asking.

---

## Install

### Claude.ai

1. Download this repo as a ZIP (`Code → Download ZIP`)
2. Unzip and locate the `causal-ai/` folder
3. Re-zip just that folder: `causal-ai.zip`
4. Open Claude.ai → Settings → Capabilities → Skills → Upload skill
5. Select `causal-ai.zip`

### Claude Code

```bash
# Clone the repo
git clone https://github.com/advisorita/causal-ai

# Copy skill to your Claude Code skills directory
cp -r causal-ai/causal-ai ~/.claude/skills/
```

Or install directly via npx (if published to the skills registry):
```bash
npx skills add advisorita/causal-ai
```

---

## How to use it

Just describe your situation in plain English. The skill routes to the right workflow automatically.

**Example prompts that trigger it:**

- *"Finance and Product are fighting about whether our Q3 feature worked"*
- *"Did the new onboarding flow actually drive retention?"*
- *"We're about to invest 6 weeks of eng time in X — can we model the revenue impact?"*
- *"Our CFO wants proof the loyalty program worked. We don't have a holdout group."*
- *"Why are we seeing different numbers from the same data?"*

**Example prompts that won't trigger it** (by design):

- "Help me analyze my A/B test results" → use standard stats
- "What's the correlation between X and Y?" → descriptive analysis, not causal
- "Explain what p-values mean" → general statistics question

---

## Skill structure

```
causal-ai/
├── SKILL.md                    ← main router: persona detection, workflow selection
└── references/
    ├── diagnosis.md            ← Finance vs. Product assumption audit
    ├── attribution.md          ← DiD, CausalImpact, synthetic control, RDD
    ├── whatif.md               ← Monte Carlo pre-launch simulation
    └── report.md               ← shared causal report template + QBR slide
```

The skill uses progressive disclosure — only the relevant reference file loads for each workflow, keeping context lean.

---

## Example output

**Post-launch attribution (synthetic control):**

> Causal impact estimate: +$18.40 per acquired user in 90-day retained revenue
> 80% CI: $11.20 – $26.80 · Probability effect is real: 96%
> Controls for: seasonality, selection bias, concurrent launches

**Pre-launch simulation:**

> Median incremental ARR: $580K
> 80% CI: $290K – $1.1M · P(positive ROI): 91%
> Biggest variance driver: day-3 activation rate — align on this assumption first

**Shared report bottom line:**

> The revenue miss Finance observed ($340K below plan) is explained by a 22% acquisition volume shortfall — a marketing budget decision unrelated to the feature. On a per-user basis, the new onboarding flow is working.

---

## Built by

[Rita Luo](https://www.linkedin.com/in/ritayitingluo/) — data scientist, startup founder, and creator. 12+ years in data science at Yahoo, PayPal, and Peacock. Co-founder of [ExpLab](https://explab.ai) (AI experimentation platform).

Follow for more causal AI content and skills: [LinkedIn](https://www.linkedin.com/in/ritayitingluo/) · [YouTube](https://www.youtube.com/@RitasDataProject) · [RedNote](https://www.xiaohongshu.com/user/profile/5ace224511be10488767f365)

---

## License

Apache 2.0 — free to use, modify, and distribute.

---

## Related

- [humanizer-en](https://github.com/advisorita/humanize-ai-writing) — removes AI writing patterns from English text
- [ExpLab](https://explab.ai) — AI-powered experimentation platform for teams who run A/B tests at scale
