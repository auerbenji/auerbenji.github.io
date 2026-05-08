---
title: "The limits of match-based learning in recreational padel"
parent: Specials
nav_order: 1
---

---
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}
---

# Abstract
{: .d-inline-block }
05-09-2026
{: .label .label-green }

In this article we analyze the recorded padel level development of three players: Henrik, Julius, and Benjamin. Across one year of logged playing history, the available data shows the highest match exposure for Julius, moderate-to-low exposure for Henrik and Benjamin, respectively, and no statistically convincing upward trend in playing level. Simple exponential smoothing was selected as the forecasting method because the available series are short, irregular, and noisy. The resulting forecasts remain largely flat with wide uncertainty bands. The practical implication is: occasional training interventions did not produce measurable sustained improvement. The most viable intervention is therefore not more isolated play, but structured, repeated technical training with an experienced coach. Among the three players, Henrik shows the highest apparent upside and should be prioritized for targeted training.

# Situation
Three players, one year of padel ambition:
Henrik, Julius, and Benjamin have each played padel in a consistent manner for roughly one year and their accumulated match histories differ as follows: Henrik, male, 27, has logged 55 matches. Julius, male, 26, has logged 61 matches. Benjamin, male, 29, has logged 40 matches. For illustrative purposes, the players are introduced in the following figure.

<figure id="Meet the players">
  <img src="/assets/images/specials/birthday-0-comp.jpeg" alt="">
  <figcaption><strong>Figure 1:</strong> Meet the players (LTR: Benjamin, Henrik, Julius) </figcaption>
</figure>

Despite a small asymmetry in match volume, all three players share the same objective: they want to improve their playing level. In recreational padel, level progression is often assumed to follow exposure. More matches should, in theory, lead to better anticipation, cleaner positioning, higher shot tolerance, and better tactical decision-making. On that view, Julius' highest match count should translate into measurable improvement, while Henrik and Benjamin should show slower but still visible progress.

The data, however, do not support that optimistic assumption. The central empirical finding is that playing more is not the same as improving. Match exposure produces experience, but experience alone does not necessarily remove technical errors, tactical shortcomings, or repeated decision failures. This creates the core analytical problem of the study.

# Complication

To evaluate the progression of the players, historic level data was extracted from the available player profiles at [Playtomic](https://playtomic.com/) and plotted over a one-year time horizon. The extracted values were treated as the ground-truth. At the time of analysis, Playtomic did not feature a raw player level export, thus the data originates from screenshots rather than a raw database export. Hence, the numerical values should be interpreted as a good approximation. Note that data accuracy is negligible for the purpose of this analysis, as the variation within the dataset is significantly greater than the inaccuracy introduced via screenshot data extraction.

The resulting graph combines raw data points, Damped Holt's exponential smoothing (HES) fitted data, and forecasted future levels with wide confidence intervals.

HES was chosen because the data series are short, noisy, and do not provide enough observations to estimate a credible seasonal structure. Consequently, Holt-Winters smoothing was discarded. The interested reader is directed to [Forecasting: Principles and Practice](https://playtomic.com/), providing an excellent overview of the applied forecasting method.
More complex models, such as a seasonal ARIMA model or a trend-seasonality decomposition, are too advanced compared to the limited ground truth data. In this setting, a conservative smoothing method is economically superior as it extracts the available signal without overfitting noise.

<figure id="Historic player data, fit and forecast">
  <img src="/assets/images/specials/birthday-chart.svg" alt="">
  <figcaption><strong>Figure 2:</strong> Historic player data, fit and forecast </figcaption>
</figure>

The forecast remains mostly flat. There is no robust evidence of steady upward progression for any of the players. Benjamin’s curve fluctuates around a relatively stable level. Julius shows volatility, including temporary improvements, but no sustained structural break. Henrik shows the strongest possible hint of a slow positive tendency, but the uncertainty remains large enough that this should be interpreted cautiously rather than triumphantly.

This flat outcome is notable because each player had at least one meaningful training or playing effort during the observation period. Henrik trained in August 2025. Julius had a training-related intervention in December 2025 in Barcelona. Benjamin trained in April 2026 in Crete. Yet none of these interventions translated into a clearly visible level increase in the recorded series.

## Three explanations for level stagnation

First, the **timing** of training may have been insufficient. A single intervention can correct awareness, but it rarely rewires execution. Padel improvement depends on repeated exposure to the same corrected movement pattern under pressure. If training occurs too late, too far apart, or without immediate match reinforcement, the expected return decays quickly.

Second, training **consistency** appears inadequate. Improvement in racket sports is not produced by isolated motivation peaks. It requires repetition at a frequency high enough to convert deliberate correction into automatic execution. Without consistency, players return to their previous equilibrium. The level chart then shows not growth, but oscillation.

Third, training **effectiveness** may have been limited by insufficient problem focus. General training can feel useful but still fail to address the true bottleneck. A player who loses points because of poor court positioning will not improve much by only practicing aggressive shots. A player with weak shot selection will not improve reliably by merely increasing intensity. The economically relevant question is not whether training occurred, but whether it attacked the binding constraint.

# Resolution
We suggest structured coaching on key performance bottlenecks: Casual match accumulation and occasional training sessions prove insufficient. If the objective is a steady and continuous level increase, the most viable intervention is structured training with a seasoned coach.

The coach’s role is not merely to provide drills. The coach must diagnose the highest-leverage weakness, define a correction, enforce repetition, and prevent the player from drifting back into old habits. This matters especially in padel, where many mistakes are not obvious to the player during play. Bad positioning, late preparation, poor lob selection, and inefficient transitions often feel normal until an external observer identifies them.

**Among the three players, Henrik appears to have the highest uplift potential.** The data suggest a possible slow trend toward higher levels. If that weak trend is real, then targeted coaching could convert latent match experience into measurable progress.
Henrik therefore represents the most attractive training investment. He already has the match volume. What he likely needs is not more random exposure, but a sharper correction mechanism.

# Action
The proposed intervention is a 90-minute padel training session for Henrik with coach Jose. The session will be sponsored as a birthday present.
This is where the analysis ends and the gift begins.

Henrik, your data have spoken with scientific restraint yet clarity. The curve is not yet impressed. The birthday committee has therefore approved a targeted performance intervention. You get 90 minutes with coach Jose, a proper coach, a proper session, and your clear duty to bend that trendline upward. Henrik aligns the appointment with Jose. Payment will be settled via Julius, Benjamin, and Max.

<figure id="Player development forecast">
  <img src="/assets/images/specials/birthday-1.jpeg" alt="">
  <figcaption><strong>Figure 3:</strong> Player development forecast </figcaption>
</figure>

# Note on fallback coach

In case Jose is not available, Henrik will receive the session with coach Max instead. Max is top of the game.

<figure id="Backup coach">
  <img src="/assets/images/specials/birthday-2-comp2.jpeg" alt="">
  <figcaption><strong>Figure 4:</strong> Backup suggestion for Jose unavailability </figcaption>
</figure>

# Acknowledgements
Special thanks to

- [ChatGPT](https://chatgpt.com/) for image generation

- [Playtomic](https://playtomic.com/) premium subscription at € 9.99 for data gathering

- [Julius](https://www.linkedin.com/in/juliusscheuerer/) for coaching session organization

- [Max](https://www.linkedin.com/in/mmilde1/) for coaching backup