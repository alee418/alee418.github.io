---
layout: default
---

# Lake Huron Levels Forecast

## Introduction

Lake levels are important for the Environmental Protection Agency to measure the potential 
amount of flooding in a specific area. The data we used was the water levels of Lake Huron, 
recorded between 1875 to 1972. It includes one response variable (lake level) and one 
explanatory variable (time). We first used graphs to show the data and then tested the 
normality with a qqplot. Additionally, because the qqplot did not give us a precise conclusion, 
we used the Shapiro-Wilks test for normality of the data. Finally, we used ARIMA to fit 
our model and predict water levels.

## Preliminary Graphical Analysis: Trends and Normality

Before we fitted a model, we decided to see what the data looked like graphically to see 
any obvious trends and patterns. For this specific reason, we plotted the data over the 
timespan given. We observed that there is a downward trend, but no particular pattern. 
To better get a grasp of seasonality and trend, we graphed the decomposition of the levels 
into trend, seasonality and remainder; we did this through linear filters and the lines 
are depicted in red, purple and green, respectively. 

![Branching](/assets/img/LH_1.png)

![Branching](/assets/img/LH_2.png)

During this step in our project, we were also concerned with normality. We evaluated this 
both graphically using a histogram of the density of the data and a qqplot, given below. 
The data seems to have some semblance of normality. In terms of the qqplot, if the data was 
truly normal, we would expect to see all the points on the qqline; in the histogram, the 
black line would more closely follow that of the blue (which depicts what a normal 
distribution would look like). We noticed some possible outliers on the tail of the qqline as well.

![Branching](/assets/img/LH_3.png)

![Branching](/assets/img/LH_4.png)

Deciding that graphical displays of normality are not enough, we conducted the Shapiro-Wilks 
test; we chose this test as it outperforms the Kologorov-Smirnoff test. The decision rule 
in terms of this test is to reject the null hypothesis (that the sample comes from that of a 
normal distribution) if the p-value is smaller than our chosen alpha level of 0.05. 
The output for R was: 

![Branching](/assets/img/LH_5.png)

Therefore, we fail to reject the null hypothesis and conclude our data is normally distributed.

## Regression and ARIMA

A method for estimating the trend of a time series is using least squares estimation, where 
we can fit a polynomial of order one to our data. Graphically, we can see that there seems 
to be a decline in water level over time because our least squares slope is negative.

![Branching](/assets/img/LH_6.png)

The downward trend of our time series is informative, but we would also like to forecast 
using ARIMA models. We used the auto.arima() function, which uses unit root tests and minimizes 
AICc and MLE to obtain an ARIMA model. It finds the number of differences d using repeated 
KPSS tests, and p and q by minimizing AICc after differencing the data d times. We find that 
the best model is ARIMA(0,1,0), which we used to forecast levels for the next decade (green).

![Branching](/assets/img/LH_7.png)

In diagnostic checking of our fitted ARIMA model, we analyzed the residuals. In the diagnostic 
plot of our fitted time series model, we can see a plot of the residuals, the autocorrelation 
of residuals, and the p-values of the Ljung-Box statistic for 10 lags. We can see that there 
seems to be a good amount of randomness in our fit, which is what we want.

![Branching](/assets/img/LH_8.png)

## Conclusion

Time series analysis of this data shows the water levels decreasing over time. When we consider 
normality, the qqplot shows a few outliers, but generally data that fits the qqline. To be precise, 
we use the Shapiro-Wilks test and reject the null hypothesis to conclude that the data is 
normally distributed. We then fit the time series regression using an ARIMA model and find that 
there seems to be a good amount of randomness in our fit.

## Appendix

```r
dat1=LakeHuron
	
plot.ts(LakeHuron, main="Water level in Lake Huron, 1875-1972", ylab="Level (in feet)")

hist(LakeHuron, main="Histogram of Level frequency", xlab="Feet", probability = T)
lines(density(LakeHuron))

mu<-mean(LakeHuron)
sigma<-sd(LakeHuron)
x<-seq(575,582,length=98)
y<-dnorm(x,mu,sigma)
lines(x,y,lwd=2,col="blue")

qqnorm(LakeHuron)
qqline(LakeHuron)

shapiro.test(LakeHuron)

library(ts)
plot(LakeHuron,type="l",main="Water level in Lake Huron, 1875-1972", ylab="Level (in feet)")
trend <- filter(LakeHuron,filter=rep(1/5,5))
seasonal <- filter(LakeHuron,filter=rep(1/25,25))
remainder <- filter(LakeHuron,filter=rep(1/81,81))
lines(trend,col="red")
lines(seasonal,col="purple")
lines(remainder,col="green")

library(tseries)
library(forecast)
data = LakeHuron
as.matrix(data)
plot(LakeHuron)

# Parameter-Estimation of ARIMA
auto.arima(data)
model = arima(data,order=c(1,0,1))

# Diagnostic Checking
tsdiag(model)

# Prediction of ARIMA-Model
prediction = predict(model,n.ahead=10)
plot(LakeHuron,xlim=c(1875,1982),ylim=c(575,584),ylab="Level(ft)",main="Water Level in Lake Huron 1875-1972 (1982)")
lines(prediction$pred,col="green")

# Regression Analysis
# least squares fit
lake = ts(data,start=1875)
t = 1:length(lake)
lsfit = lm(lake~t)
plot(t,lake,xlab="Time",ylab="Level(ft)",main="Water Level in Lake Huron 1875-1972")
lines(lsfit$fit,col="red")
```

[back](./)
