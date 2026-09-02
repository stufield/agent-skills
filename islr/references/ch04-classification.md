# Chapter 4 — Classification

In Chapter 3 the response Y was quantitative. In many problems Y is
qualitative — a category. Examples in the book:

- **Default**: predict whether a credit-card holder will default (yes/no) from
  account balance, income, and student status.
- **Smarket**: predict whether the S&P 500 will go up or down tomorrow from
  the past five days' returns.
- **Khan**: predict the type of small round blue cell tumor (4 classes) from
  2,308 gene expression measurements.

We could try to use linear regression by coding Y as numeric (1, 2, 3, …) but
that's a bad idea: it imposes a fictitious ordering and produces predictions
outside the categorical range. Even for binary Y, where 0/1 coding works, the
predictions can fall outside [0, 1] and so cannot be interpreted as
probabilities. We need methods built for classification.

This chapter covers four classical classifiers: **logistic regression**,
**linear discriminant analysis (LDA)**, **quadratic discriminant analysis (QDA)**,
and **naive Bayes**, and revisits **K-nearest neighbors** for classification.
We compare them on the Default and Smarket datasets and develop the geometric
intuition that makes the method choices clear.

## 4.1 An Overview of Classification

Given an observation x, a classifier produces a predicted class ŷ. Most
classifiers do this by first estimating the **conditional class probabilities**

$$ p_k(x) = P(Y = k \mid X = x), \qquad k = 1, \ldots, K, $$

and then assigning x to the class with the highest probability. The Bayes
classifier (§2.2) does this with the *true* p_k. Different classifiers differ
in how they estimate p_k from training data.

## 4.2 Why Not Linear Regression?

If Y has more than two unordered classes, coding them 1, 2, 3 implies that
class 3 is "twice as far" from class 1 as class 2 is. That's nonsense for, say,
{stroke, drug overdose, epileptic seizure}. The fitted regression depends on
the arbitrary numerical labels.

Even for binary Y, fitting a linear regression to a 0/1 indicator works *for
prediction* — the fitted values estimate P(Y = 1 | X) up to a constant, and the
0.5 cutoff produces the same classifier as logistic regression in many cases.
But the linear fit can yield predicted probabilities that are negative or
above 1, which is awkward. Logistic regression fixes this elegantly.

## 4.3 Logistic Regression

We model the probability of the positive class directly.

### The logistic model

$$ p(X) = P(Y = 1 \mid X) = \frac{e^{\beta_0 + \beta_1 X}}{1 + e^{\beta_0 + \beta_1 X}}. $$

This **logistic function** maps any input in (−∞, ∞) to an output in (0, 1).
A bit of algebra shows

$$ \frac{p(X)}{1 - p(X)} = e^{\beta_0 + \beta_1 X}. $$

The left side is the **odds**: probability of yes divided by probability of
no. Odds run from 0 (certain no) to ∞ (certain yes). Taking logs:

$$ \log\!\left(\frac{p(X)}{1 - p(X)}\right) = \beta_0 + \beta_1 X. $$

The **log-odds** (or **logit**) is linear in X. So logistic regression is
"linear regression for the log-odds." A one-unit change in X changes the
log-odds by β₁, equivalently multiplies the odds by $e^{\beta_1}$.

### Estimating the coefficients: maximum likelihood

We choose β̂₀, β̂₁ to maximize the **likelihood**

