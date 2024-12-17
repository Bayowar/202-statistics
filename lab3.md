lab3
================
Bayowa
2024-11-17

# Introduction:

This lab investigates the relationship between immigration status and
health insurance coverage among immigrants in the U.S., using a
multivariate analysis with three variables: nativity status, income, and
health insurance coverage.

# 1.1: Dataset

For this analysis, i decided to use the American Community Survey (ACS)
2021 5-year estimates,a stratified sampling database which provides
detailed demographic, economic, and health-related data at the state
level.

``` r
# Load necessary libraries
library(tidycensus)
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.5.1     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(dplyr)


api_key <- Sys.getenv("MY_API_KEY")
# Set API key 
census_api_key(api_key, overwrite = TRUE)
```

    ## To install your API key for use in future sessions, run this function with `install = TRUE`.

``` r
# Retrieve ACS data for selected variables
acs_data <- get_acs(
  geography = "state",
  variables = c(
    foreign_born = "B05006_001", "B05006_003",  # Foreign-born population
    median_income = "B19013_001", # Median household income
    uninsured_rate = "B27010_017" # Population without health insurance
  ),
  year = 2021,
  survey = "acs5"
)
```

    ## Getting data from the 2017-2021 5-year ACS

``` r
head(acs_data)
```

    ## # A tibble: 6 × 5
    ##   GEOID NAME    variable         estimate   moe
    ##   <chr> <chr>   <chr>               <dbl> <dbl>
    ## 1 01    Alabama "foreign_born"     173429  3378
    ## 2 01    Alabama ""                   5080   602
    ## 3 01    Alabama "median_income"     54943   377
    ## 4 01    Alabama "uninsured_rate"    40628  2325
    ## 5 02    Alaska  "foreign_born"      57925  1950
    ## 6 02    Alaska  ""                   1435   268

# 1.2: Variables analyzed in the dataset

## The analysis focuses on the following variables:

Dependent Variable: uninsured_rate which is Health Insurance Coverage
showing the proportion of individuals with or without health insurance
and is the Independent Variables: foreign_born which shows immigration
data of the population that is foreign-born. Median_Income which shows
Median household income.

# 1.3: Research question

Hypothesis: immigration has some negative or positive relationship with
health outcomes of immigrants in the U.S

Research question: How does immigration status and income levels
influence health insurance coverage among immigrants in the United
States?

# 1.4: Data analysis plan

Download ACS data for 2021 and 2022 using the tidycensus package.Remove
missing or incomplete data and ensure all variables are properly
formatted.Visualize and summarize the distribution of each variable.
Conduct correlation analysis to explore relationships between variables.
Run multiple regression models to test how immigration affects health
insurance rates and median income and whether health insurance coverage
mediates the relationship between immigration and median
income.Summarize findings and relate them to my research question.

# 1.5: Potential limitations

The cross-sectional nature of ACS data limits causal inference. Health
insurance as a proxy for health outcomes may not capture other relevant
health dimensions. State-level aggregation of data masks
individual-level data.

# 1.6 Checking and fixing missing values

The ACS dataset has no missing values for selected variables because it
provides state-level aggregated data. However, checks for completeness
and consistency were performed.

``` r
# Load necessary libraries
library(tidycensus)
library(tidyverse)

# Set API key 
census_api_key(api_key, overwrite = TRUE)
```

    ## To install your API key for use in future sessions, run this function with `install = TRUE`.

``` r
# Retrieved ACS data for selected variables
acs_data <- get_acs(
  geography = "state",
  variables = c(
    foreign_born = "B05012_003",  # Foreign-born population
    median_income = "B19013_001", # Median household income
    uninsured_rate = "B27010_017" # Population without health insurance
  ),
  year = 2021,
  survey = "acs5"
)
```

    ## Getting data from the 2017-2021 5-year ACS

``` r
# Reshaped and cleaned the data
acs_clean <- acs_data %>%
  select(NAME, variable, estimate) %>%
  pivot_wider(names_from = variable, values_from = estimate) %>%
  rename(
    state = NAME,
    foreign_born = foreign_born,
    median_income = median_income,
    uninsured_rate = uninsured_rate
  ) %>%
  mutate(
    foreign_born = foreign_born / 100000,  # Convert to percentage
    uninsured_rate = uninsured_rate / 1000 # Adjust scale
  )

# Check the cleaned data
head(acs_clean)
```

    ## # A tibble: 6 × 4
    ##   state      foreign_born median_income uninsured_rate
    ##   <chr>             <dbl>         <dbl>          <dbl>
    ## 1 Alabama           1.73          54943           40.6
    ## 2 Alaska            0.579         80287           16.9
    ## 3 Arizona           9.22          65913          148. 
    ## 4 Arkansas          1.48          52123           38.3
    ## 5 California      105.            84097          317. 
    ## 6 Colorado          5.45          80184           66.3

