---
title: "RPower"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```
Here
```{r}
library(simr)

data("simdata")
head(simdata)

# Everything before the 1|g is a fixed effect and this is a random intercepts model
model = glmer(z~x + (1|g), family = "poisson", data = simdata)
summary(model)

# Changing the effect size, which is the slope for the fixed effect coefficient.

fixef(model)["x"]
fixef(model)["x"] = -.05

set.seed(123)
powerSim(model)
```
Now we are extending the model to make it bigger.  We could use other studies to set up the power analysis parameters.
```{r}
#This adds extra levels to the multi part.  This is the part we want to extend, because we can add more people and don't want to add more measurements likely.  If we want to add more measurements, then we add more to the x variable, which is the time variable.  With x the time variable, we measuring the average change over time controlling for any other covariates that we add.

# Run the power curve along "g", which is the number of people in the study
model13 = extend(model, along = "g", n = 15)
powerCurve(model13, nsim = 10, along = "g")
```
Try from scratch.  We have five values of x for each person, which means we have five time points of data for each person.  Then we need to create the fixed intercept and slope.

Then we have a random slope, which is how much they difference on the dependent variable.  

How do we get random slopes in this package?
```{r}
x = rep(1:5)
g = c(1:100)
X = expand.grid(x = x, g = g)
b = c(2, -.1) # intercept and slope
V1 = .5
V2 <- matrix(c(0.5,0.05,0.05,0.1), 2)
s= 1

model = makeLmer(y ~ x + (1 | g), fixef = b, VarCorr = V1, sigma = s, data = X)

powerCurve(model, nsim = 10)
powerSim(model, nsim = 10)

```

