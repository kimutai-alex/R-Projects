---
title: "Kimutai Alex | R Analytics Portfolio"
description: "Data analytics, statistical modeling, and visualization in R"
---

# 👋 Hi, I’m Kimutai Alex

I build **data-driven solutions** that blend rigorous statistics, crisp visualizations, and clean, reproducible R code. This portfolio highlights selected work in **EDA**, **regression**, **ANOVA**, **report automation**, and **dashboard-quality plots**—with an emphasis on tidy, production-ready scripts.

---

## 🔧 Tech Stack
- **R** (tidyverse, ggplot2, dplyr, tidyr, readr, readxl, purrr, forcats)
- **Modeling**: broom, stats, car, AIC/BIC workflows
- **Reporting**: knitr, rmarkdown, quarto, gt
- **Data hygiene**: janitor, skimr
- **Reproducibility**: renv, here
- **Tooling**: Git/GitHub, GitHub Pages

---

## 📌 Featured Project — Climate Analytics (R)
**Goal:** Build a compact analytical pipeline from raw Excel to insights: **descriptives, regression, ANOVA, and five plots**.

**Dataset (example path):** `C:/Users/akimutai/Downloads/climatedata.xlsx`

**Highlights:**
- E2E script: load → clean → summarize → model → visualize
- Multiple linear regression with diagnostic summaries
- One-way ANOVA with Tukey post-hoc and eta-squared
- 5 export-ready visualizations for reports/dashboards

### Core Script
```r
# Packages (install once if needed)
# install.packages(c("readxl","tidyverse","janitor","broom","skimr"))
library(readxl); library(tidyverse); library(janitor); library(broom); library(skimr)

xlsx_path <- "C:/Users/akimutai/Downloads/climatedata.xlsx"
raw <- read_excel(xlsx_path)
climate <- raw |>
  clean_names() |>
  mutate(
    year = as.integer(year),
    country = as.factor(country)
  )

# Descriptives
overall_desc <- climate |>
  select(where(is.numeric)) |>
  summarize(across(everything(), list(
    n = ~sum(!is.na(.)), mean = ~mean(., na.rm=TRUE), sd = ~sd(., na.rm=TRUE),
    min = ~min(., na.rm=TRUE), q25 = ~quantile(., 0.25, na.rm=TRUE),
    median = ~median(., na.rm=TRUE), q75 = ~quantile(., 0.75, na.rm=TRUE),
    max = ~max(., na.rm=TRUE)
  ), .names = "{.col}_{.fn}"))

by_country_desc <- climate |>
  group_by(country) |>
  summarize(
    n = n(),
    mean_temp = mean(avg_temperature_c, na.rm=TRUE),
    sd_temp = sd(avg_temperature_c, na.rm=TRUE),
    mean_co2 = mean(co2_emissions_tons_capita, na.rm=TRUE),
    mean_rain = mean(rainfall_mm, na.rm=TRUE),
    mean_sea = mean(sea_level_rise_mm, na.rm=TRUE),
    mean_renew = mean(renewable_energy_percent, na.rm=TRUE),
    mean_forest = mean(forest_area_percent, na.rm=TRUE),
    .groups = "drop"
  )

# Regression
model <- lm(avg_temperature_c ~ co2_emissions_tons_capita + rainfall_mm +
              renewable_energy_percent + sea_level_rise_mm + forest_area_percent +
              year + country, data = climate)
model_summary <- summary(model)
model_tidy <- tidy(model, conf.int = TRUE)
model_glance <- glance(model)

# ANOVA
anova_fit <- aov(avg_temperature_c ~ country, data = climate)
eta_sq <- {
  ss <- summary(anova_fit)[[1]][, "Sum Sq"]; ss[1] / sum(ss)
}

# Visuals
library(ggplot2); dir.create("figs", showWarnings = FALSE); theme_set(theme_minimal(base_size=12))

p1 <- ggplot(climate, aes(year, sea_level_rise_mm, color = country, group = country)) +
  geom_line(linewidth = 0.9) + geom_point(size = 1.6) +
  facet_wrap(~ country, scales = "free_y") +
  labs(title = "Sea Level Rise Over Time", x = NULL, y = "Sea level rise (mm)") +
  theme(legend.position = "none")

ggsave("figs/01_sea_level_trend.png", p1, width = 10, height = 6, dpi = 150)

p2 <- ggplot(climate, aes(co2_emissions_tons_capita, avg_temperature_c, color = country)) +
  geom_point(size = 2) + geom_smooth(method = "lm", se = TRUE) +
  labs(title = "CO₂ vs Avg Temperature", x = "CO₂ (tons per capita)", y = "Avg Temp (°C)")

ggsave("figs/02_co2_vs_temp.png", p2, width = 8, height = 6, dpi = 150)

p3 <- ggplot(climate, aes(year, avg_temperature_c, color = country)) +
  geom_point(alpha = 0.7) + geom_smooth(method = "loess", se = TRUE) +
  labs(title = "Average Temperature Trend", x = NULL, y = "Avg Temp (°C)")

ggsave("figs/03_temp_trend.png", p3, width = 8, height = 6, dpi = 150)

p4 <- ggplot(climate, aes(rainfall_mm, renewable_energy_percent, color = country)) +
  geom_point(size = 2) + geom_smooth(method = "lm", se = FALSE) +
  labs(title = "Rainfall vs Renewable Energy", x = "Rainfall (mm)", y = "Renewable Energy (%)")

ggsave("figs/04_rainfall_vs_renewables.png", p4, width = 8, height = 6, dpi = 150)

p5 <- ggplot(climate, aes(x = factor(year), y = extreme_weather_events, fill = country)) +
  geom_col(position = "dodge") +
  labs(title = "Extreme Weather Events by Country and Year", x = "Year", y = "Events (count)")

ggsave("figs/05_extreme_events_by_country_year.png", p5, width = 10, height = 6, dpi = 150)
```

