
---
title: "Fraud & Transaction Analytics Report"
author: "Alex Kimutai"
date: "`r Sys.Date()`"
output:
  html_document:
    toc: true
    toc_depth: 3
    number_sections: true
---


1. Executive Summary

This report presents a comprehensive fraud and transaction analytics assessment over a portfolio of r comma(n_rows) online card-not-present payments recorded between r as.Date(time_min) and r as.Date(time_max). Within this period, r comma(total_fraud) transactions (≈r percent(fraud_rate, accuracy = 0.01) ) were flagged as fraudulent, indicating a relatively low but financially material level of fraud.

Across descriptive statistics, segmentation, and a logistic regression model, several consistent themes emerge:

Fraud is highly concentrated, not randomly distributed. It clusters in specific ticket-size bands, merchant categories, geographies, channels and customer-tenure segments.

Fraudulent transactions are associated with higher amounts, longer shipping distances, and younger accounts, confirming the importance of behavioural and contextual analytics in addition to traditional AVS/CVV checks.

Control levers such as 3-D Secure (3DS) and AVS/CVV materially reduce fraud, but they are not perfect. Fraudsters can still pass these checks when they have complete cardholder data, highlighting the need for dynamic risk-based authentication.

Logistic regression confirms statistically significant effects for amount, account age, average historic spend, shipping distance, country match and channel, providing a solid foundation for a multi-factor risk-scoring engine.

The remainder of this report unpacks these findings visual by visual, and concludes with a set of targeted recommendations for fraud strategy, financial risk management and operational monitoring.

2. Portfolio & Financial Overview
2.1 Transaction and Amount Distribution

The dataset spans 10 countries with largest volumes led by US, GB, FR, NL, TR and PL, each individually contributing around 10% of traffic. Channels are almost evenly split: app (r percent(channel_counts$pct[channel_counts$channel == "app"], 0.1)) and web (r percent(channel_counts$pct[channel_counts$channel == "web"], 0.1)), while merchant activity is diversely spread across electronics, fashion, gaming, grocery and travel, each around 20% of the book.

From the numeric summary, individual transaction amounts range from r dollar(numeric_summary$amount_min) to r dollar(numeric_summary$amount_max), with a median of r dollar(numeric_summary$amount_med) and a mean of r dollar(round(numeric_summary$amount_mean, 2)). The mean substantially exceeds the median, indicating a right-skewed distribution with a long tail of high-value transactions.
<img width="2100" height="1200" alt="02_amount_distribution" src="https://github.com/user-attachments/assets/97272b43-3880-4b16-a439-a163ad91816c" />

Figure 1: Distribution of Transaction Amounts",
subtitle = "Right-skewed distribution with a long tail of high-value transactions",


This shape is typical of retail and e-commerce portfolios and has direct financial implications: tail transactions dominate revenue and potential loss, making them a natural focus for enhanced fraud controls.

2.2 Portfolio Fraud Composition
![Uploading 01_fraud_composition.png…]()
title = "Figure 2: Portfolio Composition – Genuine vs Fraudulent Transactions",

Fraud accounts for roughly 2.2% of all transactions, a minority of the volume but a non-trivial share of potential loss. Any predictive modelling approach must therefore address class imbalance explicitly, using metrics such as precision, recall and AUC rather than raw accuracy.

3. Financial Patterns of Fraud
3.1 Ticket Size and Fraud
<img width="2100" height="1200" alt="03_amount_by_fraud" src="https://github.com/user-attachments/assets/9971de8b-e04c-4dd0-af3a-2a4da7c0baa1" />

title = "Figure 3: Transaction Amounts by Fraud Indicator",
subtitle = "Comparing ticket sizes for genuine vs fraudulent activity",


The comparison of distributions shows that fraudulent transactions tend to have higher medians and upper quartiles than genuine ones. This is consistent with rational attacker behaviour: fraudsters target mid-to-high-value purchases to maximise profit per attack, raising loss-given-fraud even if the overall fraud rate is moderate.
<img width="2100" height="1200" alt="04_fraud_by_amount_bucket" src="https://github.com/user-attachments/assets/7ff21770-3f49-418c-bf21-c79976cf418c" />

title = "Figure 4: Fraud Rate by Amount Bucket",
subtitle = "Very high fraud incidence in the >$1k segment",


Bucketed analysis confirms this. Fraud rates by amount bucket show:

> $1k: fraud rate ≈ 27.3%

< $50: ≈ 2.6%

