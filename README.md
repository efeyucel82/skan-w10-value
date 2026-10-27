# SKAN 4.0 Conversion Value Design for Week 10 Subscription Value

How much budget does an iOS growth team lose by reading SKAdNetwork postbacks at face value, and how much of it can a smarter conversion value schema win back?

## Result

| Decision maker | What it sees | Week 10 profit |
|---|---|---|
| Blind | SKAN postbacks read as "subscribed or not" | $12,278 |
| Predictive (this project) | Same postbacks decoded with a hybrid schema | **$17,077** |
| Perfect information | The true week 10 value of every user | $20,282 |

On a $150,000 budget the predictive approach earns **39% more profit** than the blind approach and closes **60% of the gap** to perfect information. Across nine scenarios with different diminishing return and budget assumptions the closed gap stays between **54% and 64%**.

## The problem

A consumer AI app sells weekly subscriptions with no free trial and runs paid campaigns on TikTok, Meta, Google and Snapchat. The goal is to split the budget so that profit at week 10 is as high as possible.

On iOS most users decline tracking under ATT. The app knows what each user does but not which ad brought them. The only bridge is Apple's SKAdNetwork 4.0:

* one number between 0 and 63 per install, locked after 48 hours
* low, medium or high only for the 3 to 7 day and 8 to 35 day windows
* no value at all for low volume campaigns
* nothing after day 35

Channels differ most in how long subscribers keep paying. A channel can look like the winner in week 1 and lose money by week 10. SKAN shows that difference late or never.

## Approach

1. **Synthetic world.** 116,173 installs across 72 cells (4 channels × 3 country tiers × 2 platforms × 3 creative types). Each user has a hidden intent score that drives both first day behavior and the 10 weeks of payments. Known traps are planted on purpose: cheap but low intent installs, a channel whose subscribers churn as a group after the first renewal, a Simpson's paradox and very small cells.
2. **Week 10 value model.** A LightGBM model predicts each user's week 10 net value from first 24 hour behavior only. It never sees the ad channel because the app cannot see it on iOS.
3. **Hybrid conversion value schema.** 2 bits encode what the user did on day 0 (not subscribed, subscribed, subscribed with in app purchase, subscribed with voice recording and export). 4 bits encode the model's predicted value bucket within that stage. `CV = stage × 16 + bucket`. A decode table maps every CV to a dollar value.
4. **SKAN simulator and budget race.** iOS data passes through Apple's privacy filter. Three decision makers split the same budget with marginal returns. A referee scores every allocation in the true world.

## Findings

**Blind reading over funds the wrong channel.** TikTok has the best week 2 retention (87%) and collapses to 38% in week 3. Read at face value it looks like the best channel and receives the largest share ($54,000). In truth it loses money.

**Subscribing is not enough.** Two values that both mean "subscribed" decode to $6.00 (CV 16) and $41.64 (CV 31). Exporting a song and the time to first generation separate lasting subscribers from short ones. Trying the text to song feature carries almost no signal (0.5% of model importance) even though nearly everyone tries it.

**Simpson's paradox inside Meta.** The demo creative beats the UGC creative in every country tier but loses in total (1.37 vs 1.49 ROAS) because 70% of its budget went to the lowest value tier.

**The model beats a channel aware baseline.** User level error drops from $2.47 to $0.96 even though the baseline is given the channel and the model is not.

## Limitations

* **Synthetic data.** The model recovers rules I wrote. The value of the project is the method, not the numbers.
* **Privacy thresholds are assumed.** Apple does not publish them. I used 250 and 500 installs per cell.
* **TikTok is still over funded.** The predictive approach cuts TikTok from $54,000 to $36,000 but the ideal is $15,000 and it labels TikTok "Hold". Some short lived subscribers behave like loyal ones on day 0.
* **Channel level only.** Budget is split across channels, not creatives or countries.
* **No postback delay in the decision.** Delays are described but not modeled.
* **No incrementality.** All installs are treated as caused by ads.

## Next steps

* Add the third postback (days 8 to 35) to the decision rule. A low share of "high" for TikTok would expose the cliff about five weeks after launch.
* Calibrate the decode table on Android and ATT opted in users as new cohorts arrive.
* Fit a shifted beta geometric retention curve per channel. A channel where it does not fit is itself a warning.
* Allocate at creative and country level.
* Port the schema to AdAttributionKit.

## Repository

```
notebooks/01_synthetic_world.ipynb   full pipeline, run top to bottom
outputs/skan_schema.csv              64 row conversion value schema with decoded dollar values
outputs/cell_table.csv               72 cells in performance marketing format
outputs/roas_by_decision_maker.csv   week 10 ROAS through each decision maker's eyes
outputs/budget_allocation.csv        the three allocations
outputs/profit_by_decision_maker.csv the referee's scores
outputs/sensitivity.csv              nine scenario robustness check
assumptions.md                       every assumption and why
```

## How to run

Open the notebook in Google Colab and choose Runtime → Run all. It takes under a minute. The random seed is fixed so every run produces the same world. Small differences in model results can appear across LightGBM versions. Run every cell once and in order: running a cell twice moves the random generator and changes the world slightly.
