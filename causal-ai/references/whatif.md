# Workflow 3: Pre-Launch What-If Simulation
## "What Will Happen to Revenue If We Ship X?"

---

## The Goal

Simulate a distribution of plausible revenue outcomes *before* a single line of code is committed. Replace gut-feel debates with structured uncertainty.

> "Instead of arguing about whether the feature will work, let's agree on the range of outcomes we believe is plausible — and what assumptions drive that range."

---

## Framework: Causal Revenue Model

A what-if simulation requires a causal model — a set of explicit assumptions about how the feature affects the system.

### Step 1: Build the Causal Chain

Map the intervention → intermediate outcomes → revenue:

```
Feature ships
    → Adoption rate (what % of users will try it?)
        → Engagement lift (do adopters use the product more?)
            → Retention lift (do adopters churn less?)
                → Upsell rate (do adopters upgrade plans?)
                    → Incremental Revenue
```

This causal chain is the shared model. Finance and Product agree on it upfront — disagreements surface in the parameter estimates, not in the conclusion.

### Step 2: Estimate Parameters with Uncertainty

For each node in the causal chain, specify a distribution — not a point estimate.

```python
import numpy as np
from scipy import stats

# Each parameter is a distribution, not a number
params = {
    # Adoption: Beta distribution (bounded 0-1)
    'adoption_rate':     stats.beta(a=4, b=12),    # mean ~25%, skeptical prior
    
    # Engagement lift: Log-normal (positive, right-skewed)
    'engagement_lift':   stats.lognorm(s=0.4, scale=np.exp(0.15)),  # mean ~16%
    
    # Retention lift: Normal (can be negative = feature hurts retention)
    'retention_lift_pp': stats.norm(loc=1.5, scale=1.0),  # +1.5pp retention, ±1pp
    
    # Upsell rate among adopters
    'upsell_rate':       stats.beta(a=2, b=18),    # mean ~10%
    
    # Revenue per upsell
    'revenue_per_upsell': stats.norm(loc=50, scale=10),   # $50/mo ± $10
}
```

**Key insight**: Finance sets the conservative parameters. Product sets the optimistic ones. The simulation shows the *full distribution* — both teams see their scenario represented.

### Step 3: Monte Carlo Simulation

```python
import pandas as pd
import matplotlib.pyplot as plt

N_USERS = 100_000       # Total eligible user base
N_SIM   = 10_000        # Number of simulation runs
MRR_PER_USER = 20       # Current baseline MRR per user

results = []

for _ in range(N_SIM):
    # Draw from each distribution
    adoption     = params['adoption_rate'].rvs()
    eng_lift     = params['engagement_lift'].rvs()
    ret_lift_pp  = params['retention_lift_pp'].rvs()
    upsell_rate  = params['upsell_rate'].rvs()
    rev_upsell   = params['revenue_per_upsell'].rvs()
    
    n_adopters = N_USERS * adoption
    
    # Revenue from retention improvement
    # Each 1pp retention lift ≈ 1% more users kept per month
    retention_rev = n_adopters * MRR_PER_USER * (ret_lift_pp / 100)
    
    # Revenue from upsells
    upsell_rev = n_adopters * upsell_rate * rev_upsell
    
    # Total incremental monthly revenue
    total_incremental = retention_rev + upsell_rev
    results.append(total_incremental)

results = pd.Series(results)

# Summary statistics
print("=== WHAT-IF SIMULATION RESULTS ===")
print(f"Median incremental MRR:     ${results.median():>10,.0f}")
print(f"80% confidence interval:    ${results.quantile(0.10):>10,.0f} — ${results.quantile(0.90):>10,.0f}")
print(f"Downside (10th pct):        ${results.quantile(0.10):>10,.0f}")
print(f"Upside (90th pct):          ${results.quantile(0.90):>10,.0f}")
print(f"Probability of positive ROI: {(results > 0).mean()*100:.1f}%")
print(f"Annualized median impact:   ${results.median()*12:>10,.0f}")
```

### Step 4: Visualize the Distribution

```python
fig, axes = plt.subplots(1, 2, figsize=(14, 5))

# Left: Revenue distribution
axes[0].hist(results / 1000, bins=80, color='steelblue', alpha=0.7, edgecolor='white')
axes[0].axvline(results.median()/1000, color='navy', linewidth=2, label=f'Median: ${results.median()/1000:.0f}K')
axes[0].axvline(results.quantile(0.10)/1000, color='red', linewidth=1.5, linestyle='--', label='10th pct')
axes[0].axvline(results.quantile(0.90)/1000, color='green', linewidth=1.5, linestyle='--', label='90th pct')
axes[0].set_xlabel('Incremental Monthly Revenue ($K)')
axes[0].set_ylabel('Frequency')
axes[0].set_title('Revenue Impact Distribution')
axes[0].legend()

# Right: Cumulative probability (useful for Finance)
sorted_results = results.sort_values().reset_index(drop=True)
axes[1].plot(sorted_results / 1000, np.linspace(0, 1, len(sorted_results)), color='steelblue')
axes[1].axhline(0.5, color='navy', linestyle='--', alpha=0.5)
axes[1].axhline(0.1, color='red', linestyle='--', alpha=0.5)
axes[1].set_xlabel('Incremental Monthly Revenue ($K)')
axes[1].set_ylabel('Cumulative Probability')
axes[1].set_title('Probability of Exceeding Revenue Target')
plt.tight_layout()
plt.show()
```

---

## Sensitivity Analysis: Which Assumption Matters Most?

```python
from scipy.stats import spearmanr

# Draw parameter samples and correlate with outcomes
samples = pd.DataFrame({k: v.rvs(N_SIM) for k, v in params.items()})
samples['revenue'] = results.values

sensitivities = {
    col: spearmanr(samples[col], samples['revenue']).correlation
    for col in params.keys()
}

# Plot: which parameter drives variance the most?
pd.Series(sensitivities).abs().sort_values().plot(
    kind='barh', title='Sensitivity: Which Assumption Drives Revenue Variance?'
)
plt.xlabel('Spearman Correlation with Revenue')
plt.tight_layout()
plt.show()
```

**Use this to focus the Finance/Product debate**: "We don't need to agree on every assumption. We just need to align on adoption rate — it explains 60% of the variance."

---

## Communicating the What-If

### For Finance
> "Under conservative assumptions (10th percentile scenario), the feature breaks even within 6 months. Under our baseline assumptions, it generates $X in annualized incremental MRR. The downside is bounded; the upside is meaningful."

### For Product
> "The simulation shows a positive median outcome with 87% probability of generating meaningful revenue lift. The biggest lever is adoption rate — if we can get to 30% adoption (vs. our 25% base case), the upside grows by 40%."

### The Go/No-Go Frame
> "The question isn't 'will this work?' The question is: 'Is the expected value of shipping positive, given honest uncertainty?' Based on the simulation, yes — and here's what we'd need to believe for it to be a bad bet."