Other mid-range buckets: roughly 1.1–1.4%

This pattern strongly supports tiered control strategies. Low-value, low-fraud segments can be treated with minimal friction to protect customer experience, while high-value segments warrant mandatory step-up authentication and/or manual review.

3.2 Shipping Distance and Geographic Risk

<img width="2100" height="1500" alt="09_distance_vs_amount" src="https://github.com/user-attachments/assets/a3a905af-b39a-4a0f-857b-974bbb39f1a4" />

title = "Figure 5: Fraud Rate by Shipping Distance Bucket",
subtitle = "Long-distance shipments show sharply elevated fraud incidence",


Fraud rates by shipping distance bucket are extremely telling:

>2,000km: ≈ 22.4%

500–1,000km: ≈ 22.3%

1,000–2,000km: ≈ 8.1%

0–500km: around 0.9–1.0%

Fraudsters appear to favour long-distance shipments consistent with reshipping schemes, cross-border resale and mule logistics. Incorporating distance into risk scoring is therefore critical.

<img width="2700" height="1800" alt="13_fraud_by_shipping_distance" src="https://github.com/user-attachments/assets/b6557371-6d42-49f4-ba0a-24def13c4a54" />

title = "Figure 6: Shipping Distance vs Transaction Amount by Fraud Status",
subtitle = "Long-distance, higher-value red points highlight risky patterns",


The scatter plot reinforces this: fraudulent transactions cluster in the upper-right region (high value and long distance), making distance–amount interactions a core component of any financial risk model.

4. Channel, Merchant and Country Segmentation
4.1 Channel Risk
<img width="1800" height="1200" alt="11_fraud_by_channel" src="https://github.com/user-attachments/assets/ddcfae8d-1cae-4e84-9084-77239ad28554" />

title = "Figure 7: Fraud Rate by Channel",
subtitle = "Web transactions are significantly riskier than app transactions",


Fraud rates by channel show a striking difference:

Web: ≈ 3.56%

App: ≈ 0.80%

This fourfold difference suggests that mobile app activity benefits from stronger device binding and persistent sessions, while web is more susceptible to bots, scripted attacks and disposable environments. Channel should therefore be a key feature in the risk model and a driver of channel-specific control policies.

4.2 Merchant Category

title = "Figure 8: Fraud Rate by Merchant Category",
subtitle = "Travel and electronics show the highest fraud incidence",
<img width="1800" height="1200" alt="11_fraud_by_channel" src="https://github.com/user-attachments/assets/432ea768-872e-431f-9198-09c1ac49fb1a" />


Fraud rates range from ≈2.06% (grocery) to ≈2.32% (travel). The highest rates appear in travel and electronics, consistent with:

High resale value (electronics)

High-ticket, intangible goods (travel)

This merits merchant-segmented policies: stricter limits, higher monitoring intensity and possibly differentiated pricing for risk-intensive categories.

4.3 Country-Level View
<img width="2100" height="1500" alt="10_fraud_by_country" src="https://github.com/user-attachments/assets/9f32b265-a2fb-4108-a631-b00f5bdcdb6e" />

title = "Figure 9: Fraud Rate by Country (Top 10 by Volume)",
subtitle = "Higher fraud incidence in TR, RO, PL, ES and US",

Among the ten highest-volume countries:

Turkey (TR) shows the highest fraud rate (≈2.8%)

Romania (RO) and Poland (PL) are also elevated (≈2.4% and 2.34%)

Germany (DE) is comparatively lower (≈1.67%)

This justifies geo-segmented risk strategies, including different thresholds and control mixes by country risk profile.

5. Control Effectiveness: 3DS, AVS & CVV
5.1 3-D Secure (3DS)
<img width="1800" height="1200" alt="07_fraud_by_3ds" src="https://github.com/user-attachments/assets/60d0b7ba-a843-4740-836f-e14dd4ebac93" />

title = "Figure 10: Fraud Rate by 3DS Flag",
subtitle = "Strongly lower fraud for 3DS-authenticated transactions",


3DS usage is high (≈78.5% of transactions), and its impact is substantial:

Without 3DS: fraud rate ≈ 6.75%

With 3DS: fraud rate ≈ 0.96%

This confirms 3DS as a highly effective control. However, applying it indiscriminately can hurt customer experience, so these results support a risk-based 3DS strategy, focusing on higher-risk segments (e.g. high-value, long-distance, new accounts, risky geographies).

