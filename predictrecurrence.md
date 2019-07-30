---
layout: default
---

# Predicting Recurrence of Breast Cancers

## Which variables best predict breast cancer recurrence events?

### Introduction

Cancer remains a common occurrence among people. Seeing how this health issue has
no cure, detection and early treatment are critical. Close monitoring of previous
cases of cancer is also important for a patient's physical health. Fitting a simple
logistic regression model will help predict the recurrence of breast cancer.

### Material and Methods

The dataset comes from the Institute of Oncology in Ljubljana, Yugoslavia by way of UC Irvine.
As of 1988, Drs. Zwitter and Soklic had reported 286 instances of breast cancer. The response
variable, in this case, is binary (no-recurrence events or recurrence-events). There are 9 possible
variables that can be used in our model. They are listed below:

1. Class: no-recurrence-events, recurrence-events
2. age: 10-19, 20-29, 30-39, 40-49, 50-59, 60-69, 70-79, 80-89, 90-99
3. menopause: lt40, ge40, premeno
4. tumor-size: 0-4, 5-9, 10-14, 15-19, 20-24, 25-29, 30-34, 35-39, 40-44, 45-49, 50-54, 55-59
5. inv-nodes: 0-2, 3-5, 6-8, 9-11, 12-14, 15-17, 18-20, 21-23, 24-26, 27-29, 30-32, 33-35, 36-39
6. node-caps: yes, no
7. deg-malig: 1, 2, 3
8. breast: left, right
9. breast-quad: left-up, left-low, right-up,	right-low, central
10. irradiat:	yes, no

Logistic regression will be appropriate for this data because we want to fit a regression
curve and the binary outcome is categorical. This will help predict the outcome given several 
predictors that can be continuous, categorical, or both. First, we will use l1 regularization
to select the features to include in the model. Then, we will use a grid search to find an
optimal C to fit the model with. Finally, we can find the model accuracy and summarize the odds 
ratio. Note that in total, 9 observations were missing from the dataset, which were accounted
for in the simple logistic regression.

### Results

**Model 1: Full Model**

![Branching](/assets/img/POBC_1.png)

**Model 2: Reduced Model**

![Branching](/assets/img/POBC_2.png)

Based on backwards stepwise regression and AIC, our reduced model is the best model.
Final Model: c ~ ct + csp + ma + bn + bdc + nn + m (includes variables 2, 4, 5, 7, 8, 9, 10).
For the model parameter estimates, their logistic regression coefficients give the change in
the log odds of the outcome for a one unit increase in the predictor variable. For example,
for every one unit change in clump thickness, the log odds of malignant (vs benign)
increases by 0.534. For a one unit change in marginal adhesion, the log odds of the cancer
being malignant increases by 0.342, etc.

**Odds Ratio**

![Branching](/assets/img/POBC_3.png)

For odds ratio, we can now say that for a one unit increase in clump thickness, the odds of a
cancer being malignant (vs being benign) increase by a factor of 1.7, etc. The 95%
confidence interval for that odds ratio would subsequently be (1.3, 2.3).

**Goodness of Fit (Likelihood Ratio Test)**

![Branching](/assets/img/POBC_4.png)

Chi-square of 781.08 with 7 degrees of freedom and an associated p-value of less than 0.05
shows that our model fits significantly better than the empty model.

**Residual Plots**

![Branching](/assets/img/POBC_5.png)

![Branching](/assets/img/POBC_6.png)

There are two lines of points because we are predicting for a variable that takes on one of
two values. We can see that there are outliers in our data (standardized residuals below -2
and above 2). Lowess smooth is not approximately horizontal with zero intercept, which
suggests there may be influential outliers.

### Conclusion and Discussion

In fitting a model that best fits our data, we found that the best one logistically regresses
class outcome (benign or malignant) on variables 2, 4, 5, 7, 8, 9, and 10. Not only does our
model fit well according to the goodness of fit test, we can conclude that the clump
thickness, uniformity in cell shape, marginal adhesion, bare nuclei, bland chromatin,
normal nucleoli, and mitoses attributes are good predictors of benign and malignant breast
cancers. There were outliers in our data, which suggests that there are points that do not fit
our data. Even though our model fit well, there may be model inadequacy. It would be a
good idea to look at the potential impact of these existing outliers on our model.

### Appendix

```r
bc = read.csv("breast-cancer-wisconsin.data.txt",header=F,na.strings=c("?"))
ct = bc$V2
csz = bc$V3
csp = bc$V4
ma = bc$V5
ecsz = bc$V6
bn = bc$V7
bdc = bc$V8
nn = bc$V9
m = bc$V10
c = factor(bc$V11)

# Full Model
mod = glm(c ~ ct + csz + csp + ma + ecsz + bn + bdc + nn + m,data=bc,family=binomial(link='logit'))
summary(mod)
anova(mod,test="Chisq")

# Stepwise Regression: Backwards--Reduced Model
backwards = step(mod)
summary(backwards)
formula(backwards)

# Odds Ratio and CI
exp(cbind(OR = coef(backwards), confint(backwards)))

# Likelihood Ratio Test
with(backwards, null.deviance - deviance)
with(backwards, df.null - df.residual)
with(backwards, pchisq(null.deviance - deviance, df.null - df.residual, lower.tail = FALSE))

# Standardized Pearson Residuals
pear.stdresid=resid(backwards,type="pearson")/sqrt(1-lm.influence(backwards)$hat)
subc <- na.omit(breast)

# Plot of standardized Pearson residuals against Outcome
plot(subc$c,pear.stdresid,xlab="class (1=benign 2=malignant)",ylab="standardized Pearson residuals")
abline(h=0)

# Plot of standardized Pearson residuals against fitted probability
plot(backwards$fitted,pear.stdresid,pch=19,xlab="fitted prob",ylab="standardized Pearson residuals")
lines(lowess(backwards$fitted,pear.stdresid),col="black",lwd=2)
```

[back](./)
