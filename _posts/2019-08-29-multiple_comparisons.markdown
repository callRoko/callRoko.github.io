---
layout: post
title:      "**Multiple Comparisons**"
date:       2019-08-29 11:45:20 -0400
permalink:  multiple_comparisons
---

Imagine the following scenario, you are a data scientist asked to assist a business make sense of all the data coming in about different aspects of their company. This can include employee performance, discounts, freight costs and sales on their products. As data scientist, your job is to perform experiments, learn as much possible about those findings and come up with recommendations for that business to follow. Usually this is done by defining several useful metrics or segmentations. Both strategies help you learn more about the business in different ways. In the case of segmentation, you may run statistical analyses to discover significant or non-significant findings; for example, if discounts increase product revenues and which discount level increases revenues the most. This means digging deeper to find insights. But this comes at a cost because the more comparisons you may make, the more likely it possible to make a false positive or come to conclusion that is incorrect.  This common mistake is known as multiple comparisons and this post will highlight how this mistake can affect your statistical analysis and a technique, I used to correct this problem. 
For this project, I was given a database from the Northwind Co, who is a food and beverage distributor and was asked to find some insights that would improve their business. Their database consists of 13 tables, all intertwined with unique ID keys. One table that caught a lot of my interest was the Order Detail information which consists of details of order quantities, unit prices and discounts. One of the questions I needed to answer was if discounts affected order quantities. Part of that question was if there was an optimal discount level that affected order quantities more than others. This question is an example of how one can run into the multiple comparisons problems because you need to compare all the combinations of discount levels and perform the same test multiple times. 
In every statistical analysis, it is important to define three things: a null hypothesis, an alternative hypothesis and a significance level. The null hypothesis tells that this no difference between the metrics we are comparing; the alternative hypothesis tells that there is a difference between the metrics we are comparing. For example, that products that are discounted account for more order quantities than products with no discount. The significant level also known as the p-value or alpha is a value selected to represent the possibility that the null hypothesis is true. In the case of the Northwind project, the p-value used was 0.05. This means that in a world where the level of discount doesn’t matter to order quantities, we would measure that discounts do affect order quantities 5% of the time due to random noise.  After the p-value is established and the experiment is performed, we determine the experiment’s p-value. If that value is less than 0.05, we can reject the null hypothesis; alternatively, if the p-value is greater than 0.05, we fail to reject the null hypothesis. However, this is not the entire story because obtaining a low p-value doesn’t guarantee that the null hypothesis is incorrect. For example, a p-value of 0.001 still means there’s a 1 in 1000 chance the null hypothesis is true. And if we perform this same test 100 times and get a small p-value, the false positive only becomes cumulative. 
In order to avoid this problem in the Northwind project, I used ANOVA and Tukey tests to perform multiple comparisons. ANOVA stands for analysis of variance and is a test that can generate statistical analysis for multiple groups. This is a useful alternative to t-tests when you need to compare multiple groups. For the Northwind Sale Analysis, I used ANOVA when comparing the different discount levels. Using statsmodels to generate an ANOVA table:
```
Import statsmodels.api as sm
From statsmodels.formula.api import ols
Lm =ols(‘Quantity ~C(‘Discount’), discounted).fit()
Lm.summary()
```
In this table, we can see the p-value generated. The next step would be performing a Tukey test. Tukey test is a single step statistic test used to find means that are significantly different from each other. For multiple comparisons, we will need to create data frames for the varying discount levels. Then we will import libraries for Tukey test:
from statsmodels.stats.multicomp import pairwise_tukeyhsd
from statsmodels.stats.multicomp import MultiComparison
```
tk = pairwise_tukeyhsd(discount_tukey.Quantity, discount_tukey.Discount, .05)
print(tk)
```
The resulting table gives us all the combination pairs of discount levels to compare, with the mean difference and whether this mean difference rejected the null hypothesis or failed to reject null hypothesis. In the case of the Northwind project, I found that for the most part the discount level that product was priced at didn’t affect order quantities. In fact, there is a possibility that these discounts were negatively affecting revenues. 
The multiple comparisons problem is an important issue to keep in mind when performing statistical analysis or examine studies. Because if we look hard enough, we may see correlations where they don’t exist. Humans are very adept at finding patterns and can make the mistake of finding meaning in what is random noise. From a business standpoint, being aware of this means analyzing claims closely and making more rational decisions. 



