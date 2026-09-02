# Chapter 5 — Resampling Methods

In Chapters 3 and 4 we estimated f̂ by minimizing some training loss. We
warned repeatedly that *training* error is optimistic, and what we care about
is *test* error. But how do we estimate test error if we have no separate test
set? And once we have estimates, how do we attach standard errors to them?

This chapter introduces two resampling methods — **cross-validation** and
**the bootstrap** — that answer both questions by repeatedly drawing samples
from the training data. They are among the most useful tools in all of
statistics.

## 5.1 Cross-Validation

The point of cross-validation is to estimate test error using only the
training data.

### The validation set approach

Split the n observations into two halves: a **training set** and a **validation
set**. Fit on the first, score on the second. The validation error is an
unbiased estimate of test error *for a model fit on n/2 data points*.

Two problems:
1. **High variance.** Different random splits give very different validation
   errors. The estimate is noisy.
2. **Pessimistic bias.** We trained on only n/2 points, so our model is worse
   than the model we would have trained on all n points. Test error of the
   *bigger* model is what we actually want, and the half-trained model
   overestimates it.

### Leave-one-out cross-validation (LOOCV)

To address both problems, we'd like to (a) train on as many points as possible,
(b) average over many splits. LOOCV does both. For i = 1, …, n:

1. Hold out observation i.
2. Fit the model on the other n − 1 observations.
3. Predict ŷᵢ and record the squared error (yᵢ − ŷᵢ)².

Average the n squared errors:

$$ \text{CV}_{(n)} = \frac{1}{n} \sum_{i=1}^{n} (y_i - \hat y_i^{(-i)})^2. $$

Each fit uses n − 1 points, so the bias is tiny (essentially the same as
fitting on n). And we average over n splits, so variance is reduced.

The catch: it takes n model fits. Expensive for most methods.

**A magical exception: linear regression.** For OLS,

$$ \text{CV}_{(n)} = \frac{1}{n} \sum_{i=1}^{n} \left(\frac{y_i - \hat y_i}{1 - h_i}\right)^2, $$

where ŷᵢ is the prediction *from the full-data fit*, and hᵢ is the leverage of
observation i. So LOOCV requires only one model fit. The same shortcut works
for any linear smoother.

### k-fold cross-validation

Compromise between validation set and LOOCV. Randomly partition the n
observations into k roughly equal **folds**. For j = 1, …, k:

1. Hold out fold j as validation.
2. Fit on the other k − 1 folds.
3. Compute the validation error on fold j.

Average the k validation errors:

$$ \text{CV}_{(k)} = \frac{1}{k} \sum_{j=1}^{k} \text{MSE}_j. $$

Standard choices: k = 5 or k = 10. With k = 5, each fit uses 80% of the data
— still a good model. With k = 10, 90%. We do only k fits, much cheaper than
LOOCV.

### Bias–variance trade-off in cross-validation

LOOCV has very little bias (each fit uses n − 1 points) but high variance
(the n fits are highly correlated since their training sets are nearly
identical). k-fold CV has slightly more bias (each fit uses (k-1)n/k points)
but less variance (fits are less correlated). Empirically, k = 5 or k = 10 is
usually a better estimate of test error than LOOCV.

### Cross-validation for classification

Replace MSE with misclassification rate:

$$ \text{CV}_{(k)} = \frac{1}{k} \sum_{j=1}^{k} \frac{1}{n_j} \sum_{i \in \text{fold } j} I(y_i \neq \hat y_i). $$

Same idea, different loss.

### The 1-standard-error rule

When CV is used to choose a tuning parameter (e.g., the number of predictors,
λ in lasso, K in KNN), the CV error estimate has its own standard error. The
**1-SE rule**: among models whose CV error is within 1 SE of the minimum,
choose the *simplest*. This trades a tiny bit of CV error for substantially
more parsimonious models, and reduces overfitting to the CV criterion itself.

### A subtle pitfall: improper cross-validation

Suppose you have p = 5,000 features and n = 100. You want to fit a classifier.
Tempting plan: (a) screen the 5,000 features, keeping the 100 with highest
correlation to y; (b) fit a classifier on those 100 features; (c) cross-
validate the classifier.

This is **wrong**. The screening step uses the *full* dataset (including the
validation fold), so the held-out fold leaks information into training. CV
error estimates will be wildly optimistic — sometimes near zero when the true
error is 50%.

**The right way**: cross-validate the *entire procedure*, including any feature
screening, scaling, or imputation. In each fold, redo screening on the
training portion only.

## 5.2 The Bootstrap

The bootstrap is a way to estimate the standard error or confidence interval
of any statistic. It works by treating the observed sample as a stand-in for
the population and resampling from it.

### The basic idea