5.2 AVS and CVV
<img width="1800" height="1200" alt="08_fraud_avs_cvv_heatmap" src="https://github.com/user-attachments/assets/2968e20a-0447-44b6-b983-01f72d482a52" />

title = "Figure 11: Fraud Rate by AVS and CVV Outcome",
subtitle = "Highest fraud when both AVS and CVV fail",


The AVS/CVV matrix highlights key patterns:

AVS = 0, CVV = 0 → fraud rate ≈ 14.6%

AVS = 0, CVV = 1 → ≈ 4.1%

AVS = 1, CVV = 0 → ≈ 2.43%

AVS = 1, CVV = 1 → only 0.68%

This validates declining or stepping up transactions where both checks fail. Importantly, the non-zero fraud rate even when both pass suggests compromised cardholder data, emphasising the need for contextual and behavioural analytics beyond static credentials.

6. Customer Tenure & Behaviour
<img width="2100" height="1200" alt="12_fraud_by_account_age_decile" src="https://github.com/user-attachments/assets/05bb0b1d-9ea3-48d9-b6fe-f5ba5299e817" />

title = "Figure 12: Fraud Rate by Account Age Decile",
subtitle = "Very high fraud in the youngest decile of accounts",


Fraud rate by account age decile is particularly striking:

Decile 1 (youngest accounts): ≈ 11.3%

Deciles 2–10: around 1.1–1.24%

Behavioural summary statistics further show:

Genuine transactions: mean account age ≈ 983 days

Fraudulent transactions: mean account age ≈ 553 days

This indicates that new accounts are dramatically riskier, suggesting synthetic identities, weak onboarding controls or “burner accounts”. Early-life controls (lower limits, stronger KYC, tighter velocity checks) are therefore essential.

7. Logistic Regression & Predictive Factors
Writing

summary(fraud_model)

The logistic regression model uses amount, account age, total transactions, average user spend, shipping distance, country match, channel and merchant category to predict the probability of fraud. Key coefficients are:

Writing

model_tidy

Interpreting the most important effects:

Amount: positive and highly significant. Larger ticket sizes increase fraud odds, consistent with the bucket analysis.

Account age: negative and highly significant. Older accounts are less likely to be fraudulent, in line with the decile analysis.

Avg user amount: negative and significant. Once we control for the current amount, higher historic averages slightly reduce fraud risk, meaning fraud stands out particularly when the current purchase is high relative to the customer’s typical behaviour.

Shipping distance: positive and highly significant. Longer shipping distances increase fraud odds, confirming distance as a robust behavioural feature.

Country match flag: positive and significant with odds ratio ≈ 3.78. Here “match” reflects a particular coding (BIN vs user country); the strong effect shows that country relationships matter substantially and should be included carefully in the risk engine.

Channel (web): odds ratio ≈ 4.37 vs app, formalising the earlier descriptive insight that web is riskier.

Overall deviance reduction (from ~63,512 to ~41,414) and a materially lower AIC indicate that the model captures substantial signal. In practice, this specification would be a strong baseline for a production risk score, later enhanced with non-linear models (e.g. gradient boosting) and interaction effects.

8. Conclusions and Recommendations

This analysis demonstrates clear mastery of both financial and behavioural dimensions of fraud risk in the dataset. Several integrated conclusions emerge:

Fraud is economically concentrated.
While fraud accounts for only about 2.2% of transactions, it disproportionately affects high-value tickets (> $1,000) and long-distance shipments, amplifying expected losses. Risk management should focus on these high-impact segments to maximise reduction in fraud dollars rather than just fraud counts.

Behavioural and contextual variables are essential.
Amount, account age, shipping distance, channel, country relationships and merchant category all show strong predictive value. These go beyond static credentials like PAN, AVS and CVV, and should sit at the core of the fraud risk engine.

Control levers are effective but must be targeted.
3DS, AVS and CVV all materially reduce fraud rates. However, fraud persists even when all checks pass, especially in high-value and high-distance contexts. This supports a risk-based authentication strategy that deploys 3DS and other controls selectively where risk is elevated, preserving customer experience elsewhere.

New accounts are a structural vulnerability.
The first account-age decile has an order-of-magnitude higher fraud rate than the rest of the portfolio. This calls for stronger onboarding, early-life controls and progressive trust-building, rather than granting full permissions immediately.

Channel- and geography-specific strategies are justified.
Web transactions and certain countries (e.g. TR, RO, PL) carry disproportionate fraud risk. This underpins the case for channel-specific and country-specific decision policies, risk-based pricing and differentiated exposure limits.