$$ \ell(\beta_0, \beta_1) = \prod_{i: y_i = 1} p(x_i) \cdot \prod_{i': y_{i'} = 0} (1 - p(x_{i'})). $$

This says: under the model, the chance of seeing the actual 1s and 0s in the
training data is $\ell$. We pick coefficients to make this chance as large
as possible. There is no closed form; software solves the optimization
numerically (iteratively reweighted least squares).

For the Default data, regressing default (1 = yes) on balance:

$$ \widehat{p}(\text{balance}) = \frac{e^{-10.6513 + 0.0055 \cdot \text{balance}}}{1 + e^{-10.6513 + 0.0055 \cdot \text{balance}}}. $$

A balance of $1,000 gives p̂ ≈ 0.006. A balance of $2,000 gives p̂ ≈ 0.586.
The probability of default is highly nonlinear in balance — flat at low
balances, then a sharp rise around $1,800.

The output also gives standard errors, z-statistics (analogous to t-statistics),
and p-values. For balance: z = 24.95, p < 0.0001. Very strong evidence balance
predicts default.

### Multiple logistic regression

$$ \log\!\left(\frac{p(X)}{1 - p(X)}\right) = \beta_0 + \beta_1 X_1 + \cdots + \beta_p X_p. $$

We fit by maximum likelihood again. With multiple predictors, we again face
**confounding**. In the Default data, simple logistic regression of default
on student status gives β̂_student = +0.4049: students appear *more* likely to
default. But add balance to the model: β̂_student = −0.7149. Conditional on
balance, students are *less* likely to default. The marginal pattern reversed.

What's happening: students tend to carry higher balances. Higher balance means
more default risk. So *unconditionally* students default more — but only
because they have higher balances. Hold balance fixed and the student effect
flips. This is **Simpson's paradox**, and it's a routine fact of life with
correlated predictors. Always interpret coefficients in the multiple model.

### Multinomial logistic regression

For K > 2 classes, generalize to

$$ P(Y = k \mid X = x) = \frac{e^{\beta_{k0} + \beta_{k1} x_1 + \cdots + \beta_{kp} x_p}}{\sum_{l=1}^{K} e^{\beta_{l0} + \beta_{l1} x_1 + \cdots + \beta_{lp} x_p}}. $$

This is the **softmax** function (also used in the output layer of neural
networks for multi-class classification). It is identifiable only up to a
choice of reference class — common to set β_K = 0 for some baseline class K.

```r
library(ISLR2)
fit <- glm(default ~ balance + income + student, data=Default, family=binomial)
summary(fit)
exp(coef(fit))                       # odds ratios
predict(fit, newdata=data.frame(balance=2000, income=40000, student="No"),
        type="response")             # P(default = Yes)
```

## 4.4 Generative Models for Classification

Logistic regression *directly* models p(x) = P(Y | X). An alternative approach
is **generative modeling**: we model the full joint distribution of (X, Y) and
use Bayes's theorem to invert it.

### Bayes's theorem for classification

Let π_k = P(Y = k) be the **prior** probability of class k, and let f_k(x) =
P(X = x | Y = k) be the **density** of X within class k. Bayes's theorem:

$$ P(Y = k \mid X = x) = \frac{\pi_k f_k(x)}{\sum_{l=1}^{K} \pi_l f_l(x)}. $$

If we have estimates π̂_k and f̂_k(x), we can plug in and classify x to the
class with largest posterior. The π̂_k are easy: just the sample proportions.
The f̂_k(x) are the hard part. Different assumptions about the f_k give
different generative classifiers.

### Why use a generative classifier?

Three reasons:
1. When classes are well separated, parameter estimates for logistic regression
   are unstable. Generative methods are not.
2. With small n and X approximately Gaussian within each class, generative
   methods are more efficient (smaller standard errors).
3. They extend naturally to K > 2 classes.

### Linear discriminant analysis (LDA) for p = 1

Assume X | Y = k is normal with class-specific mean μ_k and **shared**
variance σ²:

$$ f_k(x) = \frac{1}{\sqrt{2\pi}\sigma} \exp\!\left(-\frac{(x - \mu_k)^2}{2\sigma^2}\right). $$

Plug into Bayes's theorem and take logs. The class with the largest posterior
turns out to be the one maximizing the **discriminant function**

$$ \delta_k(x) = x \cdot \frac{\mu_k}{\sigma^2} - \frac{\mu_k^2}{2\sigma^2} + \log\pi_k. $$

This is **linear in x**, hence the name. The LDA decision boundary between
classes k and l is the set of x with δ_k(x) = δ_l(x), which is a single point
(when p = 1) — specifically

$$ x = \frac{\mu_k + \mu_l}{2}, $$

assuming equal priors. We classify by which side of the midpoint x lies on.

We estimate the parameters by sample versions:

$$ \hat\mu_k = \frac{1}{n_k} \sum_{i: y_i = k} x_i, \qquad
   \hat\pi_k = \frac{n_k}{n}, $$

$$ \hat\sigma^2 = \frac{1}{n - K} \sum_{k=1}^{K} \sum_{i: y_i = k} (x_i - \hat\mu_k)^2. $$

The denominator n − K reflects estimating K means.

### LDA for p > 1

Assume X | Y = k is multivariate normal with class-specific mean μ_k and
**shared** covariance Σ. The discriminant function generalizes to

$$ \delta_k(x) = x^T \Sigma^{-1} \mu_k - \tfrac{1}{2} \mu_k^T \Sigma^{-1} \mu_k + \log\pi_k, $$

still linear in x. Decision boundaries are hyperplanes.

In the Default dataset with two predictors (balance, student), LDA achieves
training error 2.75%. But that hides a problem: most people don't default. A
classifier that predicts "no" for everyone has 3.33% error. LDA is barely
better — and importantly, of the people who *do* default, LDA misses 252 out
of 333 (75.7% false negative rate). The default 0.5 threshold is too high
when the positive class is rare and false negatives are expensive (a bank
loses thousands per missed default). Lowering the threshold to 0.2 yields a
much more useful classifier.

This is a generic lesson: with imbalanced classes, the 0.5 threshold is
almost never optimal. Use the **ROC curve** to choose.

### ROC curves and the AUC

For a binary classifier with a tunable threshold, every threshold gives a
specific (false positive rate, true positive rate) point:

- True positive rate (TPR, sensitivity, recall) = TP / (TP + FN).
- False positive rate (FPR, 1 − specificity) = FP / (FP + TN).

The **ROC curve** plots TPR vs FPR as we vary the threshold from 0 to 1. A
random classifier produces the 45° diagonal. A perfect classifier hugs the
top-left corner: 100% TPR at 0% FPR.

The **area under the ROC curve (AUC)** summarizes the curve as a single
number in [0.5, 1]. AUC = 0.5 is random. AUC = 1 is perfect. AUC has a clean
probabilistic interpretation: it is the probability that the classifier
ranks a randomly chosen positive example higher than a randomly chosen
negative one.

### Quadratic discriminant analysis (QDA)

Same as LDA but each class has its own covariance Σ_k:

$$ \delta_k(x) = -\tfrac{1}{2} x^T \Sigma_k^{-1} x + x^T \Sigma_k^{-1} \mu_k - \tfrac{1}{2}\mu_k^T \Sigma_k^{-1} \mu_k - \tfrac{1}{2}\log|\Sigma_k| + \log\pi_k. $$

The −½ xᵀ Σ_k⁻¹ x term is *quadratic* in x. Decision boundaries are
quadrics — ellipses, hyperbolas, parabolas. Much more flexible than LDA.

The trade-off:
- LDA uses Kp parameters for the means and p(p+1)/2 for the shared covariance.
- QDA uses Kp + K · p(p+1)/2 — much more.

LDA is preferred when n is small or p is large (less variance, smaller risk
of overfitting). QDA is preferred when n is large enough to accurately
estimate K separate covariance matrices, and the true class covariances
really do differ.

A useful rule of thumb: try both, see which has lower CV error.

### Naive Bayes

What if we relax the Gaussian assumption but keep the generative framework?
The **naive Bayes** classifier assumes the features are *independent* within
each class:

$$ f_k(x) = f_{k1}(x_1) \cdot f_{k2}(x_2) \cdots f_{kp}(x_p). $$

The "naive" name comes from the independence assumption — almost always wrong
in practice, but the resulting classifier is often surprisingly good. We only
need to estimate p one-dimensional densities per class instead of one
p-dimensional density per class. This is a huge variance reduction, often
worth the bias.

For each f_{kj}, we can:
- Assume it is Gaussian (with class-specific mean and variance).
- Estimate it nonparametrically with a kernel density.
- For categorical features, use the empirical proportions.

Naive Bayes excels when p is large relative to n (text classification — bag
of words gives p in the thousands or more) or when features really are
roughly independent within class.

## 4.5 A Comparison of Classification Methods

A useful way to compare logistic regression, LDA, QDA, naive Bayes, and KNN
is to look at the form of the **log-odds**:

| Method | Log-odds form |
|--------|--------------|
| Logistic regression | β₀ + β₁X (linear) |
| LDA | also linear in X (but parameterized via class means and shared covariance) |
| QDA | quadratic in X |
| Naive Bayes (Gaussian) | additive in nonlinear functions of each Xⱼ |
| KNN | non-parametric (no closed form) |

So:
- LDA and logistic regression are *both* linear classifiers — similar
  decision boundaries, but estimated differently. LDA uses more assumptions
  (Gaussian classes); logistic regression uses fewer.
- QDA is between linear and fully non-parametric — quadratic.
- Naive Bayes can capture nonlinear marginal effects but no interactions.
- KNN is fully non-parametric — flexible decision boundaries with no
  parametric form.

**Choice cheat-sheet**:

```
True boundary roughly linear, classes balanced → logistic or LDA
True boundary linear, small n, K > 2          → LDA
True boundary nonlinear, moderate n           → QDA or KNN
Many features, possibly correlated within     → naive Bayes
Large n, complex boundary, large p            → use Chapter 8 or 10 methods
```

## 4.6 Generalized Linear Models for Counts

A brief detour. Suppose Y is a **count** — non-negative integer. Linear
regression of Y on X can predict negative values (impossible) and assumes
constant variance (counts have variance increasing with the mean). The right
model is **Poisson regression**, which assumes Y | X is Poisson with mean

$$ \log(\lambda(X)) = \beta_0 + \beta_1 X_1 + \cdots + \beta_p X_p. $$

The exponential link guarantees λ > 0, and the Poisson distribution has the
right mean–variance relationship (Var = mean).

This, logistic regression, and ordinary linear regression are all examples of
**generalized linear models**: a linear predictor passed through a link
function appropriate to the response distribution. In R:

```r
glm(y ~ x, family=gaussian)   # = lm
glm(y ~ x, family=binomial)   # logistic regression
glm(y ~ x, family=poisson)    # Poisson regression
```

The Bikeshare data in ISLR illustrates Poisson regression for hourly bike
rental counts.

## 4.7 R Lab: Classification

```r
library(ISLR2)
library(MASS)          # lda, qda
library(e1071)         # naiveBayes
library(class)         # knn
library(pROC)

# Stock market: predict tomorrow's direction from past returns
attach(Smarket)
cor(Smarket[, -9])     # weak correlations among lags

train <- Year < 2005
test  <- !train

# Logistic regression:
glm.fit <- glm(Direction ~ Lag1+Lag2+Lag3+Lag4+Lag5+Volume,
               data=Smarket, family=binomial, subset=train)
glm.probs <- predict(glm.fit, Smarket[test, ], type="response")
glm.pred  <- ifelse(glm.probs > 0.5, "Up", "Down")
table(glm.pred, Direction[test])
mean(glm.pred == Direction[test])    # test accuracy

# LDA:
lda.fit <- lda(Direction ~ Lag1 + Lag2, data=Smarket, subset=train)
lda.fit                  # priors and class means
lda.pred <- predict(lda.fit, Smarket[test, ])
table(lda.pred$class, Direction[test])

# QDA:
qda.fit <- qda(Direction ~ Lag1 + Lag2, data=Smarket, subset=train)
qda.pred <- predict(qda.fit, Smarket[test, ])
mean(qda.pred$class == Direction[test])

# Naive Bayes:
nb.fit <- naiveBayes(Direction ~ Lag1 + Lag2, data=Smarket, subset=train)
nb.fit
nb.class <- predict(nb.fit, Smarket[test, ])
mean(nb.class == Direction[test])

# KNN:
train.X <- cbind(Lag1, Lag2)[train, ]
test.X  <- cbind(Lag1, Lag2)[test, ]
train.Y <- Direction[train]
set.seed(1)
knn.pred <- knn(train.X, test.X, train.Y, k=3)
mean(knn.pred == Direction[test])

# Default data: imbalanced, threshold matters
data(Default)
fit <- glm(default ~ balance + income + student,
           data=Default, family=binomial)
probs <- predict(fit, type="response")
pred5 <- ifelse(probs > 0.5, "Yes", "No")
pred2 <- ifelse(probs > 0.2, "Yes", "No")
table(pred5, Default$default)   # high false negatives
table(pred2, Default$default)   # better sensitivity, more false positives

# ROC and AUC:
roc.obj <- roc(Default$default, probs)
plot(roc.obj); auc(roc.obj)
```

The methods of this chapter all assume i.i.d. data. In Chapter 5 we'll learn
how to honestly assess their test error using cross-validation.

---

