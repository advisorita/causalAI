# Workflow 2: Post-Launch Attribution
## "Did This Feature Actually Drive Revenue?"

---

## Method Selection

### When to use each method:

| Situation | Method |
|---|---|
| Have untreated users + pre/post data | **Difference-in-Differences (DiD)** |
| Rollout was threshold-based (score, tier, date) | **Regression Discontinuity (RDD)** |
| Single market/product, time series data | **CausalImpact (Bayesian structural TS)** |
| No clean control group, need to construct one | **Synthetic Control** |
| Treatment assignment based on observables | **Propensity Score Matching (PSM)** |
| External variable affects treatment but not outcome | **Instrumental Variables (IV)** |

---

## Method 1: Difference-in-Differences (DiD)

### Intuition
Compare the *change* in revenue for treated users vs. untreated users, before and after the feature launched. The treatment effect is the "extra" change that treated users experienced beyond the common trend.

### Key Assumption: Parallel Trends
Without the feature, treated and control users would have followed the same revenue trajectory. **Always plot this before running the regression.**

### Code

```python
import pandas as pd
import statsmodels.formula.api as smf
from linearmodels.panel import PanelOLS

# Required columns: user_id, week, revenue, treated, post
df['did'] = df['treated'] * df['post']

# Basic DiD
model = smf.ols('revenue ~ treated + post + did', data=df).fit(
    cov_type='cluster', cov_kwds={'groups': df['user_id']}
)
effect = model.params['did']
ci = model.conf_int().loc['did']
print(f"Causal effect: ${effect:,.2f} per user")
print(f"95% CI: [${ci[0]:,.2f}, ${ci[1]:,.2f}]")

# Two-way fixed effects (preferred)
panel = df.set_index(['user_id', 'week'])
twfe = PanelOLS.from_formula(
    'revenue ~ did + EntityEffects + TimeEffects', data=panel
).fit(cov_type='clustered', cluster_entity=True)
print(twfe.summary)
```

### Parallel Trends Check

```python
import matplotlib.pyplot as plt

pre_data = df[df['post'] == 0].groupby(['week', 'treated'])['revenue'].mean().unstack()
pre_data.plot(title='Pre-period Revenue Trends: Treated vs Control')
plt.axvline(x=launch_week, color='red', linestyle='--', label='Launch')
plt.legend(['Control', 'Treated', 'Launch date'])
plt.ylabel('Avg Revenue per User')
plt.show()
# Lines should be parallel before launch date
```

### Event Study (Dynamic Effects)

```python
# Shows how treatment effect evolves over time
df['rel_time'] = df['week'] - launch_week
# Reference period = week -1

for t in df['rel_time'].unique():
    if t != -1:
        df[f't_{t}'] = (df['rel_time'] == t) * df['treated']

time_dummies = [c for c in df.columns if c.startswith('t_')]
formula = 'revenue ~ ' + ' + '.join(time_dummies) + ' + EntityEffects + TimeEffects'
event_model = PanelOLS.from_formula(formula, data=df.set_index(['user_id','week'])).fit()

# Plot: x = relative time, y = coefficient, error bars = 95% CI
# Pre-period coefficients near 0 = parallel trends holds
# Post-period coefficients = dynamic treatment effect
```

---

## Method 2: CausalImpact (Time Series)

Best when you have a single time series (e.g., total product revenue) and can find correlated control series (competitor metrics, unaffected segments).

```python
# pip install causalimpact
from causalimpact import CausalImpact
import pandas as pd

# data: DataFrame with outcome in first column, controls in remaining columns
# pre_period: [start, end] of pre-launch window
# post_period: [start, end] of post-launch window

pre_period = ['2024-01-01', '2024-08-31']
post_period = ['2024-09-01', '2024-12-31']

ci = CausalImpact(data, pre_period, post_period)
print(ci.summary())
ci.plot()  # Shows actual vs. synthetic counterfactual
```

**Output interpretation:**
- **Absolute effect**: Revenue added by the feature
- **Relative effect**: % lift
- **Posterior probability**: Confidence that effect is real (want > 95%)

---

## Method 3: Synthetic Control

Best for: single treated unit (e.g., one market, one product line), many pre-periods.

```python
# pip install synth-control
from synth import Synth

# donor_pool: untreated units used to construct synthetic control
# treated_unit: the unit that received treatment
# outcome: the metric
# treatment_time: when treatment started

sc = Synth(
    data=df,
    treated_unit='market_A',
    donor_pool=['market_B', 'market_C', 'market_D'],
    outcome='revenue',
    time='quarter',
    treatment_time='2024-Q3'
)
sc.fit()
sc.plot()  # Actual vs synthetic counterfactual
print(f"Estimated causal effect: {sc.att()}")  # Average Treatment Effect on Treated
```

---

## Diagnostics Checklist

Run these for any method:

- [ ] **Pre-trend test**: Are pre-period coefficients statistically indistinguishable from 0?
- [ ] **Placebo test**: Does the same method find an effect for a random/fake treatment date? (Should be near 0)
- [ ] **Covariate balance**: Are treated and control groups similar on pre-period characteristics?
- [ ] **Robustness check**: Does the effect hold with different control groups, time windows, or model specs?
- [ ] **Magnitude check**: Is the effect economically plausible? (A 500% revenue lift from a UI tweak is suspicious)

---

## Communicating the Result

### For Finance
> "Using difference-in-differences, we estimate the feature caused an incremental $2.4M in revenue over 90 days (80% CI: $1.2M–$3.8M). This accounts for seasonality and user-level differences. There is an 94% probability the effect is real and not due to chance."

### For Product
> "The feature drove a measurable lift in revenue per user. The effect appeared within 2 weeks of launch and grew over the following 6 weeks — consistent with organic adoption rather than a novelty spike. Users who adopted early are retaining at a higher rate, suggesting the effect is durable."

### The Shared Number
> "Both Finance and Product can stand behind $2.4M as the causal estimate. The range reflects honest uncertainty — not a disagreement. With 3 more months of data, the confidence interval will narrow to roughly ±$500K."