Suppose we have a sample of size n and we have computed some statistic α̂.
We want to know how variable α̂ is. The classical approach is:
1. Derive a formula for SE(α̂) using probability theory.
2. Estimate the formula's ingredients from the data.

For complex statistics there is no formula. The bootstrap dispenses with
formulas. Instead:

1. Draw a **bootstrap sample**: sample n observations *with replacement* from
   the training data. Call it Z*₁.
2. Compute the statistic on Z*₁; call it α̂*₁.
3. Repeat B times to get α̂*₁, …, α̂*_B.
4. Estimate SE(α̂) by the standard deviation of the α̂*_b's:

$$ \widehat{\text{SE}}_B(\hat\alpha) = \sqrt{\frac{1}{B - 1} \sum_{b=1}^{B} \left(\hat\alpha^*_b - \frac{1}{B}\sum_{b'=1}^{B} \hat\alpha^*_{b'}\right)^2}. $$

Typical B = 1,000 to 10,000.

### Worked example: Portfolio data

The Portfolio dataset has 100 paired returns (X, Y). We want to choose α to
minimize the variance of αX + (1 − α)Y. The optimal α is

$$ \alpha = \frac{\sigma_Y^2 - \sigma_{XY}}{\sigma_X^2 + \sigma_Y^2 - 2\sigma_{XY}}. $$

We don't know the σs; we estimate them from the sample, getting α̂ = 0.5758.
But how variable is this estimate? No closed-form SE for this nonlinear
function of sample moments. Bootstrap to the rescue:

```r
library(ISLR2)
library(boot)

alpha.fn <- function(data, index) {
  X <- data$X[index]; Y <- data$Y[index]
  (var(Y) - cov(X,Y)) / (var(X) + var(Y) - 2*cov(X,Y))
}

set.seed(7)
boot.out <- boot(Portfolio, alpha.fn, R=1000)
boot.out
# Bootstrap SE for α̂ ≈ 0.0894
```

So a 95% bootstrap CI for α is roughly 0.5758 ± 2 · 0.0894 = (0.40, 0.75).

### Bootstrap for regression coefficients

For linear regression, classical formulas give SE(β̂). They depend on the
assumption that ε is iid with constant variance σ². If those assumptions are
wrong, the SEs are wrong. Bootstrap doesn't depend on those assumptions.

```r
boot.fn <- function(data, index)
  coef(lm(mpg ~ horsepower, data=data, subset=index))
boot(Auto, boot.fn, R=1000)
# Compare to summary(lm(mpg ~ horsepower, data=Auto))$coef
```

For Auto, the bootstrap SE for β̂_horsepower is slightly larger than the
formula SE, suggesting mild model misspecification.

### Important caveats

The bootstrap works by treating the sample as a proxy for the population. It
assumes:
- **iid observations.** For time series, you must use a **block bootstrap**:
  resample contiguous blocks rather than individual observations, to preserve
  short-range dependence.
- **The statistic is "smooth"** — small changes in the data produce small
  changes in the statistic. Maxima and minima don't satisfy this; bootstrap
  estimates of SE for the sample maximum can be unreliable.
- **n is large enough that the sample's empirical distribution is a good
  proxy for the truth.** Tiny n (say n < 30) can be problematic.

### Bootstrap vs cross-validation

- **CV** estimates *prediction* error.
- **Bootstrap** estimates *uncertainty in a statistic*.

Bootstrap *can* estimate prediction error using the **out-of-bag** samples,
but the result tends to be slightly biased (downward). For prediction-error
estimation, prefer CV.

## 5.3 R Lab: Cross-Validation and the Bootstrap

```r
library(ISLR2); library(boot)

# Validation set:
set.seed(1)
train <- sample(392, 196)
fit  <- lm(mpg ~ horsepower, data=Auto, subset=train)
mean((Auto$mpg - predict(fit, Auto))[-train]^2)   # validation MSE

# Try different polynomial degrees:
for (d in 1:5) {
  fit <- lm(mpg ~ poly(horsepower, d), data=Auto, subset=train)
  cat(d, mean((Auto$mpg - predict(fit, Auto))[-train]^2), "\n")
}

# LOOCV using cv.glm (works for any glm):
fit <- glm(mpg ~ horsepower, data=Auto)
cv.glm(Auto, fit)$delta[1]      # LOOCV (single fit shortcut for OLS)

# k-fold CV:
set.seed(17)
sapply(1:10, function(d) {
  fit <- glm(mpg ~ poly(horsepower, d), data=Auto)
  cv.glm(Auto, fit, K=10)$delta[1]
})

# Bootstrap (Portfolio):
boot(Portfolio, alpha.fn, R=1000)
```

Cross-validation is so central we will use it constantly from here on. Every
remaining chapter relies on it for tuning or evaluation.

---

