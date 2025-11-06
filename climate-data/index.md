# Climate Mini‑Project (R Portfolio)

_A compact, reproducible R portfolio page you can drop into your GitHub Pages as `index.md`._

---

## 🎯 Project goals
- Demonstrate mastery of R using a tidy, reproducible workflow.
- Read a local Excel dataset, clean it, sanity‑check, explore, visualize, and model.
- Save publication‑ready figures and include short, clear narrative.

> **Local data path:** `C:\Users\akimutai\OneDrive - Kenya Development Corporation Limited\Desktop\climatedata.xlsx`

---

## 🔧 Setup
```r
# Install once (uncomment to run)
# install.packages(c("tidyverse", "readxl", "janitor", "lubridate", "scales", "broom"))

library(tidyverse)
library(readxl)
library(janitor)
library(lubridate)
library(scales)
library(broom)

# Create an output folder for figures
if (!dir.exists("figs")) dir.create("figs")
```

---

## 📥 Load data (Excel → R)
```r
# Adjust the path if different on your machine
xlsx_path <- "C:/Users/akimutai/OneDrive - Kenya Development Corporation Limited/Desktop/climatedata.xlsx"

raw <- read_excel(xlsx_path)
raw %>% glimpse()
```

---

## 🧽 Clean & standardize columns
```r
# Clean column names to snake_case and harmonize types
climate <- raw %>%
  clean_names() %>%
  # The sheet uses special characters; rename explicitly for clarity
  rename(
    year                  = year,
    country               = country,
    avg_temperature_c     = avg_temperature_c,
    co2_tons_capita       = co2_emissions_tons_capita,
    sea_level_rise_mm     = sea_level_rise_mm,
    rainfall_mm           = rainfall_mm,
    population            = population,
    renewable_energy_pct  = renewable_energy,
    extreme_weather_events= extreme_weather_events,
    forest_area_pct       = forest_area
  ) %>%
  mutate(
    year = as.integer(year),
    country = as.factor(country),
    across(c(avg_temperature_c, co2_tons_capita, sea_level_rise_mm,
             rainfall_mm, population, renewable_energy_pct,
             extreme_weather_events, forest_area_pct), as.numeric)
  )

# Quick sanity checks
stopifnot(!any(is.na(climate$year)))
stopifnot(all(climate$year >= 1900 & climate$year <= 2100))

summary(climate)
```

> **Note:** If any column shows unexpected `NA`s, confirm the Excel formatting (numeric vs text). You can coerce with `readxl::col_types` or `parse_number()`.

---

## 🔎 Basic QA & tidy helpers
```r
# Duplicates check (same year + country)
dups <- climate %>% count(country, year) %>% filter(n > 1)
dups

# Missingness by column
na_rate <- climate %>% summarise(across(everything(), ~mean(is.na(.))))
na_rate

# A clean factor of countries
countries <- fct_infreq(climate$country)
levels(countries)
```

---

## 📊 Descriptive statistics
```r
# Core numeric summary
num_summary <- climate %>%
  summarise(
    n = n(),
    years_min = min(year, na.rm = TRUE),
    years_max = max(year, na.rm = TRUE),
    temp_mean = mean(avg_temperature_c, na.rm = TRUE),
    co2_mean  = mean(co2_tons_capita, na.rm = TRUE),
    sea_mean  = mean(sea_level_rise_mm, na.rm = TRUE)
  )
num_summary

# Country‑level rollups
by_country <- climate %>%
  group_by(country) %>%
  summarise(
    n = n(),
    temp_avg = mean(avg_temperature_c, na.rm = TRUE),
    co2_avg  = mean(co2_tons_capita, na.rm = TRUE),
    sea_avg  = mean(sea_level_rise_mm, na.rm = TRUE),
    renew_avg= mean(renewable_energy_pct, na.rm = TRUE),
    forest_avg = mean(forest_area_pct, na.rm = TRUE)
  ) %>% arrange(desc(temp_avg))
by_country
```

---

## 📈 Visuals (saved to `/figs`)

### 1) CO₂ vs Temperature (with linear fit)
```r
p1 <- ggplot(climate, aes(co2_tons_capita, avg_temperature_c, color = country)) +
  geom_point(alpha = 0.8) +
  geom_smooth(method = "lm", se = TRUE, linewidth = 0.8, alpha = 0.2, color = "black") +
  labs(
    title = "CO₂ emissions vs Average Temperature",
    x = "CO₂ (tons per capita)", y = "Average Temperature (°C)", color = "Country"
  ) +
  theme_minimal(base_size = 12)

ggsave("figs/co2_vs_temp.png", p1, width = 7, height = 5, dpi = 120)
```

