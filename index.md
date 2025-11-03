# 📊 Data Exploration with R: Airbnb Listings in New York City (2019)

> **Powered by [DataCamp](https://www.datacamp.com/)**  
> Category: `Business Analytics` · `Data Exploration` · `Security-Conscious Data Handling`

---

## 🧭 Project Overview

Since 2008, Airbnb has transformed how travelers experience cities worldwide.  
This project explores **Airbnb listing activity and pricing data for New York City (2019)** using R’s **tidyverse** ecosystem.  
The goal is to demonstrate **data exploration, wrangling, and visualization** skills while uncovering insights about NYC’s most active neighborhoods and price distributions.

---

## 🧰 Tools and Libraries

- **Language:** R  
- **Key Packages:**  
  - `tidyverse` (for data manipulation and visualization)  
  - `readr` (for CSV import)  
  - `dplyr` (for summarization and grouping)  
  - `forcats` (for categorical reordering)  
  - `ggplot2` (for creating visuals)

---

## 📥 Data Source

Dataset: [NYC Airbnb Listings 2019](http://insideairbnb.com/)  
> *Public dataset hosted by Inside Airbnb — featuring over 48,000 listings, including host IDs, neighborhoods, room types, and nightly prices.*

File used:  
data/AB_NYC_2019.csv
---

## 📂 Step 1: Load the Data

```r
suppressPackageStartupMessages(library(tidyverse))

# Read the dataset
listings <- read_csv('data/AB_NYC_2019.csv', show_col_types = FALSE)

# Quick check
nrow(listings)
head(listings, n = 10)
