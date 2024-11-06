# Analysis of A/B Test for Fast Food Marketing Campaign

## Data Source

- Find SQL and results [here](https://docs.google.com/spreadsheets/d/1uvGZet4UKoODFTBBeFhMO988NaH1tp5df9dq6WmXSqg/edit?usp=sharing).
- Find Jupyter Notebook [here](https://colab.research.google.com/drive/1BirtazA5rQhrT_nveezEfH38ksO1Yeaz?usp=sharing).

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

### T-Test
The T-test checks for statistically significant differences in average weekly sales across the different promotional strategies.

### Hypotheses
Null Hypothesis 

​
 : There is no significant difference in average weekly sales among the promotional strategies.
Alternative Hypothesis 

​
 : At least one strategy yields a significantly different average weekly sales.
#### Python Implementation
```python
import scipy.stats as stats

# T-tests between the promotions
t_test_results = {}

# T-test between Promotion 1 and Promotion 2
t_stat_1_2, p_val_1_2 = stats.ttest_ind(df[df['Promotion'] == 1]['AvgWeeklySales'],
                                         df[df['Promotion'] == 2]['AvgWeeklySales'])
t_test_results['Promotion 1 vs. Promotion 2'] = (t_stat_1_2, p_val_1_2)

# T-test between Promotion 1 and Promotion 3
t_stat_1_3, p_val_1_3 = stats.ttest_ind(df[df['Promotion'] == 1]['AvgWeeklySales'],
                                         df[df['Promotion'] == 3]['AvgWeeklySales'])
t_test_results['Promotion 1 vs. Promotion 3'] = (t_stat_1_3, p_val_1_3)

# T-test between Promotion 2 and Promotion 3
t_stat_2_3, p_val_2_3 = stats.ttest_ind(df[df['Promotion'] == 2]['AvgWeeklySales'],
                                         df[df['Promotion'] == 3]['AvgWeeklySales'])
t_test_results['Promotion 2 vs. Promotion 3'] = (t_stat_2_3, p_val_2_3)

for test, result in t_test_results.items():
    print(f"T-Test between {test}:")
    print(f"T-Statistic: {result[0]}")
    print(f"P-Value: {result[1]}")

```

### Results
The T-test results indicated:

Promotion 1 vs. Promotion 2: Significant difference (p-value < 0.05).
Promotion 1 vs. Promotion 3: No significant difference (p-value > 0.05).
Promotion 2 vs. Promotion 3: Marginal difference (p-value < 0.05).

### Observation on Statistical Significance
T-Test: The significant p-value for the comparison between Promotion 1 and Promotion 2 suggests a genuine difference in performance.

### Tukey's HSD Test

Tukey's HSD test identifies specific pairs of promotional strategies that differ significantly in their average weekly sales.

#### Formula
$$$ q = \frac{\bar{y}_i - \bar{y}_j}{s_p \sqrt{\frac{1}{n_i} + \frac{1}{n_j}}} $$$

Where:
- $\bar{y}_i$ and $\bar{y}_j$ are the sample means for the two groups being compared
- $s_p$ is the pooled standard deviation
- $n_i$ and $n_j$ are the sample sizes for the two groups

#### Hypotheses
- **Null Hypothesis \(H_0\)**: There is no significant difference between the two promotional strategies.
- **Alternative Hypothesis \(H_A\)**: There is a significant difference between the strategies.

#### Python Implementation
```python
from statsmodels.stats.multicomp import pairwise_tukeyhsd

# Perform Tukey's HSD test
tukey = pairwise_tukeyhsd(endog=df['AvgWeeklySales'], groups=df['Promotion'], alpha=0.05)
print(tukey)
```

### Results
Tukey's HSD test results revealed:
- **Promotion 1 vs. Promotion 2**: Significant difference (p-adj = 0.004). Promotion 1 had higher average weekly sales.
- **Promotion 1 vs. Promotion 3**: No significant difference (p-adj = 0.6862).
- **Promotion 2 vs. Promotion 3**: Marginal difference, but not convincingly significant (p-adj = 0.0371), with the confidence interval including zero.

### Observation on Statistical Significance
- **T-Test**: The significant p-value of 0.0037 suggests that the variation in average weekly sales among the promotional strategies is unlikely due to random chance.
- **Tukey's HSD Test**: Adjusted p-values indicate that only the difference between Promotion 1 and Promotion 2 is robustly significant (p-adj = 0.004), implying a genuine difference in performance.

## Results and Recommendations

### Summary of Findings
- The T-Test confirms that not all promotional strategies yield the same average weekly sales.
- Tukey's HSD test results indicate:
  - **Promotion 1 vs. Promotion 2**: Promotion 1 is significantly better.
  - **Promotion 1 vs. Promotion 3**: No significant difference.
  - **Promotion 2 vs. Promotion 3**: Only marginal evidence of a difference.

### Recommendations
- **Implement Promotion 1** broadly to maximize sales, as it significantly outperforms Promotion 2.
- **Consider replacing Promotion 2** with either Promotion 1 or 3 to enhance sales outcomes, given that Promotion 2 underperforms.
- Ongoing monitoring and analysis are recommended to fine-tune the promotional strategy based on evolving data.

This concludes the analysis of the A/B test for the fast food marketing campaign. For further details, please refer to the SQL results and Jupyter Notebook linked above.
