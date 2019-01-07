---
layout: default
---

# Predicting Occurrence of Breast Cancers

## Which attributes best predict benign or malignant breast cancers?

### Introduction

Cancer is a common occurrence among people. Breast cancer, more specifically, is
predicted to affect over 300,000 women alone this year; eighty percent affected will have
invasive cancer while the rest shall have in situ. Given that this is a prevailing problem with
no current, absolute cure, detection and early treatment are essential. Thus, we are
interested in fitting a logistic model which will predict the occurrence of breast cancer.

### Material and Methods

Our dataset comes from the University of Wisconsin Hospitals, Madison from Dr. William H.
Wolberg by way of UC Irvine. As of 1992, Dr. Wolberg has reported 699 instances of breast
cancer. The response variable, in this case, is binary; we are trying to predict if the tumor is
benign or malignant. There are 10 possible variables that can be used in our model. They
are listed below:

1. Sample code number: id number
2. Clump Thickness: 1 - 10 (benign cells usually in monolayers; cancerous—multilayers)
3. Uniformity of Cell Size: 1 - 10 (cancerous cells tend to vary in size)
4. Uniformity of Cell Shape: 1 - 10 (cancerous cells tend to vary in shape)
5. Marginal Adhesion: 1 - 10 (benign cells adhesive, cancerous—lose adhesive ability)
6. Single Epithelial Cell Size: 1 - 10 (enlarged epithelial cells may be malignant)
7. Bare Nuclei: 1 - 10 (nuclei without surrounding cytoplasm typically in benign tumors)
8. Bland Chromatin: 1 - 10 (chromatin tends to be coarser in cancerous cells)
9. Normal Nucleoli: 1 - 10 (cancerous cells tends to have more nucleoli)
10. Mitoses: 1 – 10 (cancerous cells tend to have more cell or mitoses count)
11. Class: (1 for benign, 2 for malignant)

Logistic regression will be appropriate for this data because we want to fit a regression
curve and the outcome is a categorical (in this case, binary) variable. This will help predict
the outcome given several predictors that can be continuous, categorical, or both (in this
case, categorical). First, we will fit a full model and then use backwards stepwise regression
to fit the best model with Akaike Information Criterion. We will use AIC to analyze the
estimated parameters and logit model at the 0.05 significance level. Small AIC will indicate
a better model as it penalizes models with lots of parameters and poor fit. Odds ratio will
be summarized and a likelihood ratio test will be used to determine goodness of fit. Lastly,
model diagnostics will be analyzed in a discussion of the standardized residuals.
For this project, we will see that AIC will give us a better model compared to the full logit
model, since the result from AIC is more generalizable than other models. We also want to
note that in total, 16 observations were missing from the dataset, which were accounted
for in our simple logistic regression.

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
