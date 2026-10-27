# Assumptions

Every number below is an assumption chosen to look like a plausible consumer AI subscription app. None of them is real market data or data from any company. The first set was checked against realistic ranges and recalibrated once: the first draft had installs too cheap ($0.52 average CPI) which made the low intent channel the most profitable one.

## Revenue

| Assumption | Value | Why |
|---|---|---|
| Weekly subscription price | $7.99 | Typical weekly price for consumer AI apps |
| In app purchase price | $3.99 | Small credit or voice pack |
| Store commission | 30% | Standard rate. All revenue in the project is net |
| Free trial | None | Subscribers pay on day 0. The uncertainty is retention, not conversion timing |
| Horizon | 10 weeks | Business goal is week 10 profit and short payback |

## Channels

| Channel | CPM | IPM | CPI | Intent | Planted trap |
|---|---|---|---|---|---|
| Snapchat | $8 | 7 | $1.14 | −0.6 | Cheapest installs, least interested users |
| TikTok | $11 | 6 | $1.83 | −0.2 | 45% of users are short lived subscribers who renew once then churn as a group |
| Meta | $14 | 5 | $2.80 | +0.2 | Steady and loyal. Contains the Simpson's paradox |
| Google | $20 | 4 | $5.00 | +0.5 | Expensive but users arrive by searching |

## Other layers

| Layer | Values | Why |
|---|---|---|
| Country CPM multiplier | US 1.0, Tier 1 0.7, Tier 2 0.3 | Ad cost differs sharply by market |
| Country price multiplier | US 1.0, Tier 1 0.9, Tier 2 0.5 | Apps price locally |
| Country intent bonus | US +0.3, Tier 1 +0.1, Tier 2 −0.4 | Willingness to pay differs |
| Platform | iOS CPM ×1.2, intent +0.15 | iOS users tend to pay more |
| Creative "hype" | IPM ×1.4, CTR ×1.5, intent −0.4 | Overpromises, attracts curious users |
| Creative "demo" | IPM ×0.8, CTR ×0.8, intent +0.3 | Shows the real product, attracts informed users |
| Creative "UGC" | IPM ×1.0, CTR ×1.1, intent 0 | Middle ground |
| Personal intent noise | Normal, standard deviation 1.0 | People differ more than channels do |

## Test budget

| Assumption | Value | Why |
|---|---|---|
| Budget per channel | $40,000 | Gives about 116,000 installs |
| Country split | US 50%, Tier 1 30%, Tier 2 20% | Typical test plan weighted to the main market |
| Platform split | iOS 60%, Android 40% | Subscription apps lean on iOS |
| Meta demo country split | 15%, 15%, 70% | Plants the Simpson's paradox |
| New test cells | Demo on Snapchat and TikTok gets 15% of normal budget | Plants small sample and missing SKAN data |

## Behavior

| Assumption | Why |
|---|---|
| Day 0 events depend on intent through a logistic score | Standard way to turn a score into a probability |
| Text to song has a weak link to intent | Not every event is informative. Tests whether the model can ignore noise |
| Short lived TikTok users subscribe more but record and export less | Leaves a day 0 trace of the later cliff |
| Weekly renewal probability is personal | Users differ. This is the idea behind shifted beta geometric models |
| A user who churns never returns | Simplification |
| About 1% of non subscribers subscribe within the first week | Gives non subscribers a non zero value in the schema |

## SKAN and decisions

| Assumption | Value | Why |
|---|---|---|
| No postback value | Under 250 iOS installs per cell | Apple does not publish thresholds |
| Coarse value only | 250 to 500 iOS installs per cell | Same |
| Blind rule of thumb | Every subscriber pays 4 weeks | Common shortcut |
| Missing cells, predictive approach | Filled with the channel's visible iOS average | "Move up to channel level" |
| Diminishing returns | Value grows with spend to the power 0.7 | Best audiences run out first. Tested at 0.6 and 0.8 |
| Budget | $150,000 | Tested at $100,000 and $250,000 |
| Allocation | $1,000 steps to the highest marginal return | Decide on marginal, not average, ROAS |