### Selected Outputs
- **Model quality:** `r model_glance$r.squared` R², `r model_glance$adj.r.squared` adj. R²
- **ANOVA (country on temp):** eta² = `r round(eta_sq, 3)`
- **Descriptives preview:** `overall_desc |> glimpse()`

> For a full reproducible report, knit this into **HTML/PDF** with **rmarkdown** or **quarto**, embedding `figs/*.png` and tables.

---

{% assign IMG_DIR = '/climate-data/figs' %}  <!-- ← change /climate-data to your real folder -->

## 📊 Story Gallery

### 1) CO₂–Temperature Paths by Country (faceted)
<img width="100%" alt="M1b_co2_temp_connected_paths_faceted_free" src="https://github.com/user-attachments/assets/404b495a-56e4-4b91-9feb-03878df538b0" />


### 2) Sea Level Rise Over Time
<img src="{{ IMG_DIR | append: '/01_sea_level_trend.png' | relative_url }}" alt="Sea level rise over time" width="100%">

### 3) Indexed Change Since Baseline (highlighted country)
<img src="{{ IMG_DIR | append: '/M2_index_story.png' | relative_url }}" alt="Indexed change story" width="100%">

### 4) Temperature Distributions by Country (Ridgeline)
<img src="{{ IMG_DIR | append: '/M3_temp_ridgeline.png' | relative_url }}" alt="Temperature ridgeline" width="100%">

### 5) Eco-Tradeoff Map (Rainfall vs Renewables)
<img src="{{ IMG_DIR | append: '/M4_eco_tradeoff_map.png' | relative_url }}" alt="Eco tradeoff map" width="100%">

### 6) World Map — Renewable Momentum (Δ latest − baseline)
<img src="{{ IMG_DIR | append: '/M5_world_renewables_delta.png' | relative_url }}" alt="Renewables delta world" width="100%">

### 7) Animated — Renewables by Year (GIF)
<img src="{{ IMG_DIR | append: '/world_renewables_yearly.gif' | relative_url }}" alt="Renewables animation" width="100%">

### 8) Decoupling Vectors — Renewables vs Total CO₂
<img src="{{ IMG_DIR | append: '/M7_decoupling_vectors.png' | relative_url }}" alt="Decoupling vectors" width="100%">

---

#### If images still don’t display
- Ensure `figs/` (and `tables/` for CSV) are **inside the same published source** as `index.md` (root or `/docs`).
- File names must **exactly** match (case-sensitive).
- Confirm files are **committed and pushed**, and Pages build is successful in **Settings → Pages**.

---

## 🧱 Reproducibility
- Use **renv** to snapshot package versions:
```r
install.packages("renv"); renv::init(); renv::snapshot()
```
- Use **here** for project-anchored paths:
```r
install.packages("here"); here::here("data","climatedata.xlsx")
```
- Keep raw data in `/data`, scripts in `/R`, outputs in `/figs`.

**Suggested repo structure**
```
.
├─ index.md
├─ _config.yml            # optional: theme: minima / minimal-mistakes / etc.
├─ R/
│  └─ climate_analysis.R
├─ data/
│  └─ climatedata.xlsx
├─ figs/
│  ├─ 01_sea_level_trend.png
│  ├─ 02_co2_vs_temp.png
│  ├─ 03_temp_trend.png
│  ├─ 04_rainfall_vs_renewables.png
│  └─ 05_extreme_events_by_country_year.png
└─ renv.lock             # after snapshot
```

---

## 🚀 How to Publish on GitHub Pages
1. Create a repo named `username.github.io` (or enable Pages on any repo).
2. Add this `index.md` to the repo root.
3. *(Optional)* Add `_config.yml` with a theme:
```yaml
theme: minima
```
4. Commit & push → **Settings ▸ Pages** → Source: `main` (root) → Save.
5. Visit `https://username.github.io/`.

---

## 🧪 Additional Demos
### Quick EDA Helpers
```r
library(skimr); skim(climate)
library(janitor); tabyl(climate$country) |> adorn_pct_formatting(digits = 1)
```

### Correlation Peek (Numeric)
```r
num <- climate |> select(where(is.numeric))
round(cor(num, use = "pairwise.complete.obs"), 2)
```

### Model Diagnostics (base)
```r
par(mfrow = c(2,2)); plot(model); par(mfrow = c(1,1))
```

---

## 🙋 About & Contact
- **Role interests:** Data Analyst, Research Analyst, Analytics Engineer
- **Focus areas:** Statistical modeling, public policy analytics, climate/health, automation
- **Contact:** [Email](mailto:your.email@example.com) · [LinkedIn](https://www.linkedin.com/) · [GitHub](https://github.com/)

> *This portfolio is continuously evolving. Feedback & collaboration are welcome.*
