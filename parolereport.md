---
layout: default
---

# Parole Violation Depending on Record

## Introduction

Is parole violation dependent on prior record/behavior? This is an interesting
question because we might want to know if someone is more likely to violate their
parole terms based on their record. It could be useful for determining how much a
parolee should be monitored based on this prediction. To answer this question, I
will use permutation tests.

## Summary of Data

To look at our data numerically, I have created a contingency table of the data as
follows:

![Branching](/assets/img/PV_1.png)

Because the event that there’s no violation given previous record of fine and or
probation is rare compared to the rest, asymptotics will not work so we can go with
a nonparametric technique.

A permutation test would be good since we can shuffle the data so as to make
observations equally likely to come from any row or column. Once we find the
variable dependency, we can use a multiple comparisons procedure to show how
they are dependent as well.

## Analysis

According to the Permutation Test, the null hypothesis is 

![Branching](/assets/img/PV_2.png)

and the alternative hypothesis is

![Branching](/assets/img/PV_3.png)

From R, the test statistic equals 26.939 and as a result, the permutation
based p-value, with R = 3000, is 0.

## Interpretation

Because the p-value is less than any reasonable value of alpha, we can reject the null
hypothesis that there is no difference in violation by previous record/behavior.
Furthermore, we can conclude that violation and record are dependent variables.

Since we have rejected the null hypothesis, we can find out how the variables are
dependent using Tukey’s HSD for multiple comparisons.

The Z values for comparing row values for each column are as follows:

![Branching](/assets/img/PV_4.png)

Using R, the Tukey cutoff value using alpha = 0.05 is 2.443.

According to the cutoff value, there is significant difference between No Violation
and Violation in the Jail and None categories.

Those with no parole violations tended to have previous jail record. And those
with no parole violations were less likely to have no previous record.

## Conclusion

With the Permutation Test, I found that parole violation is dependent on
record/behavior. Using Tukey’s HSD and a multiple comparisons procedure, I also
found that parole violations were less likely from those with jail record and parole
violations were more likely from those without previous record. Using this
information, we could say that those who have served time in jail may need less
surveillance because they are less likely to commit further violations and perhaps
we can keep more of an eye on those who have no record and are less
predictable.

## Appendix

```r
# Summary of Parole Data
library(coin)
data2 = read.csv("C:/Users/Anna/Desktop/SPRING 2016/STA 104/parole.csv",header=T)
rec = data2$record; vio = data2$violation
dat2 = data.frame(vio,rec); cont.table = table(dat2)

# Permutation Test
library(coin); test = chisq.test(cont.table,correct=F)
eij = test$expected; chi.sq.obs = as.numeric(test$stat)
R = 5000
r.perms = sapply(1:R,function(i){
perm.data = dat2
perm.data$vio = sample(perm.data$vio,nrow(perm.data),replace = F)
chi.sq.i = chisq.test(table(perm.data),correct = F)$stat
return(chi.sq.i)
}); perm.pval = mean(r.perms >= chi.sq.obs)

# Multiple Comparisons
ni. = rowSums(cont.table)
n.j = colSums(cont.table)
n = sum(cont.table)
all.pjG1 = cont.table[1,]/ni.[1]
all.pjG2 = cont.table[2,]/ni.[2]
all.pbar = n.j/n
all.Zij = c(all.pjG1 ‐ all.pjG2)/sqrt(all.pbar*(1‐all.pbar)*(1/ni.[1] + 1/ni.[2]))

r.perms.cutoff = sapply(1:R,function(i){
perm.data = dat2
perm.data$vio = sample(perm.data$vio,nrow(perm.data),replace = F)
row.sum = rowSums(table(perm.data))
col.sum = colSums(table(perm.data))
all.pji = table(perm.data)[1,]/row.sum[1]
all.pji.= table(perm.data)[2,]/row.sum[2]
all.pbar = col.sum/sum(row.sum)
all.Zij = c(all.pji ‐ all.pji.)/sqrt(all.pbar*(1‐all.pbar)*(1/row.sum[1] + 1/row.sum
[2]))
Q.r = max(abs(all.Zij))
return(Q.r)
})
alpha = 0.05
cutoff.q = as.numeric(quantile(r.perms.cutoff,(1‐alpha)))
```

[back](./)
