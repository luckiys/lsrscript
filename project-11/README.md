# Project 11: Advanced Plotting with R and ggplot2 - Part 2

**Author:** Lakulish Saini

## Overview

This project continues exploring data visualization techniques with R and ggplot2, focusing on boxplots, supply vs demand analysis, flight delays, and good vs bad plot design.

## Datasets

### Zillow State Time Series
- `/anvil/projects/tdm/data/zillow/State_time_series.csv`

**Columns:**
- `RegionName`: All 50 states + the District of Columbia + "United States"
- `DaysOnZillow_AllHomes`: median days on market of homes sold within a given month
- `InventoryRaw_AllHomes`: median of weekly snapshot of for-sale homes within a region

### Zillow Metro Time Series
- `/anvil/projects/tdm/data/zillow/Metro_time_series.csv`

**Columns:**
- `Date`: date of entry in YYYY-MM-DD format
- `AgeOfInventory`: median number of days all active listings have been current
- `DaysOnZillow_AllHomes`: median days on market of homes sold
- `MedianListingPrice_AllHomes`: median of the list price / asking price

### Flights Dataset
- `/anvil/projects/tdm/data/flights/subset/1997.csv`

The flights dataset contains 5,411,843 rows from 1997 with information about flight times, delays, and more.

**Columns:**
- `Month`: numeric month values
- `AirTime`: flight time, in minutes
- `ArrDelay`: flight arrival delay time in minutes
- `DepDelay`: flight departure delay time in minutes
- `Origin`: abbreviation values for origin airport

## Instructions

### Setup
Load the required libraries:
```r
library(dplyr)
library(ggplot2)
library(tidyr)
library(patchwork)
```

### Task 1: Boxplots by Region

Read Zillow State data and filter for non-NA values:
```r
myDF <- read.csv('/anvil/projects/tdm/data/zillow/State_time_series.csv')
```

**U.S. Census Bureau Regions:**
```r
the_northeast <- c('Connecticut', 'Maine', 'Massachusetts', 'NewHampshire', 'NewJersey', 'NewYork', 'Pennsylvania', 'RhodeIsland', 'Vermont')
the_midwest <- c('Illinois', 'Indiana', 'Iowa', 'Kansas', 'Michigan', 'Minnesota', 'Missouri', 'Nebraska', 'NorthDakota', 'Ohio', 'Wisconsin')
the_south <- c('Alabama', 'Arkansas', 'Delaware', 'DistrictofColumbia', 'Florida', 'Georgia', 'Kentucky', 'Louisiana', 'Maryland', 'Mississippi', 'NorthCarolina', 'Oklahoma', 'SouthCarolina', 'Tennessee', 'Texas', 'Virginia', 'WestVirginia')
the_west <- c('Alaska', 'Arizona', 'California', 'Colorado', 'Hawaii', 'Idaho', 'Montana', 'Nevada', 'NewMexico', 'Oregon', 'Utah', 'Washington', 'Wyoming')
```

**Tasks:**
1. Create boxplots showing how long listings stay on Zillow by state
2. Group data by Census region and create boxplots by region
3. Adjust plot size with `options(repr.plot.width = 10, repr.plot.height = 8)`

### Task 2: Supply vs Demand Analysis

**Key columns:**
- `DaysOnZillow_AllHomes`: Focus on selling speed (demand)
- `InventoryRaw_AllHomes`: Focus on supply level

**Tasks:**
1. Create scatterplot with `geom_point()` showing supply vs demand by region
2. Use the `patchwork` library to display multiple plots side by side
3. Create overlaid scatterplots comparing specific regions

### Task 3: Flight Delay Analysis

Read and clean flights data:
```r
myDF <- read.csv('/anvil/projects/tdm/data/flights/subset/1997.csv')

myDF_clean <- myDF %>%
  filter(!is.na(DepDelay) & !is.na(ArrDelay))
```

Reshape data using `pivot_longer()`:
```r
summaryDF <- df %>%
    select(Month, DepDelay, ArrDelay) %>%
    pivot_longer(cols = c(DepDelay, ArrDelay),
                 names_to = "DelayType",
                 values_to = "DelayTime") %>%
    group_by(Month, DelayType) %>%
    summarise(mean = mean(value),
        high = mean(value) + sd(value),
        low  = mean(value) - sd(value))
```

**Tasks:**
1. Plot mean value and standard deviation of delays by month
2. Use `geom_ribbon()` to visualize variance

### Task 4: Airport-Specific Delay Analysis

Filter for specific airports (e.g., Phoenix Sky Harbor - PHX):
```r
phx_flights <- flights %>%
  filter(Origin == "PHX") %>%
  select(Month, DepDelay, ArrDelay)
```

**Tasks:**
1. Create line-and-ribbon plots for specific airports
2. Use patchwork to arrange multiple airport plots together

### Task 5: Good vs Bad Plots

**Good plots should be:**
- Clear and accurate
- Easy to interpret
- Properly labeled
- Using appropriate chart types for the data

**Example good plot:**
```r
ggplot(myDF_clean, aes(x = AgeOfInventory, y = DaysOnZillow_AllHomes)) +
  geom_point(alpha = 0.4, color = "#559c4b") +
  geom_smooth(color = "#58135c") +
  labs(title = "Inventory Age vs Days Listed on Zillow",
       x = "Age of Inventory (days)",
       y = "Days on Zillow (All Homes)") +
  theme_minimal()
```

**Tasks:**
1. Compare example plots and identify good vs bad practices
2. Analyze what makes visualizations effective
3. Create intentionally bad plots to understand design principles

