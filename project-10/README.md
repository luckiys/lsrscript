# Project 10: Advanced Plotting with R and ggplot2

**Author:** Lakulish Saini

## Overview

This project explores data visualization techniques using R and ggplot2, analyzing baby name trends and housing market data.

## Datasets

### Baby Names (SSA)
- `/anvil/projects/tdm/data/ssa/yob2006.txt`
- `/anvil/projects/tdm/data/ssa/yob1997.txt`

The United States Social Security Administration (SSA) has kept records tracking the popularity of each name given to a newborn throughout each year starting in 1880.

**Columns:**
- `Name`: the name entered in the database
- `Sex`: using 'F' or 'M' to track the sex assigned to the newborn
- `Counts`: How many times this name-and-sex pairing occurred within the set year

### Zillow Housing Data
- `/anvil/projects/tdm/data/zillow/State_time_series.csv`

Zillow is a real estate marketplace company for discovering real estate, apartments, mortgages, and home values.

**Columns:**
- `Date`: date of entry in YYYY-MM-DD format
- `RegionName`: All 50 states + the District of Columbia + "United States"
- `DaysOnZillow_AllHomes`: median days on market of homes sold within a given month
- `MedianListingPrice_AllHomes`: median of the list price / asking price for all homes
- `PctOfHomesDecreasingInValues_AllHomes`: percentage of homes decreasing in values
- `PctOfHomesIncreasingInValues_AllHomes`: percentage of homes increasing in values

## Instructions

### Setup
Load the required libraries:
```r
library(dplyr)
library(ggplot2)
```

### Task 1: Introduction to ggplot2

ggplot2 is an R package that focuses on data visualization. This package is part of the Tidyverse collection of R packages.

**The philosophy of ggplot:**
- Start with `ggplot()` and use `+` to add components to the plot
- Supply a dataset (mandatory)
- Supply aesthetic mapping (mandatory)
- Add on layers (such as `geom_point()`)
- Add on scales (such as `scale_colour_brewer()`)
- Specify the facet (such as `facet_wrap()`)
- Specify coordinate systems (such as `coord_flip()`)

**Reading the data:**
```r
myDF <- read.table("/anvil/projects/tdm/data/ssa/yob2006.txt", sep = ",", header = FALSE)
the_1997 <- read.table("/anvil/projects/tdm/data/ssa/yob1997.txt", sep = ",", header = FALSE)

colnames(myDF) <- c("Name", "Sex", "Counts")
colnames(the_1997) <- c("Name", "Sex", "Counts")
```

**Tasks:**
1. Subset the data to find entries for 'Alan', 'Eric', and 'Avery'
2. Analyze how the popularity changed between 2006 and 1997
3. Create barplots (base R and ggplot2) of the top 20 names in each year
4. Customize plots with title, axis labels, and customizations

### Task 2: Letter Distribution Analysis

Extract first letters from names using `mutate()` and `substr()`:
```r
df_grouped <- myDF %>%
  mutate(first_letter = substr(Name, 1, 1)) %>%
  group_by(first_letter) %>%
  summarise(total_count = sum(Counts))
```

**Tasks:**
1. Create a barplot of counts per first letter with `geom_col()` or `geom_bar()`
2. Create a histogram using `geom_histogram()` to show distribution of counts

### Task 3: Gender-Based Analysis

Group data by both first letter AND sex:
```r
df_grouped_again <- myDF %>%
  mutate(first_letter = substr(Name, 1, 1)) %>%
  group_by(first_letter, Sex) %>%
  summarise(total_count = sum(Counts), .groups = 'drop')
```

**Tasks:**
1. Create a barplot showing distribution by first letter, colored by sex
2. Use `facet_wrap()` to create subplots
3. Use `scale_fill_manual()` to highlight specific letters

### Task 4: Housing Market Scatterplots

Read and clean Zillow data:
```r
zillow = read.csv("/anvil/projects/tdm/data/zillow/State_time_series.csv")

zillow_cleaned <- zillow %>%
  filter(!is.na(DaysOnZillow_AllHomes),
         !is.na(MedianListingPrice_AllHomes))
```

**Tasks:**
1. Create a scatterplot using `geom_point()` with DaysOnZillow vs MedianListingPrice
2. Use color gradients to represent percentage of homes changing in value
3. Add trend lines using `geom_smooth()`

### Task 5: Time Series Visualization

Convert Date column and create time series:
```r
zillow_cleaned$Date <- as.Date(zillow_cleaned$Date)

selected_regions <- c("Indiana", "Tennessee", "Utah", "NewHampshire")

zillow_grouped_small <- zillow_cleaned %>%
  filter(RegionName %in% selected_regions) %>%
  group_by(Date, RegionName) %>%
  summarise(avg_price = mean(MedianListingPrice_AllHomes), .groups = "drop")
```

**Tasks:**
1. Create a line plot with `geom_line()` showing average prices over time
2. Add additional regions and compare trends

