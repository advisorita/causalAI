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

---

## Export Options

Every shared causal report should be produced as an HTML file with two built-in export buttons. Always include these when generating the report output.

### How to generate the report with exports

When producing the shared report as an HTML file, include the following at the top of the `<body>`:

1. **Google Fonts link** in `<head>` — use `Inter` (not system fonts — they break image export):
```html
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&display=swap" rel="stylesheet">
```

2. **Export toolbar** above the report card:
```html
<div id="export-bar" style="max-width:680px;margin:0 auto 1rem;display:flex;gap:.75rem;justify-content:flex-end;">
  <button onclick="exportImage()" style="...">Export image</button>
  <button onclick="exportPDF()" style="...">Export PDF</button>
</div>
```

3. **html2canvas script** + export functions before `</body>`:
```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
<script>
  function exportPDF() {
    document.getElementById('export-bar').style.display = 'none';
    window.print();
    document.getElementById('export-bar').style.display = '';
  }

  async function exportImage() {
    const bar = document.getElementById('export-bar');
    bar.style.display = 'none';
    await document.fonts.ready;
    await new Promise(r => setTimeout(r, 600));
    const canvas = await html2canvas(document.querySelector('.page'), {
      scale: 2,
      useCORS: true,
      allowTaint: true,
      backgroundColor: '#ffffff',
      onclone: function(cloned) {
        cloned.querySelectorAll('*').forEach(el => {
          const cs = window.getComputedStyle(el);
          el.style.fontFamily = 'Inter, sans-serif';
          el.style.letterSpacing = cs.letterSpacing;
          el.style.wordSpacing = cs.wordSpacing;
          el.style.fontKerning = 'none';
        });
      }
    });
    bar.style.display = '';
    const link = document.createElement('a');
    link.download = 'causal-ai-report.png';
    link.href = canvas.toDataURL('image/png');
    link.click();
  }
</script>
```

4. **Print CSS** inside `<style>` — hides toolbar and removes card styling for clean PDF output:
```css
@media print {
  body { background: white; padding: 0; }
  #export-bar { display: none; }
  .page { border: none; border-radius: 0; max-width: 100%; }
}
```

### Export formats

| Format | Best for | How |
|---|---|---|
| **PNG image** | LinkedIn/X post, Slack, Notion | Click "Export image" — downloads 2x resolution PNG |
| **PDF** | Sending to CFO, board memo, email attachment | Click "Export PDF" — browser print dialog, save as PDF |

### Critical: use Inter, not system fonts

html2canvas cannot render `-apple-system`, `BlinkMacSystemFont`, or `Segoe UI` correctly — characters cluster together in the exported image. Always use `font-family: 'Inter', sans-serif` in report HTML output.
