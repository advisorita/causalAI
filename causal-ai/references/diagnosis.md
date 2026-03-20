[diagnosis.md](https://github.com/user-attachments/files/26150252/diagnosis.md)
# Workflow 1: Diagnosis
## "Why Are Finance and Product Seeing Different Numbers?"

---

## The Core Problem

Finance and Product are not looking at different data. They are protecting against different kinds of being wrong.

| Team | Fear | Bias | Metric preference |
|---|---|---|---|
| **Finance** | False Positive — celebrating a win that isn't real | Conservative | Incremental revenue, statistical significance, p-values |
| **Product** | False Negative — killing something that actually works | Optimistic | Engagement lift, feature adoption, directional trends |

Both are rational. Both are incomplete. **Causal AI gives them a shared framework that addresses both fears simultaneously.**

---

## Step 1: The Assumption Audit

When teams disagree, run through this checklist to find the hidden gap:

### A. Are they measuring the same outcome?
- Finance: total revenue, net revenue, incremental MRR
- Product: DAU, feature engagement, retention, NPS
- **Fix**: Agree on a single primary metric that both care about. Usually: incremental revenue attributable to the feature.

### B. Are they measuring the same population?
- Finance may look at all users
- Product may look at only feature adopters
- **Fix**: Clarify whether the claim is about "users who adopted the feature" (per-user impact) or "the whole product" (portfolio impact)

### C. Are they using the same time window?
- Finance may use fiscal quarter
- Product may use 30-day post-launch window
- **Fix**: Align on a time window that captures both the launch effect and any lag

### D. Are they controlling for the same baseline?
- Finance may compare to prior quarter (seasonal risk)
- Product may compare to non-adopters (selection risk)
- **Fix**: Use a causal method that explicitly constructs a counterfactual

### E. Are they accounting for the same confounders?
Common confounders that inflate Product's numbers:
- Power users adopted first (selection bias)
- Macro tailwinds in the same period
- Other features shipped simultaneously

Common confounders that deflate Finance's numbers:
- Cannibalization from legacy features (misattribution)
- Revenue lag — impact shows up next quarter
- Geographic rollout — not all users exposed yet

---

## Step 2: Reframe the Disagreement

Use this script to reframe the fight as a shared causal question:

> "Both teams are right about what they measured. The question is: what would revenue have looked like *if this feature had never shipped*? That's the counterfactual. Let's build it."

This moves the conversation from "who's right" to "what's the right question."

---

## Step 3: Diagnose the Metric Gap

Ask the user to share both teams' numbers. Then:

1. **Decompose the difference**: Which part of the gap is due to population, time window, baseline, and confounders?
2. **Estimate each contribution**: Rough order-of-magnitude is fine at this stage
3. **Identify the biggest driver**: Usually 1–2 factors explain 80% of the gap

```python
# Simple decomposition template
components = {
    "Population difference": ...,      # e.g., feature adopters vs. all users
    "Time window difference": ...,     # e.g., 30-day vs. full quarter
    "Baseline difference": ...,        # e.g., YoY vs. holdout comparison
    "Confounder not controlled": ...,  # e.g., seasonal lift, concurrent launch
}

total_gap = finance_number - product_number
print(f"Total gap to explain: {total_gap}")
for k, v in components.items():
    print(f"  {k}: {v} ({v/total_gap*100:.1f}% of gap)")
```

---

## Step 4: Recommend a Resolution Path

Based on the diagnosis, recommend one of:

| Root cause | Resolution |
|---|---|
| Different populations | Agree on a target population; re-run both analyses on the same group |
| Different time windows | Use event study (see attribution.md) to see effect over time |
| Selection bias (adopters ≠ random) | Use DiD or PSM to construct a comparable control group |
| Concurrent launches | Use synthetic control to isolate this feature's contribution |
| Revenue lag | Extend time window; look at cohort-level LTV not just immediate revenue |
| Cannibalization | Measure net portfolio impact, not just feature-level impact |

---

## Deliverable: Assumption Map

Produce a structured assumption map for the user:

```
ASSUMPTION MAP — [Feature Name] Revenue Impact Analysis
=========================================================

FINANCE's implicit assumptions:
  ✗ Compares Q3 vs Q2 (seasonal confound present)
  ✗ Counts all users, not just those exposed to feature
  ✗ No adjustment for concurrent campaign

PRODUCT's implicit assumptions:
  ✗ Compares adopters vs. non-adopters (selection bias)
  ✗ 30-day window misses Q4 revenue recognition
  ✗ Counts gross engagement, not revenue

SHARED GROUND:
  ✓ Both agree the feature rolled out to 40% of users on Sept 1
  ✓ Both agree primary metric should be MRR

RECOMMENDED APPROACH:
  → DiD: Compare MRR of exposed vs. unexposed users, pre/post Sept 1
  → Controls for: user tenure, plan type, geography
  → Time window: 90 days post-launch (captures lag)
  → Expected to close the gap by ~70%
```