``` r
# Check for missing values in the cleaned data
missing_summary <- acs_clean %>%
  summarise(
    missing_foreign_born = sum(is.na(foreign_born)),
    missing_median_income = sum(is.na(median_income)),
    missing_uninsured_rate = sum(is.na(uninsured_rate))
  )

# Print the summary of missing values
print(missing_summary)
```

    ## # A tibble: 1 × 3
    ##   missing_foreign_born missing_median_income missing_uninsured_rate
    ##                  <int>                 <int>                  <int>
    ## 1                    1                     0                      0

``` r
# count total missing values
total_missing <- sum(is.na(acs_clean))
cat("Total missing values in the dataset:", total_missing, "\n")
```

    ## Total missing values in the dataset: 1

# 1.7 Cleaning variables

``` r
# Load necessary libraries
library(tidycensus)
library(tidyverse)

# Set API key 
census_api_key(api_key, overwrite = TRUE)
```

    ## To install your API key for use in future sessions, run this function with `install = TRUE`.

``` r
# Retrieved ACS data for selected variables
acs_data <- get_acs(
  geography = "state",
  variables = c(
    foreign_born = "B05012_003",  # Foreign-born population
    median_income = "B19013_001", # Median household income
    uninsured_rate = "B27010_017" # Population without health insurance
  ),
  year = 2021,
  survey = "acs5"
)
```

    ## Getting data from the 2017-2021 5-year ACS

``` r
# Reshaped and cleaned the data
acs_clean <- acs_data %>%
  select(NAME, variable, estimate) %>%
  pivot_wider(names_from = variable, values_from = estimate) %>%
  rename(
    state = NAME,
    foreign_born = foreign_born,
    median_income = median_income,
    uninsured_rate = uninsured_rate
  ) %>%
  mutate(
    foreign_born = foreign_born / 100000,  # Convert to percentage
    uninsured_rate = uninsured_rate / 1000 # Adjust scale
  )

# Check the cleaned data
head(acs_clean)
```

    ## # A tibble: 6 × 4
    ##   state      foreign_born median_income uninsured_rate
    ##   <chr>             <dbl>         <dbl>          <dbl>
    ## 1 Alabama           1.73          54943           40.6
    ## 2 Alaska            0.579         80287           16.9
    ## 3 Arizona           9.22          65913          148. 
    ## 4 Arkansas          1.48          52123           38.3
    ## 5 California      105.            84097          317. 
    ## 6 Colorado          5.45          80184           66.3

# Remove missing values

``` r
# Check for missing values
missing_summary <- acs_clean %>%
  summarise(
    missing_foreign_born = sum(is.na(foreign_born)),
    missing_median_income = sum(is.na(median_income)),
    missing_uninsured_rate = sum(is.na(uninsured_rate))
  )

# Handle missing values if necessary
acs_clean <- acs_clean %>% drop_na()  # Remove rows with missing values (if any)
```

## View if missing values have been removed

``` r
#head(acs_clean)
#summary(acs_clean)
#dim(acs_clean)  # Returns number of rows and columns
colSums(is.na(acs_clean))
```

    ##          state   foreign_born  median_income uninsured_rate 
    ##              0              0              0              0

# 1.8: What relationship exists between the variables

## Scatter plot

``` r
# Summarize data
summary(acs_clean)
```

    ##     state            foreign_born      median_income   uninsured_rate   
    ##  Length:51          Min.   :  0.1966   Min.   :49111   Min.   :  2.094  
    ##  Class :character   1st Qu.:  1.2479   1st Qu.:61410   1st Qu.: 19.722  
    ##  Mode  :character   Median :  2.6474   Median :66644   Median : 45.063  
    ##                     Mean   :  8.7931   Mean   :68872   Mean   : 81.740  
    ##                     3rd Qu.:  8.8405   3rd Qu.:78420   3rd Qu.: 86.519  
    ##                     Max.   :104.5495   Max.   :93547   Max.   :906.070

``` r
# Pairwise scatter plots
pairs(~foreign_born + median_income + uninsured_rate, data = acs_clean,
      main = "Scatter Plot")
```

![](lab3_files/figure-gfm/unnamed-chunk-8-1.png)<!-- --> \## Results:
Foreign-Born and Median Income :The relationship appears weakly
positive.Which could indicate that higher percentages of
immigrants/foreign-born populations may have slightly higher median
household incomes. This could suggest economic contributions from
immigrant populations. Foreign-Born and Uninsured Rate: The relationship
appears weakly negative. Which could indicate that larger
immigrants/foreign-born populations tend to have slightly lower
uninsured rates. This finding might reflect good health beahaviour,
regional health policies or health system inclusivity toward immigrants.
Median Income and Uninsured Rate: There is a clear negative
relationship.Which could indicate that people with higher median incomes
tend to have lower uninsured rates. This aligns with expectations that
wealthier communities have better access to health insurance hence
better health outcomes.

## Correlation

``` r
# Correlation matrix
cor_matrix <- cor(acs_clean %>% select(foreign_born, median_income, uninsured_rate))
cor_matrix
```

    ##                foreign_born median_income uninsured_rate
    ## foreign_born      1.0000000    0.24547451     0.64974567
    ## median_income     0.2454745    1.00000000    -0.03214869
    ## uninsured_rate    0.6497457   -0.03214869     1.00000000

## Results:

Foreign-Born and Median Income with a correlation of 0.245 indicates a
weak positive relationship between the percentage of foreign-born
population and median household income.This indicates a higher
percentage of immigrants/foreign-born populations tend to have slightly
higher median incomes. This suggests that the relationship is not
strong. Foreign-Born and Uninsured Rate with a correlation of 0.650
indicates a moderately strong positive relationship between the
percentage of immigrant population and the uninsured rate.This indicates
that higher percentages of immigrants/foreign-born populations are more
likely to have higher uninsured rates. This could highlight potential
disparities in access to health insurance among immigrant populations
and is in contrast with my inference from my scatter plot Median Income
and Uninsured Rate with a correlation of -0.032 indicates a very weak
negative relationship between median income and the uninsured rate which
indicates there is almost no linear relationship between median
household income and uninsured rates. This result could reflect
confounding factors e.g., state-specific health policies or income
inequality.

# Regression Models:

## Impact of Immigration on Health Insurance

``` r
model1 <- lm(uninsured_rate ~ foreign_born, data = acs_clean)
summary(model1)
```

    ## 
    ## Call:
    ## lm(formula = uninsured_rate ~ foreign_born, data = acs_clean)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -248.75  -30.38  -16.43    8.53  620.69 
    ## 
    ## Coefficients:
    ##              Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   37.2505    16.4557   2.264   0.0281 *  
    ## foreign_born   5.0596     0.8456   5.983 2.48e-07 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 104.8 on 49 degrees of freedom
    ## Multiple R-squared:  0.4222, Adjusted R-squared:  0.4104 
    ## F-statistic:  35.8 on 1 and 49 DF,  p-value: 2.483e-07

Results: Impact of immigrant population shows a 1% increase in the
foreign-born population is associated with a 5.06 percentage point
increase in the uninsured rate which may suggest a strong positive
relationship between immigration and lack of health insurance
coverage.Both the overall model and the immigrant/foreign-born predictor
variable are statistically significant, indicating the relationship is
unlikely due to chance.

## Impact of immigration and median income on health insurance

``` r
# Linear regression model
model2 <- lm(uninsured_rate ~ foreign_born + median_income, data = acs_clean)

# Summary of the model
summary(model2)
```

    ## 
    ## Call:
    ## lm(formula = uninsured_rate ~ foreign_born + median_income, data = acs_clean)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -249.13  -30.42  -14.06   21.87  601.24 
    ## 
    ## Coefficients:
    ##                 Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   200.994539  89.206134   2.253   0.0289 *  
    ## foreign_born    5.449397   0.851018   6.403 6.05e-08 ***
    ## median_income  -0.002427   0.001301  -1.866   0.0682 .  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 102.3 on 48 degrees of freedom
    ## Multiple R-squared:  0.4613, Adjusted R-squared:  0.4388 
    ## F-statistic: 20.55 on 2 and 48 DF,  p-value: 3.575e-07

## Results:

The residuals range from -249.13 to 601.24, showing model’s prediction
variability.The foreign-born population remains a strong predictor of
uninsured rates which reinforces earlier findings of systemic
disparities in health insurance coverage among immigrant populations in
my correlation test. The immigrant/foreign-born population remains a
strong predictor of uninsured rates which reinforces earlier findings of
systemic disparities in health insurance coverage in correlation testing
among immigrant populations. Median income has some negative effect on
uninsured rates. Which indicates that the wealthy folk tend to have
slightly lower uninsured rates, but the effect is not as statistically
strong. The addition of median income to the model slightly improves its
explanatory power with R-squared increasing from 0.4222 to 0.4613.
However, foreign-born population remains the major predictor.

# 1.9: How these findings relate to the research question and theory

These findings support the hypothesis that immigration status and income
have a strong relationship with health insurance coverage. The results
align with theories on social determinants of health, which suggest
systemic barriers in healthcare access for marginalized groups,
including immigrants. It demonstrates a strong positive relationship
between the immigrant/foreign-born population and the uninsured rate.
For every 1% increase in the foreign-born population, the uninsured rate
increases by 5.45 percentage points, even with income included in the
analysis.This suggests that immigration status could be have a strong
relationship with health insurance coverage. Median income shows a weak
negative relationship with the uninsured rate with a p-vaklue of 0.0682
which is not statistically significant.This weak effect infers that
income alone does not fully explain disparities in health insurance
coverage among immigrant populations. A positive association between
immigrant/foreign-born population and uninsured rate suggests a negative
impact of immigration on health insurance coverage. This could mean that
immigrants are more likely to be uninsured, likely due to systemic
barriers such as lack of access to employer-sponsored insurance, health
insurance eligibility restrictions, or economic challenges. While median
income weakly influences uninsured rates, the positive relationship
between immigration and income in earlier analysis may suggest that
immigrants contribute economically. However, this economic contribution
does not necessarily translate into better health insurance coverage or
better health outcomes.

# 1.10: Limitations from this data analysis

My analysis lacks broader data due to its cross-sectional nature. The
state-level data may not reflect individual experiences. Omitted
variables and factors like education and employment are not included but
may influence health insurance coverage.
