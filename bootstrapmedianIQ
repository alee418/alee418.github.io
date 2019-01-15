---
layout: default
---

# Bootstrapping Median Inmate IQ

## Introduction

Is the median IQ for those in prison lower than the overall IQ median of 100? Does
the median IQ differ by gender for inmates? These are questions of interest
because we might want to assess whether inmates tend to have lower IQ and if
there is a difference in IQ between genders of inmates. This could be useful for
using IQ to predict whether a person or specific gender will be less or more likely to
be involved in misconduct. To find an answer, I will use bootstrap methods.

## Summary of Data

To assess the distributions of our data, I have created histograms as follow (I =
Inmate, F = Female, M = Male):

![Branching](/assets/img/BMII_1.png)

![Branching](/assets/img/BMII_2.png)

And to further address the distributions or pinpoint any possible outliers, I have
also created boxplots of the data:

![Branching](/assets/img/BMII_3.png)

![Branching](/assets/img/BMII_4.png)

The histograms show that our distributions are not symmetric. The boxplots also
show that the data is not exactly normal either.

For summary statistics, the sample mean for Inmate is 101, for Female is 102 and
for Male is 100.

And the sample standard deviation for Inmates is 14.693, for Female is 16.286 and
for Male is 13.711.

At this point, we can see that the data is not symmetric so we can go with a nonparametric
technique. We have a small sample, so the assumption of normality is also likely to be 
violated and asymptotics might not work as well. A nonparametric bootstrap method would be 
good in that it resamples from the data and allows for a better sample approximation of 
our estimator.

## Analysis

Our first null hypothesis is the median IQ for inmates equals 100 and alternative 
hypothesis is the median IQ of all inmates is less than 100.

To find bootstrap estimations, I have plotted a histogram that estimates the
bootstrap distribution of the median IQ of inmates as follows:

![Branching](/assets/img/BMII_5.png)

The bootstrap distribution of the median with B = 4000 bootstrap samples does not
appear to be symmetric.

Using the bootstrap distribution, the bootstrap estimate of the median is 102.

We can also estimate the bias and standard error, which are 0.189
and 6.024 respectively.

Since there is some bias, it will be appropriate to use a Bias Corrected and
Accelerated method, which will correct for the bias and the apparent skew in the data.

Using R, the 95% BCA confidence interval for the true median is (89, 110).

Our second null hypothesis is the difference between median IQ of female and male inmates
equals zero and alternative hypothesis is the difference between the median IQ of female 
and male inmates does not equal zero.

To determine the appropriate confidence interval method, I have plotted a
histogram of the bootstrap distribution of the median IQ difference as follows:

![Branching](/assets/img/BMII_6.png)

The bootstrap distribution of median IQ difference with B = 4000 bootstrap samples appears to be
slightly skewed.

Since the distribution is not perfectly symmetric and our standard error is unknown,
simple resampling would be appropriate.

Using the BCA method, the 95% confidence interval for the true difference in
medians is (6, 27.5).

## Interpretation

For the first confidence interval, we are 95% confident the median IQ of inmates is
between 89 and 110.

The interval is not strictly below 100, so we cannot conclude that the median IQ of
inmates is lower than the overall IQ of 100. We fail to reject the null hypothesis that
the median IQ of inmates can equal 100.

In the second confidence interval, we are 95% confident the difference in median
IQ between female and male inmates is from 6 to 27.5.

The interval is not strictly above or below 0, which suggests no significant
difference in median IQ between genders. We fail to reject the null hypothesis that
the median IQ of females and males do not differ.

## Conclusion

Using bootstrap method, I found that the median IQ between inmates and average
people are not significantly different. The median IQ for those in prison is not lower
than the overall IQ median of 100. I also found that the median IQ between female
inmates and male inmates are not significantly different either. Using this
information, we can say that one cannot simply predict if a person or gender will be
more prone to misconduct based solely on their IQ score.

## Appendix

```r
# Summary of Jail Data
library(coin)
data = read.csv("C:/Users/Anna/Desktop/SPRING 2016/STA 104/jail.csv",header=T)
iq = data$IQ; gender = data$gender; dat = data.frame(iq, gender)

# Histograms and Boxplots
library(FSA); hist(dat$iq, xlab='IQ Score',main='I')
hist(iq ~ gender, dat, xlab='IQ Score')
sort.dat = dat[order(gender) , ]
boxplot(dat$iq,ylab='IQ Scores',main='Boxplot for Inmate')
boxplot(sort.dat$iq[1:9],sort.dat$iq[10:17],names=c('Female','Male'),ylab='IQ Scores',mai
n='Boxplot by Gender')

library(coin); X = mean(dat$iq); s = sd(dat$iq)
Xi = aggregate(iq ~ gender, dat, mean)$iq; si = aggregate(iq ~ gender, dat, sd)$iq

# Bootstrap Method
library(boot); b.data = as.numeric(dat$iq)
B = 4000
boot.median = sapply(1:B, function(i){
boot.sample = sample(b.data, length(b.data),replace = T)
theta.i = median(boot.sample)
return(theta.i)
})
hist(boot.median,main = "Bootstrap Distribution of Median",xlab = "Bootstrap Median")
theta.hat = median(b.data)
boot.expect = mean(boot.median)
boot.bias = boot.expect ‐ theta.hat
boot.SE = sqrt( var(boot.median)*(B‐1)/B)

# BCA Confidence Interval Method
median.fun = function(b.data, random){
boot.data = b.data[random]
return(median(boot.data))
}
alpha = 0.05
r.bootstraps = boot(b.data,median.fun, R = 4000)
r.ci = boot.ci(r.bootstraps,type = "bca",conf = 1‐alpha)
r.ci.bca = r.ci$bca[4:5]

# Difference of Two Samples Bootstrap Method
B = 4000
boot.diff = sapply(1:B,function(i){
group1 = dat$iq[data$gender == "F"]
group2 = dat$iq[data$gender == "M"]
boot.group1 = sample(group1, length(group1),replace = T)
boot.group2 = sample(group2, length(group2),replace = T)
return(median(boot.group1) ‐ median(boot.group2))
})
hist(boot.diff,xlab = "Bootstrap Difference",main = "Bootstrap Distribution of Difference
in Median of Female vs. Male")

theta.hat = median(dat$iq[data$gender == "F"]) ‐ median(dat$iq[data$gender == "M"])
boot.expect = mean(boot.diff); boot.bias = boot.expect ‐ theta.hat
boot.SE = sqrt(var(boot.diff)*(B‐1)/B)

# Difference in Two Sample Medians BCA Method
diff.fun = function(boot.diff,random){
boot.data = boot.diff[random]
return(boot.data)
}; alpha = 0.10
CI = boot.ci(boot(boot.diff,diff.fun,R = 4000),conf=1‐alpha,type = "bca")
bca.CI = CI$bca[4:5]
```

[back](./)
