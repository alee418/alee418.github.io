---
layout: default
---

# Boston Armed Robberies Report

## Inspection of Data

Over time, the variance increases; this can be seen in the fact that the peaks and troughs have
larger space in between them. The mean also changes as an upward trend is evident. Therefore,
the data is not stationary.

![Branching](/assets/img/BARP_1.png)

## Transformations

From inspecting the data, we notice that the data is monthly. There is no clear seasonality
present; thus, we decided to transform the data and to address the problem with the variance first.
As you can see from below, the x^1/3 transformation provides the best solution. In theory, if we
were to draw bands around the data, they would be equidistant throughout. From this point on,
we shall be using the x^1/3 transformation.

### pic

## ACF, PACF, and Periodogram

In evaluating the ACF and PACF, we see that the process is i.i.d. which means we can fit a
model. Based on the ACF, we choose q=2; through the PACF, p=2. Because we are differencing,
we decide upon an ARIMA(2, 0, 2) model. From the periodograms, there are no significant
peaks, and therefore, no evidence of seasonality.

### pics (2)

## Fitting A Model

The model we fit is an ARIMA(2,0,2) with a non zero-mean. The coefficients are given below
along with the variance. The blue points are the fitted values.

### pics (2)

## Residuals

From evaluating the residuals in the histogram, they seem to be distributed similarly to Gaussian
white noise. Most points on the qqplot lie on the qqline. The plot of residuals over time shows
that all structures are accounted for and there is no discernable trend or seasonal component. The
Box Ljung test produced a large p-value, supporting the idea that there is not enough evidence to
say the residuals are dependent.

### pics (4)

## Fit Model With auto.arima

The model we fit is an ARIMA(0,0,2) with a zero-mean. The coefficients are given below along
with the variance. The blue points are the fitted values.

### pics (2)

## Residuals

Through the histogram, the residuals seem to be distributed as Gaussian white noise. The qqplot
shows some points being off the qqline near the tails; however, this is negligible as the residuals
in the middle are close or on the line. Plotting the residuals over time shows there are no
dependent structures that have yet to be accounted for. This is further supplemented by the
results of running the Box-Ljung test, in which our p-value is greater than our chosen alpha of
0.05; from this test, we can conclude there is not enough evidence to say it is dependent.
Therefore, the fit of the model is good.

### pics (4)

## Theoretical Spectral Density

We chose L through creating a vector of candidates of odd numbers from 3 to 117. We then used
the modified Daniell kernel to choose an L of 39 for our smoothed periodogram. The plot given
below is the theoretical spectral density, which is similar to our smoothed version from step 3.

### pic

## Forecasting

In using auto.arima, R chose an ARIMA(1,1,1) model with the following coefficients. The AICc
is 1074. We kept the original data rather than transform it because the resulting ARIMA model is
not efficient; it overfit the data and had a variance of 0.

### pic

The residuals are an approximate Gaussian distribution.

### pics (2)

The performance of the forecast shows an upward trend, consistent with the trend in the observed
data. Unlike the observed data, the forecast does not have peaks and troughs but rather, is closer
to a straight line. Most points are close to the observed. This is a sufficient model given that
crime is dependent on economic environment, which is hard to include in our model as it is not a
trend nor a seasonal thing.

### pics (2)

## Appendix

```r
install.packages("astsa")
library(astsa)
library(forecast)
```

[back](./)
