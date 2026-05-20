# food-access-meal-cost
This project investigates whether U.S. counties with lower food access have higher average meal costs, a question relevant to food equity and the economic burdens faced by underserved communities.

## Repository Structure
```
food-access-meal-cost/
│
├── data/          # Raw data files — not committed, see data/README.md for download instructions
├── analysis.ipynb # Main analysis notebook with narrative
├── README.md      # Overview of question, data, and findings
└── results/       # Figures and output plots
```

The following files:
1. **Food Environment Atlas** (Excel)
   - Source: https://www.ers.usda.gov/data-products/food-environment-atlas/data-access-and-documentation-downloads

2. **Map the Meal Gap** (Excel)
   - Source: https://www.feedingamerica.org/research/map-the-meal-gap/by-county


# Food Insecurity and Associated Prices

## 1. Research Question
Has the trend of counties with low food accessibility having higher grocery costs still continuing or has something changed? Food deserts are usually assocated with povety and limited retail competition for better pricing - this small project is focusing on whether or not limited food access continues to equal higher meal costs at a county level in the United States.

## 2. Hypothesis
Null: There is no difference in MEDIAN meal costs between low-access and adequate-access counties, any observed difference is by chance.

## 3. Data Description:
- Source 1: Feeding America Map the Meal Gap (2025 Report, 2023 Data)
   - URL: https://www.feedingamerica.org/research/map-the-meal-gap
   - Unit of analysis: 1 U.S. county
   - Observations: 3144 counties filtered with the 2023 data
   - Key variables: 'Cost per Meal', 'Overall Food Insecurity Rate'
   - Filtering: Year as 2023, one county was dropped due to missing food insecurity rate in that column

- Source 2: USDA Food Environment Atlas (2025) / Edit: latest data only had 2019 listings
   - URL: https://www.ers.usda.gov/data-productions/food-environment-atlas
   - Unit of analysis: 1 U.S. county
   - Observation: 3146 counties
   - Key variables: 'PCT_LACCESS_POP19' (% total population w/ low access), and 'PCT_LACCESS_LOWI19' 
      (% low-income population w/ low access)
   - Filtering: Rows coded at -9999 dropped, title row skipped on import

- Merging: Both datasets joined on FIPS county code (inner join)
- Final dataset: 3132 counties w/ food access indicators and meal cost data
- Low access group: Counties in top quartile of PCT_LACCESS_POP19 (n = 784)
- Adequate access group: Counties BELOW the top quartile (n = 2348)

## 4. Methods
- Permutation Test:
   - Test statistic: Difference in median meal cost between low-access and adequate-access counteis
   - Null simulation: Shuffling group labels 10,000 ties across all counties
   - P-value: Proportion of permuated differences >= observed difference
   - Random seed set to 50
- Bootstrap Confidence Intervals:
   - Metric 1: Median meal cost difference between groups, each group resampled independently w/ replacement at 10,000 times.
   - Metric 2: Correlation between PCT_LACCESS_POP19 and meal cost, full dataset resampled with replacement at 10,000 times.

- CLT didn't apply here as the median is not a sum or mean of the independent random variables, not guaranteeing its sampling distribution as normal. Also, the correlation aspect is not a mean or proportion as well.

## 5. Results
- Overall median meal cost: $3.52
- Median meal costs by Low Access Counties: $3.57
- Median meal costs by Adequate Access Counties: $3.50
- Observed median difference: $0.07
- Permuation test P-value: 0.0000
- Bootstrap 95% CI by median difference: ($0.045, $0.095)
- Bootstrap 95% CI by correlation: (0.0052, 0.0823)

- Findings:
   - Low-access counties do have a statistically significant higher median meal cost compared to adequate-access counties
   - The P-value of 0.0000 indicates that no shuffle produced a median difference as large as the $0.07 amount by chance alone
   - Bootstrap CI for median difference excludes 0, and the correlation CI also excludes 0. The correlation itself pointing towards a small, positive relationship between low access rate and meal costs
   - The $0.07 difference represents around $1.47 per person per month, a modest but still meaningful amount for very food-insecure households

- Visualizations (jupyter notebook):
   - Histogram and boxpot of meal costs by group
   - Scatter plots of access rate vs meal cost
   - Null distribution w/ observed statistic
   - Bootstrap distribution for both metrics

## 6. Uncertainy Estimation
- 10,000 bootstrap resamples and permuation samples were used throughout the testing
- Bootstrap distribution of median difference was centered around $0.07, consistent with observed statstic.
- Correlation boostrap distribution was slightly right-skewed adn centered near 0.04, a smaller but consistent positive relationship.
- CI of (0.0052, 0.0823) barley excludes 0, that correlation is real but weak. Food access rate is only a small portion of meal cost variation across the given counties.
- Permutation null distribution was centered at 0, observed difference of $0.07 fell outside the null distribution.

## 7. Limitations
- Feed America Meal Cost figures are modeled estimates form NielsenIQ scanner data, not directly observed priceds that may not consider informal food markets or food bank numbers
- Food access data (2019) and meal cost data (2023) are from different years, the changes are not captured regarding access between these years
- Results could differ under alternate thresholds placed
- Asscoiation between food access and meal cost does not state the case that low access automatically causes higher pricing
- Higher cost counties (e.g. San Francisco and other ubran areas) appear as outliers for the adequate access gropu, compressing observed difference.

## 8. References
- USDA Economic Research Service. *Food Environment Atlas (2025).* 
https://www.ers.usda.gov/data-products/food-environment-atlas
- Feeding America. *Map the Meal Gap 2025 (2023 data).* 
https://www.feedingamerica.org/research/map-the-meal-gap
- McKinney, W. (2010). pandas: a Python data analysis library. 
https://pandas.pydata.org
- Hunter, J.D. (2007). Matplotlib: A 2D graphics environment. 
https://matplotlib.org
- Waskom, M. (2021). seaborn: statistical data visualization. 
https://seaborn.pydata.org
- Harris, C.R. et al. (2020). NumPy. https://numpy.org
- SciPy Contributors. scipy.stats. https://scipy.org



