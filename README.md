# Analyzing Voting Patterns and Factors Influencing Consensus at the United Nations General Assembly
## Yunong Xue

### Introduction

In an increasingly interconnected world, understanding the relationships between countries is crucial for making informed decisions and fostering international cooperation. This project examines the voting patterns and factors influencing consensus at the United Nations General Assembly (UNGA). Specifically, it seeks to determine how trade activity, ideological differences, and cultural and historical links between two countries can influence their voting similarities in the UNGA, providing insights into foreign policy preferences and alliances on a global stage.

### Data Source

The analysis is based on three datasets:

- [United Nations General Assembly Voting Database](https://doi.org/10.7910/DVN/LEJUQZ), retrieved from the Harvard Database

  This panel dataset contains information on UNGA voting similarities and ideological distances, spanning from 1981 to 2021 and including paired data for countries worldwide. Key variables in this dataset include "Agree" and "IdealPointDistance".

- [International Trade Data](https://www.kaggle.com/datasets/appetukhov/international-trade-database), retrieved from Kaggle
  
  This panel dataset contains information on export values, covering the period from 1988 to 2021 and including paired data for countries worldwide. The dataset consists of 634,509 rows and includes the key variable "value".

 - [CEPII - GeoDist.xls](http://www.cepii.fr/CEPII/en/bdd_modele/bdd_modele_item.asp?id=6), published by CEPII Research and Expertise on the World Economy
 
   This dataset comprises 50,176 rows and contains binary data on links between each pair of countries, such as colonial relationships, common languages, and contiguity. Important variables include "contig", "comlang_off", "comlang_ethno", "comcol", and "curcol".

### Methodology
#### Data and Variables

The dataset used for modeling combines the three datasets mentioned above. It is formatted longitudinally, incorporating both cross-sectional (multiple units) and time-series (multiple periods) data. The fields from the CEPII dataset are time-invariant and can be used to control for unit effects. The dependent variable in our analysis is 'Agree', which represents the voting similarity index between a pair of countries in the UNGA for a given year.

The independent variables, or features, included in the regression model are:
- ***Agree_Lag1, Agree_Lag2, and Agree_Lag3***: These variables represent the lagged values of the voting similarity, capturing the historical influence of previous periods on the current voting similarity.
- ***Exp_Pct_Avg***: This variable denotes relative trading importance between paired countries, calculated as the average of (Export from A to B / Overall Export from A (%)) and (Export from B to A / Overall Export from B (%)).
- ***IdealPointDistance***: This variable measures the ideological distance between the paired countries.
- ***contig***: This binary variable takes a value of 1 if the countries are contiguous and 0 otherwise.
- ***comlang_off, comlang_ethno***: These variables indicate whether the countries share an official or ethnolinguistic common language (with more than 9% of the population speaking the language), respectively.
- ***comcol***: This binary variable takes a value of 1 if the countries share a colonial history and 0 otherwise.

#### Model Specification

A panel data regression model, specifically the PanelOLS method, is employed to estimate the relationship between the independent variables and the dependent variable. This model accounts for time-fixed effects, controlling for unobserved factors that change over time but remain constant across units. This approach helps to mitigate potential biases in our estimates that may arise from omitted variable issues.

The regression model can be expressed as:
```
Agree_it = β1 * Agree_Lag1_it + β2 * Agree_Lag2_it + β3 * Agree_Lag3_it + β4 * Exp_Pct_Avg_it + β5 * IdealPointDistance_it + β6 * contig_it + β7 * comlang_off_it + β8 * comlang_ethno_it + β9 * comcol_it + λ_t + ε_it
```
Here, Agree_it represents the voting similarity for country pair i at time t, β1 to β9 denote the coefficients of the independent variables, λ_t stands for the time-fixed effects, and ε_it signifies the error term.

### Estimation and Results

The PanelOLS model is fitted to the data using Python's linearmodels package. A clustered covariance estimator is also employed to account for potential heteroskedasticity and autocorrelation within the units, ensuring that our standard errors and hypothesis tests are robust to these issues.

The panel data regression model provides a good fit to the data, explaining 81.73% of the variation in voting similarity through the included variables. The results indicate that contiguity and having a common colonizer post-1945 are positively correlated with voting similarity. In contrast, relative trading importance, ideal point distance, and sharing a common ethnolinguistic language are negatively correlated with voting similarity. All these effects are statistically significant at the 0.1% level. However, sharing an official language does not have a significant impact on voting similarity.

More specifically, a one-percentage-point increase in the average relative trading importance (%) between two countries is associated with a 0.244-point decrease in the voting similarity score, on average. A one-point increase in the ideal point distance between two countries corresponds to a 0.13-point decrease in the voting similarity score, on average. Countries that share a border have a 0.013-point higher voting similarity score than those that do not. Furthermore, countries that share at least one common official language have a 0.003-point higher voting similarity score, while having at least 9% of the population sharing a non-official language can lower the voting similarity score by 0.018 points. Additionally, countries with a common post-1945 colonizer have, on average, a 0.004-point higher voting similarity score compared to those without a shared colonizer.

### Discussion

The negative association between average relative trading importance (%) and voting similarity may seem counter-intuitive, as one might expect countries with stronger trade ties to exhibit more similar voting patterns. This unexpected result could be explained by several factors. One possible explanation is that countries with stronger trade ties may be more sensitive to each other's economic policies and regulations, potentially leading to tensions and disagreements in various policy areas, such as the ongoing trade war between China and the USA. Another possibility is that the negative relationship between trading importance and voting similarity could be driven by confounding variables not captured in the model, resulting in omitted variable bias.

The primary limitation of this analysis is the non-normal distribution of the dependent variable, which is left-skewed and slightly bimodal. This may affect the normality of residuals, a crucial assumption in classical linear regression models, as it ensures that the t-statistics for the coefficients have a t-distribution and the F-statistic for the overall model has an F-distribution. Consequently, violating this assumption could impact the validity of hypothesis tests, p-values, and confidence intervals for the model coefficients. The non-normal distribution of the residuals could also suggest that our linear model may not adequately capture the relationship between the independent and dependent variables, potentially resulting in suboptimal model fit and prediction performance, as well as violations of other regression assumptions such as independence and homoscedasticity of the error terms.

To address this limitation, logarithmic transformation and generalized linear models were attempted. However, the former did not yield better normality, while the latter were not feasible due to limited statistical packages and hardware memory constraints. Therefore, the interpretation of the results should be approached with caution. Further research is needed to explore alternative modeling techniques that can better account for the non-normal distribution of residuals and improve the robustness of the findings. In future research, several strategies could be considered:

- Data transformations: Applying other transformations to the dependent or independent variables, such as square root or inverse transformations, might help stabilize variance and make the residuals more symmetric and closer to normal.
- Nonlinear models: Investigating alternative modeling approaches, such as polynomial regression, generalized additive models (GAMs), or adding interactive terms between predictors, may better capture the relationship between the independent and dependent variables.
- Generalized linear models (GLMs): If the dependent variable's distribution is better represented by a non-normal family of distributions, a generalized linear model (GLM) with a suitable link function and error distribution could provide more accurate and reliable results.
Furthermore, future studies could expand the scope of the analysis by incorporating additional variables related to geopolitical, economic, and cultural factors and examining the potential mediating or moderating effects of these variables on the relationships between UN voting affinity, cultural proximity, and international trade.

### Conclusion

This analysis has provided insights into the factors influencing countries' voting similarities in the United Nations General Assembly by examining trade activity, ideology difference, and cultural and historical links. Using panel data regression models and the PanelOLS method, the analysis accounted for time-fixed effects and revealed several significant relationships.

While the results indicated that contiguity and having a common colonizer post-1945 are positively correlated with voting similarity, relative trading importance, ideal point distance, and sharing a common ethnolinguistic language were found to be negatively correlated. However, it is important to approach these findings with caution due to limitations, such as the non-normal distribution of the dependent variable and potential omitted variable bias.

Future research should consider alternative modeling techniques to address these limitations and provide a more robust analysis. Additionally, incorporating a wider range of geopolitical, economic, and cultural variables in future studies could offer a more comprehensive understanding of the factors shaping countries' foreign policy preferences and alliances on a global stage.

By shedding light on the factors that influence consensus at the United Nations General Assembly, this project contributes to more informed decision-making and facilitates international cooperation, ultimately promoting a better understanding of the complexities of global politics.
