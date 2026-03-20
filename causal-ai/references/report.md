# Workflow 4: Shared Causal Report
## "One Number Both Finance and Product Sign Off On"

---

## Purpose

This workflow produces a stakeholder-ready artifact: a structured causal analysis that Finance and Product present *together*. The goal is not to declare a winner — it's to replace two competing narratives with one honest one.

---

## Report Structure

Produce a report with the following sections. Adapt length to the stakes — a QBR slide can be condensed; a board memo needs full rigor.

---

### SECTION 1: The Causal Question

> **One sentence.** What caused what, for whom, over what time period?

*Example:*
> "We estimate the causal effect of Feature X on monthly revenue per user, for users exposed between Sept 1–Nov 30, 2024, compared to a counterfactual world where Feature X was never shipped."

**Why this matters**: Finance and Product are often answering different questions. Agreeing on this sentence first closes 30% of the gap before any analysis begins.

---

### SECTION 2: What Each Team Saw (And Why They Were Both Right)

| | Finance | Product |
|---|---|---|
| **Metric** | Net MRR (all users) | Feature engagement (adopters only) |
| **Baseline** | Prior quarter | Non-adopters |
| **Finding** | Flat revenue | +34% engagement lift |
| **Main risk** | Seasonal confound | Selection bias |
| **Conclusion** | "Not proven" | "Massive success" |

> Both findings are accurate given each team's framing. The disagreement is about methodology, not honesty.

---

### SECTION 3: The Causal Analysis

**Method selected**: [DiD / CausalImpact / Synthetic Control / RDD — explain why in 1–2 sentences]

**Key assumption**: [State the critical identifying assumption in plain English]

**How we validated it**: [Parallel trends plot / placebo test / covariate balance — summarize in 2–3 sentences]

**The counterfactual**: [What would revenue have looked like without the feature? Show the graph.]

---

### SECTION 4: The Shared Number

Present the finding in a format both teams can stand behind:

```
┌─────────────────────────────────────────────────────────────┐
│  CAUSAL IMPACT ESTIMATE — Feature X                         │
│                                                             │
│  Incremental monthly revenue:   $2.4M                       │
│  80% Confidence Interval:       $1.2M — $3.8M              │
│  Annualized:                    $28.8M                      │
│  Probability effect is real:    94%                         │
│                                                             │
│  This estimate controls for:                                │
│  ✓ Seasonality  ✓ Concurrent launches  ✓ Selection bias     │
└─────────────────────────────────────────────────────────────┘
```

---

### SECTION 5: What the Number Means for Each Team

**For Finance:**
- The $2.4M estimate is net of seasonality — this is *incremental*, not gross
- The downside scenario ($1.2M) still justifies the investment
- Risk-adjusted ROI at 80% CI: [calculate based on feature cost]
- Recommended action: Include $2.0M (conservative) in revenue forecast

**For Product:**
- The effect is real with 94% confidence — this is not noise
- Effect grew over 6 weeks — consistent with organic adoption, not novelty spike
- Adoption rate was the biggest driver of variance — invest in onboarding to capture upside
- Recommended action: Prioritize the 3 onboarding improvements that could move adoption from 25% → 35%

---

### SECTION 6: What We Don't Know (Honest Caveats)

Always include this section. It builds trust with Finance and signals rigor to analysts.

- **Durability**: The 90-day window may not capture full long-term impact (or decay)
- **Generalization**: Effect was measured on [user segment] — may differ for [other segment]
- **Attribution**: [Any concurrent launches that couldn't be fully isolated]
- **Next validation point**: We recommend revisiting this analysis in [timeframe] with [additional data]

---

### SECTION 7: Recommended Next Steps

Both teams sign off on a shared action plan:

| Team | Action | Owner | By When |
|---|---|---|---|
| Product | A/B test the 3 onboarding improvements | PM | Q1 |
| Finance | Update revenue forecast using $2.0M conservative estimate | FP&A | Next QBR |
| Data | Re-run analysis at 6-month mark with full cohort | DS | March 15 |
| Both | Align on causal measurement approach for next feature | DS + Finance | Before next launch |

---

## Output Templates

### QBR Slide (condensed)
```
Feature X Impact — Q3 2024
Causal estimate: $2.4M incremental MRR (+/-$1.3M, 80% CI)
Method: Difference-in-Differences, controlling for seasonality and user characteristics
Confidence: 94% probability the effect is real
Key driver: Adoption rate (currently 25% — pathway to $3.8M if we reach 35%)
Next: A/B test onboarding improvements in Q1
```

### Board Memo (header)
```
TO: Board / Executive Team
FROM: Finance & Product (joint)
RE: Causal Analysis — Feature X Revenue Impact

BOTTOM LINE: Feature X drove an estimated $2.4M in incremental monthly revenue
(80% CI: $1.2M–$3.8M). This estimate uses causal inference methods to isolate
the feature's true contribution from seasonal and market effects. Finance and
Product jointly endorse this as the shared measure of success.
```

---

## Facilitation Guide: Running the Joint Review

If you're facilitating a Finance/Product alignment meeting, use this agenda:

1. **(5 min)** Agree on the causal question — one sentence, both teams sign off
2. **(10 min)** Each team presents their original finding — no debate yet
3. **(10 min)** DS presents the assumption audit (Diagnosis workflow)
4. **(15 min)** DS presents the causal analysis and the shared number
5. **(10 min)** Discuss caveats — both teams add what they're still uncertain about
6. **(10 min)** Agree on next steps

The output of this meeting is the shared report above — signed by both Finance and Product leads.
