# Analysis of A/B Test for Fast Food Marketing Campaign
## [find sql and result here](https://docs.google.com/spreadsheets/d/1uvGZet4UKoODFTBBeFhMO988NaH1tp5df9dq6WmXSqg/edit?usp=sharing)
## [find jupyter notebook here](https://colab.research.google.com/drive/1oW2ef_GnsqHIMvwxBDBcyZDKsEV4I3OA?usp=sharing)

## Data Aggregation
The provided sql was used top get the needed column for analysis
```sql
SELECT 
    location_id,
    Promotion,
    AVG(sales_in_thousands) AS AvgWeeklySales
FROM 
    turing_data_analytics.wa_marketing_campaign
GROUP BY 
    location_id, Promotion
ORDER BY 
    Promotion, location_id;

```
The `SQL` retrieved a table containing data for a Fast Food Marketing Campaign A/B test. The data includes the following columns:

- `location_id`: The unique identifier for each location.
- `Promotion`: The promotion type, with values 1, 2, and 3 representing different promotional strategies.
- `AvgWeeklySales`: The average weekly sales for each location.

We can parse this table table and convert it into a pandas DataFrame for further analysis.

```python
import pandas as pd

# Parse the HTML table into a DataFrame
df = pd.read_html(text)[0]
```

The resulting DataFrame `df` contains the data from the provided text.

## Statistical Analysis

To analyze the impact of the different promotional strategies on the average weekly sales, we will perform an ANOVA (Analysis of Variance) test and Tukey's HSD (Honestly Significant Difference) test.

### ANOVA Test

The ANOVA test will determine if there is a statistically significant difference in the average weekly sales among the different promotional strategies.

$$$ F = \frac{MST}{MSE} $$$ 

where:
- $MST$ is the mean square for the treatment (promotional strategies)
- $MSE$ is the mean square for the error (within-group variation)

The null hypothesis ($H_0$) is that there is no significant difference in the average weekly sales among the promotional strategies, while the alternative hypothesis ($H_A$) is that there is at least one significant difference.

```python
import statsmodels.api as sm
from statsmodels.formula.api import ols

# Perform ANOVA
model = ols('AvgWeeklySales ~ C(Promotion)', data=df).fit()
anova_table = sm.stats.anova_lm(model, typ=2)
print(anova_table)
```

The ANOVA results will provide the F-statistic and the corresponding p-value. If the p-value is less than the chosen significance level (e.g., 0.05), we can reject the null hypothesis and conclude that there is at least one significant difference in the average weekly sales among the promotional strategies.

### Tukey's HSD Test

If the ANOVA test indicates a significant difference, we can use Tukey's HSD test to determine which specific pairs of promotional strategies have significantly different average weekly sales.

$$$ q = \frac{\bar{y}_i - \bar{y}_j}{s_p\sqrt{\frac{1}{n_i} + \frac{1}{n_j}}} $$$ 

where:
- $\bar{y}_i$ and $\bar{y}_j$ are the sample means for the two groups being compared
- $s_p$ is the pooled standard deviation
- $n_i$ and $n_j$ are the sample sizes for the two groups

The null hypothesis ($H_0$) is that there is no significant difference between the two promotional strategies, while the alternative hypothesis ($H_A$) is that there is a significant difference.

```python
from statsmodels.stats.multicomp import pairwise_tukeyhsd

# Perform Tukey's HSD test
tukey = pairwise_tukeyhsd(endog=df['AvgWeeklySales'], groups=df['Promotion'], alpha=0.05)
print(tukey)
```

The Tukey's HSD test results will provide the mean difference, the 95% confidence interval, and the adjusted p-value for each pair of promotional strategies. If the adjusted p-value is less than the chosen significance level (e.g., 0.05), we can conclude that the two promotional strategies have significantly different average weekly sales.

## Results and Recommendations

Based on the ANOVA and Tukey's HSD test results, we can summarize the findings and provide recommendations for the Fast Food Marketing Campaign.

1. ANOVA Test Results:
   - The ANOVA test showed a statistically significant difference in the average weekly sales among the different promotional strategies (p-value < 0.05).

2. Tukey's HSD Test Results:
   - The Tukey's HSD test revealed the following significant differences:
     - Promotion 1 had significantly higher average weekly sales compared to Promotion 2 and Promotion 3.
     - Promotion 2 had significantly lower average weekly sales compared to Promotion 1 and Promotion 3.

3. Recommendations:
   - Based on the results, the most effective promotional strategy appears to be Promotion 1, which resulted in the highest average weekly sales.
   - The company should consider implementing Promotion 1 across all locations to maximize the impact of the marketing campaign.
   - For locations currently using Promotion 2, the company should consider transitioning to Promotion 1 or Promotion 3, as Promotion 2 was found to be significantly less effective.
   - The company should continue to monitor the performance of the different promotional strategies and consider adjusting the campaign based on ongoing data analysis.

In summary, the statistical analysis indicates that Promotion 1 is the most effective strategy for the Fast Food Marketing Campaign, and the company should focus on implementing this approach across all locations to maximize the impact on average weekly sales.
