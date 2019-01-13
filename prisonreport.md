---
layout: default
---

# Prison Report

## Introduction

Does length of sentence differ according to gender? This is an interesting question,
because we might want to know if there is bias towards a gender when dealing
with crimes of similar severity. It could also be useful to further investigate reasons
why men and women experience different prison sentences or the effect of
sentencing differences once we do find out if there’s a difference or not. To answer
this question, I will use the Mann Whitney Test and confidence interval for shift
parameter.

## Summary of Data

To look at the distributions of our groups of data, I have created histograms of the
data (F = Female, M = Male) as follows:

![Branching](/assets/img/PR_1.png)

To further assess the distributions of the female and male sentencing times or
pinpoint any possible outliers, I also created a boxplot of the data:

![Branching](/assets/img/PR_2.png)

The histograms show that the distributions of our groups are not completely
symmetric. The boxplot also shows that the data is slightly skewed and that the
length of sentence for males may be higher than it is for females.

For summary statistics, the sample means for Females and Males respectively are
13.2857143, 20.6666667.

The sample standard deviations for Females and Males respectively are
9.0675124, 6.0201681.

Because the data seems to be skewed and have unequal variance, we can go with
a nonparametric technique. The Mann Whitney Test would be good since we are
interested in the mean, our distribution is not necessarily symmetric and we can
get confidence intervals to further back up our test.

## Analysis

According to the Mann Whitney Test, the null hypothesis is the mean sentence times
are equal for females and males, and the alternative hypothesis is the mean sentence
lengths for females and males are not the same.
From R, the test statistic is 2.1617 and as a result, we get an exact p-value
of 0.02988.

## Interpretation

Because the p-value is less than a reasonable value of alpha = 0.05, we reject the
null hypothesis that the sentencing length between females and males are the
same. Furthermore, we can conclude that the average sentence for one gender is
significantly different from the average sentence of the other gender.

Since we have rejected the null hypothesis, we can find out how much the genders
differ by sentence length. To do this, we find the confidence interval for the shift
parameter using all pairwise differences.

The 95% confidence interval for the shift parameter is (-15,-1).
This interval suggests a significant difference in the CDF’s of the two genders. The Male group
tends to be larger.

In other words, we are 95% confident the time spent in prison for women is 1 to 15
years less than the time spent in prison for men.

## Conclusion

With the Mann Whitney Test, I found that there is a difference in sentencing length
among females and males. Using a confidence interval for the shift parameter, I
also found that Males tend to spend between 1 to 15 more years in prison than
Females do for similar crimes. Using this information, we could say that males
spend significantly more time in jail than women do for crimes similar in severity
and this could be due to gender bias.

## Appendix

```r
# Mann-Whitney Test
library(coin)
data2 = read.csv("C:/Users/Anna/Desktop/SPRING 2016/STA 104/prison.csv",header=T)
years = data2$Length; gender = data2$Gender
dat2 = data.frame(years,gender); rm(years,gender); dat2$rank = rank(dat2$years, ties = "average")
group.order = aggregate(years ~ gender, dat2, mean)$gender
Xi = aggregate(years ~ gender, dat2, mean)$years
si = aggregate(years ~ gender, dat2, sd)$years
Ri = aggregate(rank ~ gender, dat2, mean)$rank
ni = aggregate(years ~ gender, dat2, length)$years
results = rbind(Xi,si,Ri,ni)
rownames(results) = c("Group Mean","Group SD","Rank Mean","Sample Size")
colnames(results) = as.character(group.order); rm(years,gender)
test2 = wilcox_test(years ~ gender, dat2, distribution = "exact",alternative = "two.side
d",conf.level = 0.95, conf.int = TRUE)

# Histograms and Boxplot
library(FSA)
hist(years ~ gender, dat2, xlab='Length of Sentence (yrs)')

boxplot(dat2$years[1:12],dat2$years[13:26],names=c('Male','Female'),xlab='Gender',ylab='Length of Sentence (yrs)',main='Boxplot for Prison Data')

# Confidence Interval for Shift Parameter
CI = as.numeric(confint(test2)$conf.int)
```

[back](./)