### 2) Sea‑level rise over time (faceted by country)
```r
p2 <- ggplot(climate, aes(year, sea_level_rise_mm, group = country)) +
  geom_line(aes(color = country), linewidth = 0.9) +
  geom_point(aes(color = country), size = 1.6) +
  facet_wrap(~ country, scales = "free_y") +
  labs(title = "Sea‑level rise (mm) over time", x = NULL, y = "Sea level rise (mm)") +
  theme_minimal(base_size = 12) +
  theme(legend.position = "none")

ggsave("figs/sea_level_trend.png", p2, width = 9, height = 6, dpi = 120)
```

### 3) Extreme weather events: top years
```r
top_extreme <- climate %>% slice_max(extreme_weather_events, n = 10)

p3 <- ggplot(top_extreme, aes(reorder(paste(country, year, sep = "-"), extreme_weather_events),
                              extreme_weather_events)) +
  geom_col() +
  coord_flip() +
  labs(title = "Top 10 country‑years by extreme weather events",
       x = "Country‑Year", y = "Events (count)") +
  theme_minimal(base_size = 12)

ggsave("figs/top_extreme.png", p3, width = 7, height = 5, dpi = 120)
```

---

## 📐 Simple models

### Model A: Does higher CO₂ relate to higher temperatures?
```r
m_temp <- lm(avg_temperature_c ~ co2_tons_capita + country, data = climate)
summary(m_temp)

tidy(m_temp) %>% arrange(p.value) %>% head(10)
```

### Model B: Do renewables associate with fewer extreme events?
```r
m_events <- lm(extreme_weather_events ~ renewable_energy_pct + year + country, data = climate)
summary(m_events)

ggplot(augment(m_events), aes(.fitted, .resid)) +
  geom_point(alpha = 0.6) +
  geom_hline(yintercept = 0, linetype = 2) +
  labs(title = "Residuals vs Fitted (Extreme Events Model)", x = "Fitted", y = "Residuals") +
  theme_minimal()

ggsave("figs/model_events_residuals.png", width = 7, height = 5, dpi = 120)
```

> **Caveat:** This is an illustrative, cross‑sectional panel of heterogeneous countries; causal claims aren’t appropriate. Treat results as descriptive.

---

## 🧪 Reproducibility helpers
```r
# Deterministic operations (if sampling later)
set.seed(42)

# Session info for reproducibility
sessionInfo()
```

---

## 🧵 Narrative (what each step shows)
1. **Setup** loads the tidyverse and friends, then ensures a `figs/` directory exists for exported plots.
2. **Load** reads the Excel file with `readxl`, preserving numeric types.
3. **Clean** makes column names machine‑friendly and coerces types; we validate year ranges and summarize.
4. **QA** checks duplicates (country‑year collisions) and missingness.
5. **Descriptives** summarize central tendencies overall and by country.
6. **Visuals** illustrate relationships: CO₂↔Temp, trends in sea‑level, and distribution of extreme events.
7. **Models** run simple linear models to explore associations; we also inspect diagnostics.
8. **Reproducibility** prints R session info.

---

## 📂 Project structure (recommended)
```
climate-portfolio/
├─ index.md            # this file (your GitHub Pages entry)
├─ figs/               # auto‑saved plots
└─ data/               # optional: copy the Excel here (and update path)
```

---

## 🌐 Publish on GitHub Pages
1. Create a new public repo (e.g., `climate-portfolio`).
2. Add this `index.md`, commit, and push.
3. In **Settings → Pages**: Source = `main` (or `master`) / root.
4. Wait a minute; your page will be live at `https://<your-username>.github.io/climate-portfolio/`.

> If images don’t show, ensure you ran the code locally so `figs/*.png` exist and are committed.

---

## ✅ Run checklist
- [ ] `index.md` committed
- [ ] `figs/` contains PNGs from the scripts
- [ ] `sessionInfo()` appended if you want to show R version
- [ ] Repo published via GitHub Pages

---

## 📎 Appendix: Handy patterns
```r
# Robust mean with explicit NA handling
mean(climate$avg_temperature_c, na.rm = TRUE)

# Count distinct working days per country (example of date logic if a date column existed)
# climate %>% mutate(day = as.Date(some_timestamp)) %>% count(country, day) %>% count(country)

# Save a single high‑res plot
# ggsave("figs/my_plot.png", width = 8, height = 5, dpi = 200)
```

---

*© 2025 — R tidy workflow demo.*

