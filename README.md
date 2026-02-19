Email Campaign Experiment Analysis (480K Users)
Executive Summary：

This project analyzes a large-scale email experiment to optimize campaign strategy and improve downstream conversion outcomes (account linking and funding). Using 480,000 users across 24 treatment variants (segment × frequency) and baseline control rates, I quantified content performance, segment responsiveness, cadence effects, negative feedback risk, and causal lift via statistical testing. The analysis produced actionable recommendations on which email content to send, to whom, and for how long.

Key Results (Highlights)：

Best-performing email content: ml_funding_faq achieved the highest open rate (~0.26), while most other messages were ~0.17–0.18.

Engagement is segment-driven: users with recent activity (20-day flag) consistently showed the strongest engagement and higher conversion potential.

Cadence timing matters: open rates peaked around Day 1–2, then decayed over subsequent sends → supports shorter campaign windows / front-loaded cadence.

Causal impact (A/B vs control): most segments showed a statistically significant increase in funding rate in treatment vs control using one-sided proportion z-tests.

Risk signals surfaced: unsubscribe/spamreport events varied by segment and content, indicating the need to balance lift vs long-term deliverability and churn risk.

Data quality issues detected: anomalous near-zero delivery for order_9 (weekly cohort) suggests instrumentation/system issues → exclude or re-run before final rollout decisions.

Business Questions Answered：

Which email content generates the highest engagement (open rate)?

Which user segments respond best (and worst) to email campaigns?

Does sending emails cause higher funding rates compared to control?

Is daily or twice-weekly frequency better—and how long should campaigns run?

What are the tradeoffs between performance lift and negative feedback (unsubscribe/spam)?

Where are the drop-offs in the funnel (open → link → fund)?

Data Sources：

Email content list (10 templates): message IDs (e.g., ml_funding_faq, ml_investing_101, …)

User segmentation table (sample_segment_groups.csv): 4 binary factors + segment group naming

Experiment assignment table (sample_uuid_email_order.csv): per-user randomized email order for 10 sends (order_0 … order_9)

Email event logs (email_events.csv): open, delivered, spamreport, unsubscribe, etc.

User outcome table (user_events.csv): message status per email + timestamps for account linking/funding + activity features

Control baseline table (control_groups_rate.csv): baseline link/funding rates for each segment

Methodology Overview：
1) Content Performance (Open Rate by Message)

Converted message status into binary open indicators (open=1, delivered=0, NaN excluded).

Ranked messages by open rate to identify top-performing content.

2) Segment & Frequency Analysis (Engagement Drivers)

Computed open rate by segment group and frequency (daily vs twice-weekly).

Aggregated daily/weekly to compare segmentation effects without cadence noise.

Quantified correlations between open rate and segment flags (directional insight only).

3) Negative Feedback Monitoring

Calculated unsubscribe + spamreport counts by segment and message.

Flagged segments and messages with elevated negative responses.

4) Conversion Outcomes (Link & Funding Rates)

For users who received ≥1 email, computed:

link_rate = P(first_linked_bank_account_at not null)

funding_rate = P(first_funded_at not null)

act_rate from activity features

5) Causal Lift: Treatment vs Control A/B Tests

Merged treatment conversion metrics with baseline control rates by segment.

Ran one-sided two-sample proportion z-tests to test:

H0: funding_rate_treat ≤ funding_rate_control

H1: funding_rate_treat > funding_rate_control

Output: delta_fund_rate, p_value, significance flags.

6) Time-Series Engagement (Open Rate by Send Order)

Reconstructed day-level engagement by mapping per-user order_k to the actual message status on that day.

Compared open-rate curves for daily vs twice-weekly strategies.

Identified engagement decay pattern and anomalies.

7) Funnel Diagnostics

Built a segment-level funnel view:

max_email_open_rate → link_rate → funding_rate

Derived:

funnel_delta = max_open_rate - funding_rate

extra_conversion_rate = delta_fund_rate / max_open_rate

Used funnel plots to highlight segments with best efficiency and greatest headroom.

Deliverables

Ranked email content list by open rate (top candidate for rollout).

Segment responsiveness map (who to target first).

Experiment impact table: deltas + p-values (treatment vs control).

Time-series cadence recommendation (optimal campaign length and send timing).

Negative feedback dashboard (unsubscribe/spamreport) for risk management.

Funnel charts summarizing drop-offs and conversion efficiency.

Figures (What each plot shows)

Open rate by email message
Ranks which content drives the strongest engagement.

Open rate by segment group (daily vs weekly)
Compares engagement differences across segments and cadence.

Correlation heatmap (open rate vs segment flags + message indicators)
Highlights which user signals correlate with engagement.

Negative event table (spamreport + unsubscribe)
Identifies risky segments/messages affecting deliverability.

Funding rate: treatment vs control + delta overlay
Shows incremental lift across segments and significance results.

Link rate: treatment vs control + delta overlay
Evaluates link improvements where not already saturated.

Open rate time series (order_0 … order_9) for daily vs weekly cohorts
Finds the best send timing and observes engagement decay.

Funnel charts (open → link → fund)
Visualizes drop-offs and prioritizes segments for optimization.

Recommendations (Actionable)：

Use ml_funding_faq as a primary message template (highest engagement).

Prioritize segments with recent 20-day activity for maximum responsiveness.

Front-load sends: engagement peaks around Day 1–2, then decays → shorten campaign duration to reduce fatigue.

Monitor unsubscribe/spamreport by segment and throttle frequency where negative signals spike.

Resolve delivery instrumentation issues (e.g., order_9 anomaly) before final strategy rollout.

Tech Stack：

Python, Pandas, NumPy, Statsmodels (proportion z-test), Matplotlib, Seaborn.
How to Run (Suggested Repo Structure)

Recommended folder layout:

.
├── data/
│   ├── sample_segment_groups.csv
│   ├── sample_uuid_email_order.csv
│   ├── email_events.csv
│   ├── user_events.csv
│   └── control_groups_rate.csv
├── notebooks/
│   └── email_experiment_analysis.ipynb
├── figures/
│   ├── open_rate_by_message.png
│   ├── open_rate_heatmap.png
│   ├── funding_ab_test.png
│   ├── cadence_time_series.png
│   └── funnel.png
└── README.md


Run:

Install dependencies
pip install pandas numpy matplotlib seaborn statsmodels

Open notebook in notebooks/ and execute cells in order.

Notes / Limitations：

Some send-day delivery logs contain high missingness (NaN) and an observed anomaly for order_9 in the weekly cohort; results should exclude those days or be revalidated after instrumentation fixes.

Correlation analysis was used for prioritization; causal impact was assessed through A/B testing vs control baselines.

Next Step (Optional Enhancements)

Add confidence intervals for lift (Wilson / Newcombe intervals).

Build a unified KPI dashboard (Open / CTR proxy / Link / Fund / Spam / Unsub) by segment and time.

Model heterogeneous treatment effects (uplift modeling) to optimize targeting.
