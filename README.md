# Causal Impact of Email Marketing Campaigns
### A Randomized Controlled Trial Analysis : Hillstrom (2008) Dataset

This project estimates the causal effect of two email marketing campaigns on customer spend, using Kevin Hillstrom's public [MineThatData](https://blog.minethatdata.com/) e-mail analytics dataset — a genuine three-arm randomized controlled trial with ~64,000 customers. It's part of a broader causal inference portfolio spanning both randomized (this project) and observational designs (IV/2SLS, DML-IV, RDD, staggered DiD, Synthetic Control).

Unlike the other projects in that portfolio, this one doesn't need to *manufacture* identification the treatment was randomly assigned. So instead of asking "how do we get a valid counterfactual," this project asks a different question: **given a clean RCT, what's the honest, fully-diagnosed answer, including the places a standard technique doesn't help?**

---

## Dataset

- **~64,000 customers**, randomized into three equal-sized arms:
  - Received a **Mens** merchandise email
  - Received a **Womens** merchandise email
  - **No email** (control)
- **Pre-treatment covariates**: Recency, History (past-year spend), History_Segment, prior Mens/Womens purchase indicators, Zip_Code, Newbie flag, Channel
- **Outcomes** (measured in the two weeks following the campaign): Visit, Conversion, Spend

## Estimand

> The effect of receiving a promotional email : Mens vs. No Email, and separately Womens vs. No Email, on customer spend (`Spend`), using `History` as a CUPED covariate for variance reduction. Conversion and Visit are treated as secondary outcomes. Both comparisons use the same "No Email" arm as control (never each other), since the alternate campaign is itself an active treatment, not an absence of one.

## Methodology

This project follows a 10-step causal inference workflow, applied end-to-end:

| Step | What it checks |
|---|---|
| 0 — Estimand | Explicit, pre-registered definition of the causal question |
| 1 — Data sanity | Shape, duplicates, type/range checks |
| 2 — Assignment integrity | Sample Ratio Mismatch (SRM) test |
| 3 — Covariate balance | Standardized Mean Differences (SMD) + love plot |
| 4 — Outcome exploration | Distribution shape, zero inflation, skew no causal claims yet |
| 5 — Identification assumptions | Unconfoundedness, positivity, SUTVA, consistency, no differential attrition |
| 6 — Estimation | CUPED-adjusted and raw ATE, via Welch's t-test |
| 7 — Robustness | Mann-Whitney U (distribution-free), outlier-capping sensitivity |
| 8 — Final estimates | Bonferroni corrected joint significance, business translation |
| 9 — Heterogeneity | X-learner CATE estimation for Conversion, out of sample validation |

## Key Findings

**The campaigns worked.** Both emails produced a statistically significant increase in spend relative to no email:

| Comparison | Effect on Spend | 95% CI | p-value |
|---|---|---|---|
| Mens E-Mail vs. Control | +$0.77 | [0.49, 1.05] | 1.16 × 10⁻⁷ |
| Womens E-Mail vs. Control | +$0.42 | [0.17, 0.68] | 0.0011 |

Both hold under a Bonferroni correction for testing twice against the same control group, and both are corroborated by a second, distribution-free test (Mann-Whitney U) and by outlier capping sensitivity checks the effect doesn't depend on a handful of large purchases.

**CUPED a diagnosed null result.** CUPED is supposed to tighten the confidence interval around a treatment effect by using a correlated pre-treatment covariate to strip out noise. Here, it barely moved the estimate at all.

The reason is measurable, not mysterious: `History` (the CUPED covariate) correlates with `Spend` at only **r ≈ 0.02**. CUPED's variance reduction scales with the squared correlation between the covariate and the outcome a weak covariate simply gives the technique nothing to work with. This is arguably the more useful part of the project: most CUPED walkthroughs only ever show it working. This one shows, with the actual numbers, exactly the condition under which it doesn't a full-year spend total turns out to carry very little information about a rare, zero inflated, two week purchase event.

**Heterogeneity a lesson in validation.** An X-learner was used to estimate individual-level treatment effects (CATE) on Conversion. As a sanity check, the average of these individual estimates reconstructed the population-level ATE almost exactly a good sign the model wasn't broken. A deeper look at the subgroup with the lowest predicted uplift showed a striking pattern in sample (consistent with a "sleeping dog" effect, where marketing contact backfires for some customers) but the pattern **did not replicate** when the model was refit on a held out split and re evaluated on data it hadn't seen. That's kept in the analysis deliberately: it's a concrete, worked example of why heterogeneous treatment effect claims need out of sample validation before they're trusted, not just an appendix note.

## Limitations

- `Spend` appears to be **topcoded at $499** in the released data a property of the dataset, not the underlying purchase behavior.
- Delivery/open-rate data isn't available, so full treatment consistency (did "assigned to Mens E-Mail" always mean "received it") can't be directly verified.
- The apparent heterogeneous subgroup effect found in sample did not hold up out of sample and should not be used for targeting decisions without further validation on independent data.

## Repository Contents

- `hillstrom_project.ipynb` — full analysis, steps 0–9
- `readme_assets/key_visual.png` — headline figure referenced above

## Requirements
- pandas
- numpy
- scipy
- matplotlib
- seaborn
- scikit-learn
- econml
## Author

Baha Kahri — Data Scientist / Causal Inference Specialist
[LinkedIn](https://www.linkedin.com/in/baha-kahri/) · [GitHub](github.com/Bahakahri)
