---
layout: default
---

# Weather Report

## Introduction

Are certain weather conditions more deadly than others? This is a question of
interest, because we might want to assess the risk of living in certain areas where
there are more blizzards, flash floods, etc. This could be useful for finding the best
area to live or for insurance companies trying to determine how certain areas
should be insured against extreme weather. To find an answer, I will use the
Krustal Wallis Test and multiple comparisons.

## Summary of Data

To assess the distributions of our groups of data, I have created histograms (B =
Blizzard, FF = Flash Flood, L = Lightning, T = Tornado) as follows:

## pic

And to further address the distributions of the different types of extreme weather or
pinpoint any possible outliers, I have also created a boxplot of the data:

## pic

The histograms show that the distributions of our groups are not symmetric. The
boxplot also shows that the data is skewed and that the number of deaths for flash
floods may be different from the rest. It also shows that there is an outlier in the
flash flood group.

For summary statistics, the sample means for Blizzard, Flash Flood, Lightning,
Tornado are 45.8333333, 66.1666667, 38.6666667, 40.5 respectively.

And the sample standard deviations for Blizzard, Flash Flood, Lightning, Tornado
respectively are 8.3286654, 23.5916652, 7.3393914, 9.3112835.

At this point, we can see that because the data is skewed, contains an outlier, and
does not have constant variance, we can go with a nonparametric technique.
Krustal Wallis would be good in that it uses ranks to get rid of outlier effects and
would have higher power than ANOVA.

## Analysis

For the Krustal Wallis Test, the null hypothesis is that the average number of deaths
is the same among the groups and the alternative hypothesis is that at least one of the 
average number of deaths is different between groups.

As computed in R, the test statistic is 9.8436 and as a result, we get a p-value
of 0.01994.

## Interpretation

Since the p-value is less than a reasonable value of alpha = 0.05, we reject the null
hypothesis that the number of deaths between the four different weather conditions
are the same. As a result, we can conclude that at least one distribution is
significantly different from the other groups.

Once we have rejected the null hypothesis, we can find out which group means
differ significantly. To do this, we simply find the difference in average ranks and
compare them to the three critical values (Bonferoni, Fisher LSD, and Tukey HSD).

The average ranks for Blizzard, Flash Flood, Lightning, and Tornado are
respectively 13, 19.75, 8.0833333, 9.1666667.

The variance of all ranks is 49.8695652.

All of the possible differences in ranks are 6.75, 4.9166667, 3.8333333,
11.6666667, 10.5833333, 1.0833333.

The asymptotic cutoff values for Bonferonni, Fisher’s LSD, and Tukey’s HSD at alpha =0.05
are 10.7565824, 7.9910759, and 11.411695 respectively.

If the absolute difference in average ranks between groups are greater than or
equal to the cutoff values, then it is said that there’s significant difference between
the groups. Based on the absolute differences in rank, some of the groups appear
to be significantly different. Flash Flood and Lightning are different by all three
methods. And Flash Flood and Tornado are different by Fisher’s LSD.

## Conclusion

Using the Krustal Wallis Test, I found that certain weather conditions are more
lethal than others. In particular, with the multiple comparisons technique, I found
that Flash Floods resulted in significantly more deaths than Lightning did. Using
this information, we could say that areas where flash floods are more common may
present higher risk than those that have more lightning occurrence. For someone
looking for a new place to live or looking for insurance rates for a certain area, it
would be good to know the difference between areas that predominantly
experience flash floods and areas the see mostly lightning.

[back](./)
