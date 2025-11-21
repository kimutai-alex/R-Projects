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

# 1. Executive Summary

This report presents a comprehensive fraud and transaction analytics assessment over a portfolio of `r comma(n_rows)` online card-not-present payments recorded between `r as.Date(time_min)` and `r as.Date(time_max)`. Within this period, **`r comma(total_fraud)` transactions (≈ `r percent(fraud_rate, accuracy = 0.01)` )** were flagged as fraudulent, indicating a relatively low but financially material level of fraud.

Across descriptive statistics, segmentation, and a logistic regression model, several consistent themes emerge:

- **Fraud is highly concentrated**, not randomly distributed. It clusters in specific ticket-size bands, merchant categories, geographies, channels and customer-tenure segments.  
- **Fraudulent transactions involve higher amounts**, longer shipping distances, and younger accounts, confirming the importance of behavioural and contextual analytics beyond traditional AVS/CVV checks.  
- **3-D Secure (3DS)** and **AVS/CVV** materially reduce fraud, but they are not perfect. Fraudsters can still pass these checks when they possess complete cardholder data.  
- **Logistic regression confirms significant effects for amount, account age, average historic spend, shipping distance, country match, and channel**, forming a strong foundation for a multi-factor risk-scoring engine.

The remainder of this report unpacks these findings visual by visual and concludes with targeted recommendations for fraud strategy, financial risk management and operational monitoring.

---

# 2. Portfolio & Financial Overview

## 2.1 Transaction and Amount Distribution

The dataset spans 10 countries, with largest volumes led by **US, GB, FR, NL, TR and PL**, each contributing roughly 10% of total activity. Channels are nearly evenly split between **app (`r percent(channel_counts$pct[channel_counts$channel == "app"], 0.1)`)** and **web (`r percent(channel_counts$pct[channel_counts$channel == "web"], 0.1)`)**. Merchant activity is evenly distributed: electronics, fashion, gaming, grocery and travel each represent approximately 20% of the portfolio.

Transaction amounts range from **`r dollar(numeric_summary$amount_min)`** to **`r dollar(numeric_summary$amount_max)`**, with a median of **`r dollar(numeric_summary$amount_med)`** and a mean of **`r dollar(round(numeric_summary$amount_mean, 2))`**, indicating a strong **right-skewed distribution**.

<img width="2100" height="1200" alt="02_amount_distribution" src="https://github.com/user-attachments/assets/97272b43-3880-4b16-a439-a163ad91816c" />

**Figure 1: Distribution of Transaction Amounts**  
_Right-skewed distribution with a long tail of high-value transactions_

This shape is typical of e-commerce portfolios and has major financial implications: **tail transactions dominate revenue and potential loss exposure**, making them a natural focus for enhanced fraud controls.

---

## 2.2 Portfolio Fraud Composition

![Uploading 01_fraud_composition.png…]()

**Figure 2: Portfolio Composition – Genuine vs Fraudulent Transactions**

Fraud accounts for roughly **2.2%** of all transactions. Although a minority by count, fraud represents a disproportionate share of potential financial loss. Any predictive model must therefore **address class imbalance**, using metrics such as precision, recall and AUC instead of raw accuracy.

---

# 3. Financial Patterns of Fraud

## 3.1 Ticket Size and Fraud

<img width="2100" height="1200" alt="03_amount_by_fraud" src="https://github.com/user-attachments/assets/9971de8b-e04c-4dd0-af3a-2a4da7c0baa1" />

**Figure 3: Transaction Amounts by Fraud Indicator**  
_Comparing ticket sizes for genuine vs fraudulent activity_

Fraudulent transactions display **higher medians and larger upper-quartile values** than genuine ones. This aligns with attacker incentives: targeting medium-to-high-value purchases maximizes return per fraudulent attempt.

<img width="2100" height="1200" alt="04_fraud_by_amount_bucket" src="https://github.com/user-attachments/assets/7ff21770-3f49-418c-bf21-c79976cf418c" />

**Figure 4: Fraud Rate by Amount Bucket**  
_Very high fraud incidence in the >$1k segment_

Bucketed fraud rates reveal:

- **> $1,000:** ≈ **27.3%**  
- **< $50:** ≈ **2.6%**  
- **Other buckets:** roughly **1.1–1.4%**

This supports **tiered fraud controls**: low-value segments receive minimal friction, while high-value tickets require **mandatory step-up authentication**.

---

## 3.2 Shipping Distance and Geographic Risk

<img width="2100" height="1500" alt="09_distance_vs_amount" src="https://github.com/user-attachments/assets/a3a905af-b39a-4a0f-857b-974bbb39f1a4" />

**Figure 5: Fraud Rate by Shipping Distance Bucket**  
_Long-distance shipments show sharply elevated fraud incidence_

Fraud increases dramatically with distance:

- **>2,000 km:** ≈ **22.4%**  
- **500–1,000 km:** ≈ **22.3%**  
- **1,000–2,000 km:** ≈ **8.1%**  
- **0–500 km:** ≈ **0.9–1.0%**

This pattern aligns with reshipping schemes and cross-border mule activity. Distance is therefore a **high-value behavioural risk signal**.

<img width="2700" height="1800" alt="13_fraud_by_shipping_distance" src="https://github.com/user-attachments/assets/b6557371-6d42-49f4-ba0a-24def13c4a54" />

**Figure 6: Shipping Distance vs Transaction Amount by Fraud Status**  
_Long-distance, high-value fraud patterns are strongly visible_

Fraudulent activity clusters in the **upper-right quadrant**—high shipping distances and high amounts—making their interaction essential in fraud modelling.

---

# 4. Channel, Merchant and Country Segmentation

## 4.1 Channel Risk

