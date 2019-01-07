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

![Branching](/assets/img/WR_1.png)

And to further address the distributions of the different types of extreme weather or
pinpoint any possible outliers, I have also created a boxplot of the data:

![Branching](/assets/img/WR_2.png)

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
experience flash floods and areas that see mostly lightning.

```r
# Krustal Wallis Test
library(coin)
data = read.csv("C:/Users/Anna/Desktop/SPRING 2016/STA 104/weather.csv",header=T)
deaths = data$Deaths
groups = data$Groups
dat = data.frame(deaths, groups)
rm(deaths,groups)
dat$rank = rank(dat$deaths, ties = "average")
group.order = aggregate(deaths ~ groups, dat, mean)$groups
Xi = aggregate(deaths ~ groups, dat, mean)$deaths
si = aggregate(deaths ~ groups, dat, sd)$deaths
Ri = aggregate(rank ~ groups, dat, mean)$rank
ni = aggregate(deaths ~ groups, dat, length)$deaths
results = rbind(Xi,si,Ri,ni)
rownames(results) = c("Sample Mean","Sample SD","Rank Mean","Sample Size")
colnames(results) = as.character(group.order)

library(coin)
data = read.csv("C:/Users/Anna/Desktop/SPRING 2016/STA 104/weather.csv",header=T)
deaths = data$Deaths
groups = data$Groups
dat = data.frame(deaths, groups)
rm(deaths,groups)
test = oneway_test(deaths ~ groups, dat)

# Histograms and Boxplot
library(FSA)
hist(deaths ~ groups, dat, xlab='Number of Deaths')

boxplot(dat$deaths[1:6],dat$deaths[7:12],dat$deaths[13:18],dat$deaths[19:24],names=c('Lightning','Tornado','Flash Flood','Blizzard'),xlab='Types of Extreme Weather',ylab='Number of Deaths',main='Boxplot for Weather Data')

# Multiple Comparisons
library(coin)
data = read.csv("C:/Users/Anna/Desktop/SPRING 2016/STA 104/weather.csv",header=T)
deaths = data$Deaths
groups = data$Groups
dat = data.frame(deaths, groups)
rm(deaths,groups)
dat$rank = rank(dat$deaths, ties = "average")
group.order = aggregate(deaths ~ groups, dat, mean)$groups
Xi = aggregate(deaths ~ groups, dat, mean)$deaths
si = aggregate(deaths ~ groups, dat, sd)$deaths
Ri = aggregate(rank ~ groups, dat, mean)$rank
rm(length)
ni = aggregate(deaths ~ groups, dat, length)$deaths
results = rbind(Xi,si,Ri,ni)
rownames(results) = c("Group Mean","Group SD","Rank Mean","Sample Size")
colnames(results) = as.character(group.order)
SR.2 = var(dat$rank)
N = nrow(dat)
K = length(unique(dat$groups))

all.diff = abs(c(Ri[1] ‐ Ri[2],Ri[1] ‐ Ri[3], Ri[1] ‐ Ri[4],Ri[2] ‐ Ri[3],Ri[2] ‐ Ri[4],Ri[3] ‐ Ri[4]))
names(all.diff) = c("I vs II","I vs III","I vs IV","II vs III","II vs IV","III vs IV")

alpha = 0.05
g = K*(K‐1)/2
BON12 = qnorm(1‐alpha/(2*g))*sqrt(SR.2*(1/ni[1] + 1/ni[2]))
BON13 = qnorm(1‐alpha/(2*g))*sqrt(SR.2*(1/ni[1] + 1/ni[3]))
BON14 = qnorm(1‐alpha/(2*g))*sqrt(SR.2*(1/ni[1] + 1/ni[4]))
BON23 = qnorm(1‐alpha/(2*g))*sqrt(SR.2*(1/ni[2] + 1/ni[3]))
BON24 = qnorm(1‐alpha/(2*g))*sqrt(SR.2*(1/ni[2] + 1/ni[4]))
BON34 = qnorm(1‐alpha/(2*g))*sqrt(SR.2*(1/ni[3] + 1/ni[4]))
all.BON = c(BON12, BON13, BON14, BON23, BON24, BON34)
LSD12 = qnorm(1‐alpha/(2))*sqrt(SR.2*(1/ni[1] + 1/ni[2]))
LSD13 = qnorm(1‐alpha/(2))*sqrt(SR.2*(1/ni[1] + 1/ni[3]))
LSD14 = qnorm(1‐alpha/(2))*sqrt(SR.2*(1/ni[1] + 1/ni[4]))
LSD23 = qnorm(1‐alpha/(2))*sqrt(SR.2*(1/ni[2] + 1/ni[3]))
LSD24 = qnorm(1‐alpha/(2))*sqrt(SR.2*(1/ni[2] + 1/ni[4]))
LSD34 = qnorm(1‐alpha/(2))*sqrt(SR.2*(1/ni[3] + 1/ni[4]))
all.LSD = c(LSD12,LSD13,LSD14,LSD23,LSD24,LSD34)
HSD12 = qtukey(1‐alpha,K,N‐K)*sqrt((SR.2/2)*(1/ni[1] + 1/ni[2]))
HSD13 = qtukey(1‐alpha,K,N‐K)*sqrt((SR.2/2)*(1/ni[1] + 1/ni[3]))
HSD14 = qtukey(1‐alpha,K,N‐K)*sqrt((SR.2/2)*(1/ni[1] + 1/ni[4]))
HSD23 = qtukey(1‐alpha,K,N‐K)*sqrt((SR.2/2)*(1/ni[2] + 1/ni[3]))
HSD24 = qtukey(1‐alpha,K,N‐K)*sqrt((SR.2/2)*(1/ni[2] + 1/ni[4]))
HSD34 = qtukey(1‐alpha,K,N‐K)*sqrt((SR.2/2)*(1/ni[3] + 1/ni[4]))
all.HSD = c(HSD12,HSD13,HSD14,HSD23,HSD24,HSD34)
```

[back](./)