<img width="1800" height="1200" alt="11_fraud_by_channel" src="https://github.com/user-attachments/assets/ddcfae8d-1cae-4e84-9084-77239ad28554" />

**Figure 7: Fraud Rate by Channel**  
_Web transactions are significantly riskier than app transactions_

Fraud incidence by channel:

- **Web:** ≈ **3.56%**  
- **App:** ≈ **0.80%**

This nearly **4x difference** reflects the higher security provided by app ecosystems (device binding, persistent tokens) versus the greater anonymity of browsers. This supports a **channel-specific risk strategy**.

---

## 4.2 Merchant Category

<img width="1800" height="1200" alt="11_fraud_by_channel" src="https://github.com/user-attachments/assets/432ea768-872e-431f-9198-09c1ac49fb1a" />

**Figure 8: Fraud Rate by Merchant Category**  
_Travel and electronics show the highest fraud incidence_

Fraud incidence by category:

- **Travel ≈ 2.32%**  
- **Electronics ≈ 2.25%**  
- **Grocery lowest ≈ 2.06%**

High resale value (electronics) and intangible goods (travel) make these categories particularly attractive to fraudsters. Stronger category-level controls and risk-adjusted merchant limits are recommended.

---

## 4.3 Country-Level View

<img width="2100" height="1500" alt="10_fraud_by_country" src="https://github.com/user-attachments/assets/9f32b265-a2fb-4108-a631-b00f5bdcdb6e" />

**Figure 9: Fraud Rate by Country (Top 10 by Volume)**  
_Higher fraud incidence in TR, RO, PL, ES and US_

Key insights:

- **Turkey (TR)** highest fraud rate: ≈ **2.8%**  
- **Romania (RO)** and **Poland (PL)** elevated: ≈ **2.4%** & **2.34%**  
- **Germany (DE)** lowest: ≈ **1.67%**

This justifies **geo-segmented rules**, varying thresholds and risk scoring by country.

---

# 5. Control Effectiveness: 3DS, AVS & CVV

## 5.1 3-D Secure (3DS)

<img width="1800" height="1200" alt="07_fraud_by_3ds" src="https://github.com/user-attachments/assets/60d0b7ba-a843-4740-836f-e14dd4ebac93" />

**Figure 10: Fraud Rate by 3DS Flag**  
_Strongly lower fraud for 3DS-authenticated transactions_

- **Without 3DS:** ≈ **6.75%**  
- **With 3DS:** ≈ **0.96%**

3DS dramatically reduces fraud. However, mandatory application to all transactions can degrade user experience. A **risk-based 3DS strategy** is essential.

---

## 5.2 AVS and CVV

<img width="1800" height="1200" alt="08_fraud_avs_cvv_heatmap" src="https://github.com/user-attachments/assets/2968e20a-0447-44b6-b983-01f72d482a52" />

**Figure 11: Fraud Rate by AVS and CVV Outcome**  
_Highest fraud observed when both AVS and CVV fail_

Rates:

- **AVS = 0, CVV = 0:** ≈ **14.6%**  
- **AVS = 0, CVV = 1:** ≈ **4.1%**  
- **AVS = 1, CVV = 0:** ≈ **2.43%**  
- **AVS = 1, CVV = 1:** ≈ **0.68%**

This demonstrates the importance of layered authentication and the limitations of static cardholder credentials alone.

---

# 6. Customer Tenure & Behaviour

<img width="2100" height="1200" alt="12_fraud_by_account_age_decile" src="https://github.com/user-attachments/assets/05bb0b1d-9ea3-48d9-b6fe-f5ba5299e817" />

**Figure 12: Fraud Rate by Account Age Decile**  
_Youngest accounts show dramatically elevated fraud_

- **Decile 1:** ≈ **11.3%**  
- **Deciles 2–10:** ≈ **1.1–1.24%**

Behavioural summaries show:

- Genuine accounts: **≈ 983 days old**  
- Fraudulent accounts: **≈ 553 days old**

This supports stronger **early-tenure controls**, including KYC reinforcement, progressive trust scoring and velocity limits.

---

# 7. Logistic Regression & Predictive Factors

The logistic regression model includes:  
**amount, account age, total transactions, average user spend, shipping distance, country match, channel, merchant category**.

Key findings:

- **Amount:** strong positive effect — higher amount → higher fraud likelihood  
- **Account age:** negative — older accounts less risky  
- **Avg user amount:** negative — fraud often deviates from normal behaviour  
- **Shipping distance:** strong positive  
- **Country match flag:** odds ratio ~ **3.78**  
- **Channel (web):** odds ratio ~ **4.37**

Model fit:

- Null deviance: ~63,512  
- Residual deviance: ~41,414  
- Indicates strong predictive capability  

This is a robust baseline model suitable for building a **production-grade fraud risk engine**.

---

# 8. Conclusions & Recommendations

This analysis demonstrates mastery of both financial and behavioural aspects of fraud risk. Key takeaways:

### **1. Fraud is economically concentrated.**  
High-value and long-distance transactions drive disproportionate risk. Controls should prioritize these segments.

### **2. Behavioural/contextual features are essential.**  
Traditional static checks (AVS/CVV) cannot detect sophisticated fraud. Behavioural modelling is critical.

### **3. Controls must be risk-based, not universal.**  
3DS, AVS and CVV work well, but should be applied based on **customer history**, **transaction value**, **country risk** and **behavioural deviations**.

### **4. New accounts are structurally vulnerable.**  
The youngest decile is nearly **10x riskier**. Strengthening onboarding and early-tenure controls can prevent large fraud losses.

### **5. Channel and geography require differentiated strategy.**  
Web carries ~4x higher risk than app; certain countries consistently show higher fraud. **Geo-adaptive and channel-specific rules** are required.

---

**End of Report**  
```markdown

---
