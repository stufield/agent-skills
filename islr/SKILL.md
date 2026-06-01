---
skill:
  name: islr
  skill_version: "3.0"          # version of this SKILL.md file (not the book)
  book_edition: "2nd Edition (ISLR v2), 2023"
  source: "An Introduction to Statistical Learning with Applications in R, 2nd Edition (ISLR v2), James · Witten · Hastie · Tibshirani, 2023"
  description: >
    Textbook-depth reference for all of ISLR v2. Reads like the book itself:
    full derivations, intuitive explanations, worked numerical examples on the
    book's actual datasets (Advertising, Boston, Default, Smarket, Hitters, Wage,
    Carseats, OJ, USArrests, NCI60, BrainCancer, Khan, NYSE), R code labs, and
    transitions between ideas. Use to learn or to teach the material.
  when_to_load:
    - User asks about any topic in supervised or unsupervised learning
    - User asks "which method should I use" for prediction or inference
    - User asks about bias-variance, overfitting, or underfitting
    - User asks about cross-validation, bootstrap, or model selection criteria (Cp, AIC, BIC)
    - User asks about ridge, lasso, elastic net, regularization, or shrinkage
    - User asks about decision trees, random forests, bagging, boosting, BART, or XGBoost
    - User asks about SVMs, kernels, hyperplanes, margins, or support vectors
    - User asks about neural networks, CNNs, RNNs, LSTMs, transformers, or deep learning
    - User asks about PCA, k-means, hierarchical clustering, dendrograms, or matrix completion
    - User asks about survival analysis, Kaplan-Meier, Cox proportional hazards, censoring, or hazard ratios
    - User asks about multiple testing, FDR, FWER, Bonferroni, Holm, or Benjamini-Hochberg
    - User references ISLR, ISL, "James Witten Hastie Tibshirani", any chapter, or any lab in the book
    - User asks for R code involving lm, glm, glmnet, randomForest, gbm, xgboost, e1071, keras3, survival, pls, boot, leaps, splines, gam, mgcv, tree, rpart
    - User asks to explain, compare, derive, or implement any statistical learning algorithm
    - User wants to learn statistical learning / machine learning fundamentals
  do_not_load:
    - Pure software engineering with no statistics or modeling component
    - Pure data engineering, ETL, or database design
  instructions: >
    Treat this as a self-contained textbook. Cite chapter and section numbers
    (e.g., "ISLR §3.3.2") when relevant. Use the worked examples as templates.
    For any new problem, follow the model-selection workflow in the appendix.
    Always match method to problem type: regression vs classification, inference
    vs prediction, parametric vs nonparametric, n vs p regime. Flag assumption
    violations proactively. Provide R code that runs against the book's datasets
    when possible.
---

# An Introduction to Statistical Learning — Reference

*Based on James, Witten, Hastie, Tibshirani (2023), 2nd Ed.*

This document is meant to be read end-to-end like the book. Each chapter has the
same structure: motivation, theory with derivations, intuitive explanations,
worked examples on the book's datasets, R lab code, and a closing summary
that prepares the next chapter.

---

## Notation Conventions Used Throughout

We use the same notation as the book.

- **n** = number of observations.
- **p** = number of variables (features, predictors).
- **xᵢⱼ** = value of the j-th variable for the i-th observation, for i = 1,…,n and j = 1,…,p.
- **X** = the n×p data matrix, with element xᵢⱼ in row i and column j.
- **xᵢ** = the i-th row of X (vector of length p), the observation.
- **xⱼ** = the j-th column of X (vector of length n), the variable.
- **Y** = the response variable; yᵢ is the response for observation i.
- **f** = the unknown true function relating predictors to response.
- **f̂** = our estimate of f from the training data.
- **ε** = irreducible error term, mean zero, independent of X.
- **β** = coefficient vector for parametric models; β̂ is its estimate.
- **||v||** = Euclidean norm of vector v, i.e. √(Σⱼ vⱼ²).
- A "hat" (^) always means *estimated from data*. A bar (¯) means *sample average*.

When we write E[Y | X = x] we mean the *conditional expectation* of Y given that
X takes the value x. This is exactly the function f(x) we want to estimate in
regression.

---

## Datasets Used in the Book

These appear repeatedly. Knowing them helps internalize the methods.

| Dataset | Package | n | p | Response | Type |
|--------|---------|---|---|---------|------|
| `Advertising` | book website | 200 | 3 | sales | regression |
| `Auto` | ISLR2 | 392 | 8 | mpg | regression |
| `Boston` | ISLR2 | 506 | 12 | medv (median home value) | regression |
| `Carseats` | ISLR2 | 400 | 10 | Sales | regression |
| `College` | ISLR2 | 777 | 17 | Apps | regression |
| `Credit` | ISLR2 | 400 | 10 | Balance | regression |
| `Default` | ISLR2 | 10000 | 3 | default | binary classification |
| `Hitters` | ISLR2 | 322 | 19 | Salary | regression |
| `Khan` | ISLR2 | 83 | 2308 | small round blue cell tumor type | high-dim classification |
| `NCI60` | ISLR2 | 64 | 6830 | cell-line label | unsupervised |
| `OJ` | ISLR2 | 1070 | 17 | Purchase | binary classification |
| `Portfolio` | ISLR2 | 100 | 2 | — | bootstrap demo |
| `Smarket` | ISLR2 | 1250 | 8 | Direction (Up/Down) | binary classification |
| `USArrests` | base R | 50 | 4 | — | unsupervised (PCA, clustering) |
| `Wage` | ISLR2 | 3000 | 11 | wage | regression / GAM demo |
| `Weekly` | ISLR2 | 1089 | 8 | Direction | binary classification |
| `BrainCancer` | ISLR2 | 88 | 8 | survival time | survival analysis |
| `Publication` | ISLR2 | 244 | 9 | survival time | survival analysis |
| `NYSE` | ISLR2 | 6051 | — | log volume | RNN/time series |
| `IMDb` | keras | — | — | sentiment (binary) | RNN/text classification |
| `MNIST` | keras | 70000 | 784 | digit (0–9) | CNN |
| `CIFAR100` | keras | 60000 | — | image label (100 classes) | CNN |

Load any ISLR2 dataset with `library(ISLR2); data(Boston)`.

---

# Chapter 1 — Introduction

## 1.1 What Is Statistical Learning?

Statistical learning is a vast collection of tools for understanding data. The
tools fall into two large categories: **supervised** and **unsupervised**.

In **supervised learning**, we have data of the form (xᵢ, yᵢ) where xᵢ is a
vector of predictors and yᵢ is a response. The goal is to build a function ŷ =
f̂(x) that predicts y from x. If y is quantitative the problem is **regression**;
if y is qualitative the problem is **classification**.

In **unsupervised learning** there is no response yᵢ. We have only xᵢ, and we
ask questions like: are there natural clusters? Are some predictors redundant?
Can we summarize the data with fewer variables?

The book is organized to go from the simplest methods (linear regression) to
modern, very flexible methods (deep learning). The first chapter motivates the
field with three running examples that recur throughout the book:

1. **Wage data**: predicting a worker's wage from age, education, and year.
   This is a regression problem. A simple linear fit suggests wages rise with
   age, but the true relationship is nonlinear (rising, then leveling off).
   This motivates **flexible methods** like splines and generalized additive
   models (Chapter 7).

2. **Stock market data (Smarket)**: predicting whether the S&P 500 will go up
   or down tomorrow given the last five days' returns. This is a classification
   problem where the signal is small and the noise is large. It motivates
   careful **error estimation** via cross-validation (Chapter 5) and
   probability-based classifiers (Chapter 4).

3. **Gene expression data (NCI60)**: 64 cancer cell lines, each measured on
   6,830 genes, with no labels. Are there clusters? Can the 6,830-dimensional
   data be summarized in two or three principal components for visualization?
   This is the unsupervised setting (Chapter 12).

The chapter ends with a brief history. The earliest method is **least squares**
(Legendre 1805, Gauss 1809). **Linear discriminant analysis** is from Fisher
(1936). **Logistic regression** comes from the 1940s. **Generalized linear
models** unify these (Nelder & Wedderburn 1972). **Trees, random forests,
boosting, support vector machines, and neural networks** come from machine
learning research in the 1980s onward. Statistical learning, as the book uses
the term, is the unified statistical framework that contains all of these.

---

# Chapter 2 — Statistical Learning

## 2.1 What Is Statistical Learning?

We assume the data is generated by

$$ Y = f(X) + \varepsilon $$

where f is an unknown fixed function of X = (X₁, X₂, …, Xₚ) and ε is a random
error term with mean zero, independent of X. The function f represents the
**systematic information** that X provides about Y. Everything else is the
"noise" ε.

The goal of statistical learning is to estimate f.

### Why estimate f?

Two reasons: **prediction** and **inference**.

**Prediction.** We want to predict Y for new observations of X. We compute
ŷ = f̂(x) for some new x. We don't necessarily care what f̂ looks like; we
only care that ŷ is close to y. The expected squared error of a prediction
decomposes as

$$ E[(Y - \hat Y)^2] = \underbrace{[f(X) - \hat f(X)]^2}_{\text{reducible}} +
   \underbrace{\text{Var}(\varepsilon)}_{\text{irreducible}}. $$

The first term we can shrink by choosing a better f̂. The second term — the
variance of ε — we cannot, because ε is by definition unrelated to X. The
irreducible error sets the floor on prediction accuracy.

**Inference.** We want to understand *how* Y changes with X. Specific questions:
- Which predictors are associated with Y?
- What is the direction (positive/negative) of each association?
- What is the size?
- Is the relationship linear or more complex?

For inference, we need an interpretable f̂. A linear model is a great inference
tool because each coefficient has a precise meaning. A deep neural network with
millions of parameters is poor for inference even if it predicts well.

A good rule: if you only need prediction, use the most accurate model you can
fit. If you need to explain, prefer simpler, interpretable models even at some
cost in prediction accuracy.

### How do we estimate f?

We always have **training data** {(x₁,y₁),…,(xₙ,yₙ)}. We use this to fit f̂.
Methods come in two broad flavors.

**Parametric methods.** We assume f has a specific functional form with a
*finite* set of parameters. For example, a linear model assumes
$$ f(X) = \beta_0 + \beta_1 X_1 + \cdots + \beta_p X_p. $$
We then have to estimate only p+1 numbers — the βⱼ's. The advantage is
simplicity and statistical efficiency. The disadvantage is that if the true f
is far from linear, we have **bias**: a structural error we cannot fix even
with infinite data.

**Non-parametric methods.** We make no global assumption about the form of f.
We let the data determine the shape. A K-nearest-neighbors classifier or a
spline regression is non-parametric. The advantage is flexibility. The
disadvantage is that we need much more data, and the estimates have higher
variance.

### The trade-off between accuracy and interpretability

Methods like linear regression are highly interpretable but inflexible. Methods
like deep neural networks are highly flexible but uninterpretable. The
flexibility–interpretability trade-off is fundamental, and we will revisit it
in every chapter. As a rough chart, in increasing order of flexibility (and
decreasing order of interpretability):

```
    Subset selection ──→ Lasso ──→ Least squares ──→ GAM, trees
                ──→ Bagging, boosting ──→ Support vector machines
                ──→ Deep learning
```

## 2.2 Assessing Model Accuracy

There is no universally best method. We need ways to compare methods on a given
dataset.

### Measuring quality of fit — regression

The most common measure is the **mean squared error** (MSE):

$$ \text{MSE} = \frac{1}{n} \sum_{i=1}^{n} (y_i - \hat f(x_i))^2. $$

But computing MSE on the training data — call it the **training MSE** — gives a
biased view. Methods that are flexible enough can drive training MSE arbitrarily
small, even to zero, by interpolating every point. What we actually care about
is the **test MSE**: the MSE on observations the model has *not seen* during
training.

If we have a separate test set {(x'₁,y'₁),…,(x'ₘ,y'ₘ)}, we compute

$$ \text{Test MSE} = \frac{1}{m} \sum_{j=1}^{m}(y'_j - \hat f(x'_j))^2. $$

Here is the central tension: as we make a method more flexible, training MSE
keeps going down. Test MSE, by contrast, has a U-shape. It first decreases
(reducing bias), reaches a minimum, then increases (variance dominates). The
gap between training and test MSE — a phenomenon we call **overfitting** — is
the biggest practical problem in statistical learning.

### The bias–variance trade-off

For any new test point (x₀, y₀), the expected test MSE decomposes as

$$ E[(y_0 - \hat f(x_0))^2] = \text{Var}(\hat f(x_0)) + [\text{Bias}(\hat f(x_0))]^2 + \text{Var}(\varepsilon). $$

(The expectation is over many possible training sets and the noise in y₀.)

- **Variance**: how much f̂ would change if we re-trained on a different sample
  from the same population. High-variance methods overfit individual training
  sets.
- **Bias**: the error introduced by approximating a possibly very complex true
  f by a simpler form. A linear model has high bias if the truth is curved.
- **Irreducible error**: Var(ε), the noise in y itself.

Three facts follow:
1. Test MSE is at least Var(ε); we cannot do better.
2. To minimize expected test MSE, we want methods with simultaneously low
   bias and low variance — a hard combination.
3. Increasing flexibility typically *decreases* bias and *increases* variance.
   The optimum is somewhere in the middle, and we need cross-validation
   (Chapter 5) to find it.

### Measuring quality of fit — classification

For classification, instead of MSE we use the **error rate** (also called the
**0/1 loss** or **misclassification rate**):

$$ \text{Error rate} = \frac{1}{n} \sum_{i=1}^{n} I(y_i \neq \hat y_i), $$

where I(·) is the indicator function — 1 if true, 0 otherwise. As before, the
**training error rate** is optimistic; we care about the **test error rate**.

### The Bayes classifier and the Bayes error rate

Suppose we knew the true conditional probability P(Y = j | X = x) for every
class j and every x. The classifier that minimizes the test error rate
assigns x to the class with the highest probability:

$$ \hat Y(x) = \arg\max_{j} P(Y = j \mid X = x). $$

This is the **Bayes classifier**. It is the gold standard. Its error rate is
the **Bayes error rate**:

$$ \text{Bayes error} = 1 - E_X\!\left[\max_{j} P(Y = j \mid X)\right]. $$

The Bayes error rate plays the role for classification that Var(ε) plays for
regression: it is the irreducible floor. We can't beat it because the best you
can do at point x is bet on the most likely class, and even that has error
1 − max_j P(Y = j | X = x).

In practice we don't know P(Y | X), so we estimate it. Different methods
estimate it differently — logistic regression, LDA, and KNN all give different
estimates of the same conditional distribution.

### K-nearest neighbors (KNN)

KNN is the simplest non-parametric classifier. To classify x₀:

1. Find the K training points closest to x₀ (by Euclidean distance). Call this
   set N₀.
2. Estimate P(Y = j | X = x₀) by the fraction of those K neighbors in class j:
   $$ \hat P(Y = j \mid X = x_0) = \frac{1}{K} \sum_{i \in N_0} I(y_i = j). $$
3. Assign x₀ to the class with highest estimated probability.

The hyperparameter K controls flexibility:

- **K = 1**: each prediction depends on the single nearest training point. The
  decision boundary is jagged. Bias is low, variance is huge. Training error
  is zero (every training point is its own nearest neighbor) but test error
  can be poor.
- **K large**: the prediction at any x₀ depends on many neighbors. The boundary
  smooths out. Bias rises, variance falls.
- **K = n**: every prediction is the majority class in the entire training set
  (a single constant). Maximum bias, minimum variance.

The optimal K is dataset-specific and chosen by cross-validation.

KNN is also a regression method: ŷ₀ is the *mean* of the K nearest training
y's instead of the majority class.

### Why KNN suffers in high dimensions

The Euclidean distance between any two random points in p dimensions
concentrates as p grows: every point becomes roughly equidistant from every
other. This is the **curse of dimensionality**. KNN works well for p ≤ 4 or so
and degrades quickly beyond that. With p = 100, the "nearest" neighbor is
typically nearly as far away as the "farthest" one, and the local averaging
that KNN relies on breaks down.

For high-dimensional problems, parametric methods or regularization (Chapter 6)
are usually necessary.

## 2.3 Lab: Introduction to R

The original ISLR labs are in R. We'll show R code in every chapter. To run
the labs install:

```r
install.packages(c("ISLR2", "MASS", "glmnet", "leaps", "splines",
                   "gam", "mgcv", "tree", "randomForest", "gbm",
                   "BART", "e1071", "class", "pls", "boot",
                   "survival", "pROC", "car", "factoextra",
                   "keras3"))
library(ISLR2)
```

A few essentials:

```r
# Load data:
data(Boston)
?Boston            # documentation

# Inspect:
dim(Boston)        # n × p
names(Boston)      # column names
head(Boston)       # first 6 rows
summary(Boston)    # numeric summaries

# Plot:
plot(Boston$lstat, Boston$medv)
pairs(Boston[, 1:5])

# Subset:
Boston[1:5, ]              # rows 1–5
Boston[, "medv"]           # column medv
Boston$medv                # same
subset(Boston, medv > 30)  # filter rows
```

We will spend the rest of the book walking through methods that estimate f
better than KNN — by exploiting structure — and that estimate the *uncertainty*
in those estimates so we can do real inference.

---

# Chapter 3 — Linear Regression

Linear regression is the workhorse of statistics. Many "modern" methods —
logistic regression, generalized additive models, neural networks, even
boosting — are best understood as generalizations or modifications of linear
regression. Master this chapter and the rest of the book is much easier.

The running example here is the **Advertising** dataset. It records, for 200
markets, the amount spent (in thousands of dollars) on TV, radio, and
newspaper advertising, and the resulting product sales (in thousands of units).
We want to know:

1. Is there a relationship between advertising and sales?
2. How strong is it?
3. Which media contribute?
4. How accurately can we estimate the effect of each?
5. How accurately can we predict future sales?
6. Is the relationship linear?
7. Is there synergy among media?

Linear regression answers each of these.

## 3.1 Simple Linear Regression

We have a single predictor X and a quantitative response Y. We assume

$$ Y \approx \beta_0 + \beta_1 X. $$

The "≈" is doing real work: it allows for noise. The two unknown numbers β₀
(the **intercept**) and β₁ (the **slope**) are the **regression coefficients**
or **parameters**. Once we estimate them — call the estimates β̂₀ and β̂₁ — we
predict

$$ \hat y = \hat\beta_0 + \hat\beta_1 x. $$

### Estimating the coefficients: least squares

For each training point (xᵢ, yᵢ), the **residual** is

$$ e_i = y_i - \hat y_i = y_i - \hat\beta_0 - \hat\beta_1 x_i. $$

The **residual sum of squares** is

$$ \text{RSS} = e_1^2 + e_2^2 + \cdots + e_n^2. $$

Least squares chooses β̂₀, β̂₁ to minimize RSS. Setting partial derivatives to
zero and solving gives the closed-form

$$ \hat\beta_1 = \frac{\sum_{i=1}^{n}(x_i - \bar x)(y_i - \bar y)}{\sum_{i=1}^{n}(x_i - \bar x)^2}, \qquad
   \hat\beta_0 = \bar y - \hat\beta_1 \bar x. $$

Some intuition. The slope β̂₁ is the sample covariance of X and Y divided by
the sample variance of X — it has the units of Y per unit of X. The intercept
forces the line to pass through (x̄, ȳ), the centroid of the data.

In the Advertising example, fitting sales on TV gives

$$ \widehat{\text{sales}} = 7.03 + 0.0475 \cdot \text{TV}. $$

So an extra $1,000 in TV spend is associated with about 47 more units sold.
"Associated with" is the right phrase. We have not shown causation; in fact,
unmeasured confounders may produce the same correlation. Causation requires
either an experiment or strong assumptions.

### Assessing accuracy of the coefficient estimates

If we sampled 200 different markets, our estimates β̂₀ and β̂₁ would change
because of sampling variability. We need their **standard errors**. Under the
assumption that ε has constant variance σ²,

$$ \text{SE}(\hat\beta_0)^2 = \sigma^2 \left[\frac{1}{n} + \frac{\bar x^2}{\sum_{i=1}^{n}(x_i - \bar x)^2}\right], \qquad
   \text{SE}(\hat\beta_1)^2 = \frac{\sigma^2}{\sum_{i=1}^{n}(x_i - \bar x)^2}. $$

We don't know σ². We estimate it by the **residual standard error**:

$$ \text{RSE} = \sqrt{\frac{\text{RSS}}{n - 2}}, \qquad \hat\sigma = \text{RSE}. $$

We divide by n − 2 because we used 2 degrees of freedom to estimate β₀ and β₁.

The standard errors give us **confidence intervals**. Approximately,

$$ \hat\beta_1 \pm 2 \cdot \text{SE}(\hat\beta_1) $$

is a 95% CI. (More precisely, replace 2 by the 97.5th percentile of a
t_{n−2}-distribution; for n large this is ≈ 1.96.)

Standard errors also give us **hypothesis tests**. The most important is

$$ H_0: \beta_1 = 0 \quad \text{vs.} \quad H_1: \beta_1 \neq 0. $$

H₀ says X and Y have no linear relationship. We test it with the **t-statistic**

$$ t = \frac{\hat\beta_1 - 0}{\text{SE}(\hat\beta_1)}, $$

which under H₀ follows a t-distribution with n − 2 degrees of freedom. We
report a **p-value**: the probability under H₀ of seeing a t-statistic at
least as extreme as the one observed. Small p-value ⇒ reject H₀.

Concretely for the Advertising TV regression: β̂₁ = 0.0475, SE(β̂₁) = 0.0027,
t = 17.67, p < 0.0001. Sales and TV spend are very strongly associated.

### Assessing accuracy of the model: RSE and R²

Even when X has a real effect on Y, the model never explains everything. Two
quantities measure how well the line fits:

**Residual standard error**: an estimate of σ in the units of Y. RSE for
sales-on-TV is 3.26; the standard deviation of the noise in sales is about
3,260 units.

**R² statistic**: a unitless measure between 0 and 1 of the fraction of
variance in Y explained by X:

$$ R^2 = 1 - \frac{\text{RSS}}{\text{TSS}}, \qquad \text{TSS} = \sum_{i=1}^{n}(y_i - \bar y)^2. $$

TSS is the **total sum of squares**, the variance of Y before knowing X. RSS is
the variance of the residual *after* knowing X. So 1 − RSS/TSS is the fraction
of variance "removed" by the model. For Advertising-on-TV, R² = 0.612 — TV
explains 61% of the variance in sales.

In simple regression with one predictor, R² is the square of the correlation
between X and Y: R² = Cor(X, Y)². The two numbers carry the same information.

A common mistake: thinking small R² means the model is bad. In a noisy domain
(physics measurements with random error, biology, finance), R² of 0.1 may be
all the signal that exists.

## 3.2 Multiple Linear Regression

With p predictors X₁, …, Xₚ:

$$ Y = \beta_0 + \beta_1 X_1 + \beta_2 X_2 + \cdots + \beta_p X_p + \varepsilon. $$

**Interpretation**: βⱼ is the average change in Y for a one-unit change in Xⱼ
*holding all other predictors fixed*. The phrase "holding all other predictors
fixed" is essential and often misunderstood. With observational data, you
rarely if ever see two observations that differ only in one Xⱼ. The βⱼ is
nonetheless the right thing to interpret as a partial effect, mathematically.
Whether that partial effect has a causal interpretation is a separate question.

### Estimating the coefficients

Stack the predictors into an n × (p+1) matrix (with a column of 1s for the
intercept):

$$ \mathbf X = \begin{pmatrix} 1 & x_{11} & \cdots & x_{1p} \\
                                 1 & x_{21} & \cdots & x_{2p} \\
                                 \vdots & & & \vdots \\
                                 1 & x_{n1} & \cdots & x_{np} \end{pmatrix}. $$

Stack the responses into a length-n vector y. The vector of estimates is

$$ \hat{\boldsymbol\beta} = (\mathbf X^T \mathbf X)^{-1} \mathbf X^T \mathbf y, $$

assuming XᵀX is invertible (it is, as long as no column of X is a linear
combination of the others — i.e., no perfect collinearity).

### Why a multiple regression is not the same as separate simple regressions

A surprising and important point. Suppose we run three simple regressions of
sales on TV, on radio, on newspaper, separately. Each gives a positive,
statistically significant coefficient. We then run a multiple regression with
all three. The coefficients on TV and radio remain similar, but the newspaper
coefficient becomes ≈ 0 with a large p-value.

Why? Because newspaper spending is correlated with radio spending. In simple
regression we capture *both* the direct effect of newspaper and the indirect
effect of radio acting through newspaper. The multiple regression separates
them. The correct conclusion: holding radio and TV fixed, newspaper has no
detectable effect. The simple-regression conclusion was confounded.

**Rule**: never trust a simple regression for inference when you have multiple
correlated predictors. Always fit a multiple regression and report the
adjusted-for-others coefficients.

### Important questions in multiple regression

**1. Is there any relationship at all?** Test the global null

$$ H_0: \beta_1 = \beta_2 = \cdots = \beta_p = 0. $$

The **F-statistic** is

$$ F = \frac{(\text{TSS} - \text{RSS}) / p}{\text{RSS} / (n - p - 1)}. $$

Under H₀, F ≈ 1. If at least one βⱼ is nonzero, F > 1. We compare F against
the F_{p, n-p-1} distribution to get a p-value.

You might wonder: why not just look at the individual t-statistics? With p
large and lots of testing, some t-statistics will be "significant" by chance
alone (Chapter 13). The F-test is a single, omnibus test that controls the
type-I error for the global null.

**2. Which predictors matter?** This is **variable selection**. Three classic
approaches:

- **Best subset selection**: try all 2^p subsets, pick the best by some
  criterion. Infeasible for large p.
- **Forward selection**: start with intercept only; at each step add the
  predictor that most reduces RSS. Continue until a stopping rule.
- **Backward selection**: start with the full model; at each step remove the
  predictor with the largest p-value. Continue until all p-values are below
  some threshold.

Chapter 6 returns to this with regularization-based alternatives (lasso) that
are usually better.

**3. How well does the model fit?** RSE and R² generalize. With p predictors,

$$ \text{RSE} = \sqrt{\frac{\text{RSS}}{n - p - 1}}. $$

R² is still 1 − RSS/TSS, but it always increases when we add a variable, even
a useless one. We use **adjusted R²** (Chapter 6) for fair comparison across
models of different sizes.

**4. How accurate are predictions?** Two intervals to know:

- **Confidence interval** for E[Y | X = x]: how uncertain are we about the
  *average* response at this X? This captures sampling variability in β̂.
- **Prediction interval** for an individual Y at X = x: how uncertain are we
  about a *new observation*? This captures sampling variability in β̂ *plus*
  irreducible noise σ². Always wider than the confidence interval.

Mistaking one for the other is a frequent error.

## 3.3 Other Considerations

### Qualitative predictors

Suppose Xⱼ is gender (Male/Female). We **dummy-encode** it as a 0/1 indicator:

$$ x_{ij} = \begin{cases} 1 & \text{if person i is Female} \\ 0 & \text{otherwise.} \end{cases} $$

The coefficient βⱼ is the average difference in Y between Females and Males
holding all other predictors fixed. Males are the **baseline**.

For a categorical predictor with K levels, we create K − 1 dummies. The
omitted level is the baseline. Each coefficient is the difference between that
level and the baseline.

In R, `factor` variables are automatically dummy-encoded:
```r
fit <- lm(Balance ~ Gender + Student, data=Credit)
# R picks the alphabetically first level as baseline by default
# Reorder with: Credit$Gender <- relevel(Credit$Gender, ref="Female")
```

### Removing the additive assumption: interactions

The standard model assumes the effect of X₁ on Y is the same regardless of
X₂. In Advertising, this would mean a dollar of TV spend has the same impact
whether or not you also spend on radio. That's often false.

We capture interactions by adding a product term:

$$ Y = \beta_0 + \beta_1 X_1 + \beta_2 X_2 + \beta_3 (X_1 X_2) + \varepsilon. $$

Now ∂Y/∂X₁ = β₁ + β₃ X₂: the effect of X₁ depends on X₂. In Advertising, the
interaction TV × radio coefficient is positive and significant — there is real
**synergy**, where each medium amplifies the effect of the other.

**Hierarchical principle**: if you include an interaction X₁X₂, also include
the main effects X₁ and X₂, even if their individual p-values look big. This
keeps the model interpretable and prevents the interaction from absorbing
effects that should be on the main terms.

```r
lm(sales ~ TV * radio, data=Advertising)
# Equivalent to: lm(sales ~ TV + radio + TV:radio, data=Advertising)
```

Interactions can also be between a quantitative and a qualitative variable. In
the Credit dataset, regressing Balance on Income and Student status with an
interaction:

$$ \text{Balance} = \beta_0 + \beta_1\,\text{Income} + (\beta_2 + \beta_3\,\text{Income}) \cdot \text{Student}. $$

Students and non-students get different intercepts and different slopes.
Without the interaction they'd share the same slope.

### Non-linear relationships: polynomial regression

Sometimes a curved relationship is obvious. In `Auto`, mpg vs horsepower is
clearly not linear. Fit

$$ \text{mpg} = \beta_0 + \beta_1 \text{hp} + \beta_2 \text{hp}^2 + \varepsilon. $$

This is still a linear model — linear in the *coefficients*. We can use OLS as
usual.

```r
fit2 <- lm(mpg ~ poly(horsepower, 2), data=Auto)
fit5 <- lm(mpg ~ poly(horsepower, 5), data=Auto)
```

Higher-degree polynomials oscillate wildly at the boundaries (Runge's
phenomenon). Chapter 7 introduces splines, which handle this much better.

### Potential problems

Six issues to look for in any linear regression. Each has a diagnostic plot.

**1. Non-linearity of the response–predictor relationship.** Plot residuals
$e_i = y_i - \hat y_i$ against fitted values $\hat y_i$. If the truth is
linear, the residuals scatter randomly around 0. A clear curve in the plot
means the linear form is wrong. Fix: add polynomial terms, log-transform Y,
or use a nonlinear method (Chapter 7).

**2. Correlation of error terms.** Linear regression assumes the εᵢ are
independent. With time-series data they often aren't — error today predicts
error tomorrow. The standard errors will be too small, and your t-statistics
will be too big. Plot residuals in time order; tracking patterns are a
warning sign. Fix: time-series methods (autoregressive errors, generalized
least squares).

**3. Non-constant variance of error terms (heteroscedasticity).** A funnel-shaped
residual plot — residuals fanning out as ŷ grows. Standard errors are wrong.
Fix: transform Y by log or √, or use weighted least squares.

**4. Outliers.** A single observation with a wildly wrong yᵢ. We measure
"wildly wrong" by the **studentized residual**: the residual divided by its
estimated standard deviation. Studentized residuals beyond ±3 are suspect.

**5. High-leverage points.** Observations with unusual xᵢ. They can drag the
fitted line toward themselves. Measured by the **leverage statistic**

$$ h_i = \frac{1}{n} + \frac{(x_i - \bar x)^2}{\sum_{i'=1}^{n}(x_{i'} - \bar x)^2} $$

(simple regression), or hᵢ = i-th diagonal of $\mathbf X(\mathbf X^T \mathbf X)^{-1}\mathbf X^T$
(multiple regression). Average leverage is (p+1)/n. Anything much above is a
high-leverage point. Combined with a large residual, it's an **influential
observation**, often flagged with **Cook's distance**.

**6. Collinearity.** Two or more predictors are highly correlated. Standard
errors of their coefficients blow up; t-statistics shrink; you lose power to
detect their effects. Diagnostic: the **variance inflation factor**

$$ \text{VIF}(\hat\beta_j) = \frac{1}{1 - R^2_{X_j | X_{-j}}}, $$

where R²_{Xⱼ|X₋ⱼ} is the R² from regressing Xⱼ on all other predictors. VIF =
1 means no collinearity; VIF > 5 is concerning; VIF > 10 is serious. Fix: drop
one of the correlated variables, combine them (sum or average), or apply
ridge regression (Chapter 6) which handles collinearity gracefully.

## 3.4 The Marketing Plan Revisited

We can now answer the seven questions about Advertising.

1. **Is there a relationship?** F-statistic for the full model is 570 with a
   p-value < 0.0001. Yes.
2. **How strong?** R² = 0.897. Most of the variance in sales is explained.
3. **Which media?** TV and radio have very small p-values. Newspaper does not.
4. **How accurate are the effect estimates?** SE(β̂_TV) = 0.0014, so a 95% CI
   is roughly (0.043, 0.049). For radio, (0.172, 0.206). For newspaper,
   (−0.013, 0.011) — straddles zero, consistent with no effect.
5. **How accurate are predictions?** Use prediction intervals; in this dataset
   they are roughly ±2 RSE wide.
6. **Linear?** Diagnostic plots show some curvature. Adding TV×radio
   interaction substantially improves R² (to 0.968) and reduces residual
   curvature.
7. **Synergy?** Yes — the TV:radio interaction is highly significant.

## 3.5 Comparison with KNN

For the same dataset, how does KNN regression compare? Two findings:

- When the true f *is* approximately linear, linear regression is far better
  than KNN. Linear regression has lower variance because it uses all n points
  to estimate just p+1 numbers; KNN uses only K local points.
- When the true f is highly nonlinear, KNN beats linear regression *only* if
  p is small. For p > 4 or so the curse of dimensionality bites and even
  highly flexible KNN struggles.

The general lesson: parametric methods dominate in high dimensions because
they impose structure that beats the noise.

## 3.6 R Lab: Linear Regression

```r
library(ISLR2)
library(MASS)
library(car)

# Simple regression: medv (median home value) on lstat (% lower status)
attach(Boston)
fit <- lm(medv ~ lstat, data=Boston)
summary(fit)               # coefficients, SE, t, p, R², F
confint(fit)               # 95% CIs
predict(fit, data.frame(lstat=c(5,10,15)), interval="confidence")
predict(fit, data.frame(lstat=c(5,10,15)), interval="prediction")

plot(lstat, medv)
abline(fit, lwd=3, col="red")

# Diagnostics:
par(mfrow=c(2,2)); plot(fit)
plot(predict(fit), residuals(fit))
plot(predict(fit), rstudent(fit))   # studentized
plot(hatvalues(fit))
which.max(hatvalues(fit))           # most influential

# Multiple regression:
fit.all <- lm(medv ~ ., data=Boston)
summary(fit.all)
vif(fit.all)                        # collinearity check

# Drop a variable:
fit.minus <- update(fit.all, ~ . - age)

# Interaction:
summary(lm(medv ~ lstat * age, data=Boston))

# Polynomial:
summary(lm(medv ~ lstat + I(lstat^2), data=Boston))
summary(lm(medv ~ poly(lstat, 5), data=Boston))

# Qualitative:
data(Carseats)
fit.car <- lm(Sales ~ . + Income:Advertising + Price:Age, data=Carseats)
summary(fit.car)
contrasts(Carseats$ShelveLoc)       # how factor was dummy-coded
```

This is everything you need for a competent classical regression analysis. The
rest of the book is essentially: where does linear regression break, and what
do we do about it?

---

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

# Chapter 6 — Linear Model Selection and Regularization

The standard linear model from Chapter 3 fits with **ordinary least squares**.
That works well when n is much larger than p and the relationship is roughly
linear. It works *poorly* when:

1. **n is not much larger than p.** Variance of OLS estimates is large.
2. **p > n.** The matrix XᵀX is singular; OLS has no unique solution.
3. **You want a simpler model.** A model using all p predictors is hard to
   interpret and may include noise.

We'll improve OLS three ways:

1. **Subset selection** — pick a smaller set of predictors and fit OLS on them.
2. **Shrinkage (regularization)** — fit on all p but pull the coefficients
   toward zero. Reduces variance, may set some coefficients to exactly zero.
3. **Dimension reduction** — project the p predictors into a smaller M < p
   set of features and fit OLS on those.

Running example: **Hitters**, predicting baseball player Salary from 19
performance variables. After dropping rows with missing salary, we have
n = 263 and p = 19.

## 6.1 Subset Selection

### Best subset selection

Try all 2^p possible subsets:
1. Let M₀ be the null model (intercept only).
2. For k = 1, …, p, fit all C(p, k) models with k predictors. Pick the best
   (lowest RSS, equivalently highest R²) and call it M_k.
3. Pick among M₀, M₁, …, M_p using a method that adjusts for model size:
   adjusted R², C_p, AIC, BIC, or cross-validated error.

This is conceptually clean but computationally infeasible for p ≳ 30:
2³⁰ ≈ 10⁹ models. The Hitters example with p = 19 fits 2¹⁹ = 524,288 models —
fast on a laptop, but the next bigger problem is hopeless.

### Forward stepwise selection

Greedy approximation:
1. M₀ = null model.
2. For k = 0, …, p − 1:
   a. Among all p − k models that add one predictor to M_k, pick the one
      with smallest RSS. Call it M_{k+1}.
3. Pick among M₀, …, M_p with adjusted-for-size criterion.

Total fits: 1 + p + (p − 1) + … + 1 = 1 + p(p+1)/2. For p = 20 that's 211
fits, vs 1,048,576 for best subset.

Forward stepwise can miss the global optimum. Suppose the best 1-variable
model uses X₁, but the best 2-variable model uses {X₂, X₃}. Forward stepwise
forces the 2-variable model to include X₁. Still, it usually finds something
close.

### Backward stepwise selection

Symmetric: start with M_p (full model), at each step drop the variable whose
removal least increases RSS. Requires n > p (else can't even fit the full
model).

### Choosing the best of M₀, …, M_p

**Why not just use RSS or R² to pick?** Because they always favor the bigger
model. Adding any predictor — even a useless one — reduces RSS by at least a
tiny amount (it can't make it bigger).

We need an adjustment that penalizes complexity.

**Mallow's C_p**:
$$ C_p = \frac{1}{n} (\text{RSS} + 2 d \hat\sigma^2). $$
Here d is the number of predictors in the model and σ̂² is an estimate of the
noise variance, typically from the full model.

**Akaike Information Criterion (AIC)**:
$$ \text{AIC} = \frac{1}{n \hat\sigma^2}(\text{RSS} + 2 d \hat\sigma^2). $$
For OLS with Gaussian errors, AIC ∝ C_p — they pick the same model.

**Bayesian Information Criterion (BIC)**:
$$ \text{BIC} = \frac{1}{n}(\text{RSS} + \log(n) \cdot d \hat\sigma^2). $$
log(n) > 2 for n ≥ 8, so BIC penalizes complexity more heavily than C_p / AIC.
BIC tends to pick smaller models, especially when n is large.

**Adjusted R²**:
$$ R^2_{\text{adj}} = 1 - \frac{\text{RSS}/(n - d - 1)}{\text{TSS}/(n - 1)}. $$
Maximized when adding predictors stops paying for itself. Less rigorously
motivated than C_p, AIC, or BIC, but widely reported.

**Cross-validation** is the gold standard. With modern computing it is feasible
for nearly every problem and makes no distributional assumptions. C_p, AIC,
BIC are useful fallbacks when CV is too expensive.

For Hitters: best-subset selection identifies a 6-variable model by C_p
(crime, walks, hits, RBIs, years, error). BIC, more strict, prefers a
4-variable model. CV picks something between. All are reasonable.

## 6.2 Shrinkage Methods

Subset selection is a hard rule: a predictor is either in or out. Shrinkage
is softer: we penalize the *size* of coefficients, pulling them toward zero
without (necessarily) zeroing them out.

### Ridge regression

Minimize

$$ \text{RSS} + \lambda \sum_{j=1}^{p} \beta_j^2 = \sum_{i=1}^{n}(y_i - \beta_0 - \sum_{j=1}^{p} \beta_j x_{ij})^2 + \lambda \sum_{j=1}^{p} \beta_j^2. $$

The second term is the **shrinkage penalty** (also called the L² penalty).
λ ≥ 0 is a tuning parameter.

- λ = 0: penalty has no effect; we get OLS.
- λ → ∞: the penalty dominates; all β̂ⱼ are pulled toward 0.
- Intermediate λ: a compromise.

Note the sum starts at j = 1 — we do **not** penalize the intercept β₀.
Penalizing the intercept would force the predictions toward zero rather than
toward the mean.

Ridge regression has a closed-form solution:

$$ \hat\beta^R = (\mathbf X^T \mathbf X + \lambda \mathbf I)^{-1} \mathbf X^T \mathbf y. $$

Adding λI to XᵀX makes it always invertible — ridge works even when p > n.

**Why does ridge help?** It trades a bit of bias for a substantial reduction
in variance. The OLS estimator is unbiased; for moderate λ, ridge has small
bias but much smaller variance, so MSE is lower.

**Standardization is essential.** The ridge penalty is not scale-invariant: if
you measure a variable in dollars vs cents, its coefficient changes by 100×,
and the penalty applied changes by 10,000×. Always standardize predictors to
mean 0 and SD 1 before applying ridge. The `glmnet` package does this
automatically.

### The lasso

Replace the ℓ² penalty with an ℓ¹ penalty:

$$ \text{RSS} + \lambda \sum_{j=1}^{p} |\beta_j|. $$

The change from squares to absolute values has a dramatic consequence: the
lasso **sets some coefficients exactly to zero**. So lasso simultaneously
shrinks (like ridge) and selects (like subset selection).

**Geometric intuition.** Both ridge and lasso can be reformulated as
constrained minimization:

$$ \text{Ridge: min RSS subject to } \sum \beta_j^2 \le s. $$
$$ \text{Lasso: min RSS subject to } \sum |\beta_j| \le s. $$

The ridge constraint is a sphere; the lasso constraint is a cube with sharp
corners on the axes. The OLS solution sits somewhere in coefficient space.
The constrained minimum is where the contour of constant RSS first touches
the constraint region. For ridge, the touch is generic — almost never at
exactly βⱼ = 0. For lasso, the corners on the axes are likely contact points,
giving exact sparsity.

**When does lasso beat ridge, or vice versa?**
- If many predictors are useful with small effects: ridge wins.
- If only a few predictors are useful and the rest are noise: lasso wins.
- In practice, try both with CV and pick.

### Choosing λ

Cross-validation. Fit the ridge or lasso path over a grid of λ values, compute
CV error at each, choose the λ minimizing CV error (`lambda.min`) or the
largest λ within 1 SE (`lambda.1se`, the more parsimonious choice).

### Elastic net

A compromise: penalize $\alpha \sum |\beta_j| + (1-\alpha) \sum \beta_j^2$.
α = 0 is ridge, α = 1 is lasso, in between is **elastic net**. Useful when
predictors come in correlated groups: lasso tends to pick one and discard the
rest; elastic net keeps the group together while shrinking it.

## 6.3 Dimension Reduction Methods

A different strategy: build M < p new predictors as linear combinations of
the original p, then run OLS on these M new predictors. Two common ways to
construct the combinations.

### Principal components regression (PCR)

Step 1: do PCA on X (Chapter 12) to get principal components Z₁, …, Z_p.
Step 2: fit Y on Z₁, …, Z_M (the first M PCs, for some M chosen by CV).

PCs are constructed to capture variance in X, *ignoring* Y. The hope is that
the directions of greatest variance in X are also informative about Y. Often
true, sometimes not.

If we use M = p, PCR is identical to OLS. The benefit comes from M < p:
fewer parameters, lower variance.

### Partial least squares (PLS)

Like PCR but the components are constructed to capture variance in X *and*
correlation with Y. Each direction is chosen to maximize the covariance with
the residual response.

In theory PLS should beat PCR (it uses Y). In practice the difference is
usually small.

For both PCR and PLS, **standardize** X first.

### When dimension reduction beats shrinkage

Empirically, ridge regression usually performs at least as well as PCR or
PLS, especially when no small subset of variables is dominant. The shrinkage
methods are simpler to implement and diagnose. Use PCR/PLS mainly when you
suspect the true f depends on a few latent factors that are linear
combinations of many variables.

## 6.4 Considerations in High Dimensions

A high-dimensional problem has p comparable to or larger than n. Examples:
genomics, text, image features. The lessons:

1. **OLS fails.** When p ≥ n there is no unique OLS solution; even when
   n − p is small, OLS overfits dramatically. Subset selection, ridge, lasso,
   PCR all become essential.
2. **Cross-validation is critical.** With a flexible method and large p,
   training error is a terrible proxy for test error.
3. **Interpretation is tricky.** Two highly correlated predictors are nearly
   interchangeable. Lasso may pick one and zero the other, or alternate
   across CV folds. Don't read too much into "the lasso chose X₃".
4. **Multicollinearity is the norm.** With p ≫ n you can almost always find
   a perfect linear combination of predictors that fits the data exactly,
   which means identifiability of individual effects is gone. Inference on
   individual coefficients is essentially impossible without strong
   assumptions.

## 6.5 R Lab: Subset Selection, Ridge, Lasso

```r
library(ISLR2); library(leaps); library(glmnet)

data(Hitters)
Hitters <- na.omit(Hitters)   # drop rows with missing Salary

# Best subset:
regfit <- regsubsets(Salary ~ ., data=Hitters, nvmax=19)
res <- summary(regfit)
plot(res$cp, type="b"); which.min(res$cp)
plot(res$bic, type="b"); which.min(res$bic)
plot(res$adjr2, type="b"); which.max(res$adjr2)
coef(regfit, 6)              # 6-variable best-subset model

# Forward / backward:
regsubsets(Salary ~ ., data=Hitters, nvmax=19, method="forward")
regsubsets(Salary ~ ., data=Hitters, nvmax=19, method="backward")

# Choose with validation set:
set.seed(1)
train <- sample(c(TRUE, FALSE), nrow(Hitters), replace=TRUE)
test  <- !train
regfit <- regsubsets(Salary ~ ., data=Hitters[train, ], nvmax=19)
test.mat <- model.matrix(Salary ~ ., data=Hitters[test, ])
val.errors <- sapply(1:19, function(i) {
  coefi <- coef(regfit, id=i)
  pred  <- test.mat[, names(coefi)] %*% coefi
  mean((Hitters$Salary[test] - pred)^2)
})
which.min(val.errors)

# Ridge:
x <- model.matrix(Salary ~ ., Hitters)[, -1]
y <- Hitters$Salary

grid <- 10^seq(10, -2, length=100)
ridge.mod <- glmnet(x, y, alpha=0, lambda=grid)   # alpha=0 → ridge
plot(ridge.mod, xvar="lambda")

# CV for λ:
set.seed(1)
cv.out <- cv.glmnet(x, y, alpha=0)
plot(cv.out)
bestlam <- cv.out$lambda.min
predict(ridge.mod, s=bestlam, type="coefficients")[1:20, ]

# Lasso:
lasso.mod <- glmnet(x, y, alpha=1, lambda=grid)
plot(lasso.mod, xvar="lambda")
cv.out <- cv.glmnet(x, y, alpha=1)
bestlam <- cv.out$lambda.min
out <- glmnet(x, y, alpha=1, lambda=grid)
predict(out, type="coefficients", s=bestlam)[1:20, ]   # many zeros

# PCR / PLS:
library(pls)
set.seed(2)
pcr.fit <- pcr(Salary ~ ., data=Hitters, scale=TRUE, validation="CV")
summary(pcr.fit)
validationplot(pcr.fit, val.type="MSEP")

pls.fit <- plsr(Salary ~ ., data=Hitters, scale=TRUE, validation="CV")
summary(pls.fit)
validationplot(pls.fit, val.type="MSEP")
```

Chapter 6 fixed problems with OLS by changing what we *fit*. Chapter 7 will
fix problems by changing what kinds of *functions* we fit — moving beyond
linearity itself.

---

# Chapter 7 — Moving Beyond Linearity

The linear model is convenient but restrictive. The truth is rarely a perfect
straight line. Chapter 6 kept f linear and made the *coefficients* do less
work. Chapter 7 keeps the coefficients linear but lets *the basis* — the
features we feed in — do nonlinear work.

The running example: **Wage** data, 3,000 men from the central Atlantic
region, with wage as the response and age, education, year, and others as
predictors. The relationship between age and wage is clearly nonlinear:
wages rise from age 20 to about 40, plateau through middle age, and decline
after 60. A straight line is the wrong tool.

This chapter is a tour of methods that fit smooth nonlinear functions. The
key insight: every method here can be written as a *linear regression on
transformed features*, so all the machinery of Chapter 3 still applies.

## 7.1 Polynomial Regression

Fit Y on X, X², X³, …, X^d:

$$ Y = \beta_0 + \beta_1 X + \beta_2 X^2 + \cdots + \beta_d X^d + \varepsilon. $$

Still linear in the coefficients, so OLS works. The fitted function is a
degree-d polynomial. Choose d small (rarely above 4) — high-degree polynomials
swing wildly between data points and especially at the boundaries.

For the Wage data, a degree-4 polynomial fits well:

```r
fit <- lm(wage ~ poly(age, 4), data=Wage)
summary(fit)
```

Note: `poly()` in R returns *orthogonal* polynomials by default — linear
combinations of {1, X, X², X³, X⁴} that are uncorrelated. Coefficients change
but the fit and predictions are identical. Pass `raw=TRUE` for the standard
basis.

For classification (predicting whether wage > 250), use polynomial logistic
regression:

```r
glm(I(wage > 250) ~ poly(age, 4), data=Wage, family=binomial)
```

## 7.2 Step Functions

Cut the range of X at K points c₁ < c₂ < … < c_K, creating K+1 bins. Define
indicator features I(c_j ≤ X < c_{j+1}). Fit OLS on these. The result is a
piecewise-constant function — like a histogram smoothed onto a regression
problem.

Step functions are a building block but rarely the right final answer. They
miss within-bin trends, and the fitted function has hard discontinuities.

## 7.3 Basis Functions

Polynomial regression and step-function regression share a structure. Both
fit

$$ Y = \beta_0 + \beta_1 b_1(X) + \beta_2 b_2(X) + \cdots + \beta_K b_K(X) + \varepsilon, $$

where the b_k(X) are **basis functions** — fixed transformations of X. For
polynomial regression, b_k(X) = X^k. For step functions, b_k(X) = I(c_{k-1} ≤
X < c_k).

This is a powerful unification. Anything we can write as a linear combination
of basis functions can be fit with OLS. The art is choosing the right basis.

## 7.4 Regression Splines

Splines are the most popular nonlinear basis. They combine the smoothness of
polynomials with the locality of step functions.

### Piecewise polynomials

Divide the X range at **knots** ξ₁ < ξ₂ < … < ξ_K. In each interval, fit a
separate polynomial of degree d. With d = 3 (cubic), this is a piecewise
cubic.

The problem: the polynomial pieces don't connect smoothly at the knots. We
get a function that's continuous at best, with jumps in slope or curvature.

### Cubic splines: forced smoothness

A **cubic spline** with knots at ξ₁, …, ξ_K is a piecewise cubic that is
continuous at the knots, has continuous first derivative at the knots, and
has continuous second derivative at the knots. (Three constraints per knot,
making a 4(K+1)-parameter unconstrained piecewise cubic into a (4(K+1) − 3K)
= K + 4-parameter cubic spline.)

A cubic spline can be written using the **truncated power basis**:

$$ Y = \beta_0 + \beta_1 X + \beta_2 X^2 + \beta_3 X^3 + \sum_{j=1}^{K} \theta_j (X - \xi_j)_+^3 + \varepsilon, $$

where (X − ξ)₊³ = (X − ξ)³ if X > ξ and 0 otherwise. The first 4 terms give a
cubic; each truncated power adds a "bump" that allows the cubic to change
shape at ξ_j while preserving continuity of the function and its first two
derivatives.

In practice we use the more numerically stable **B-spline basis** (`bs()`).
The fit and predictions are the same; only the basis representation differs.

### Natural splines

Cubic splines have high variance at the boundaries of the data. The reason:
beyond the leftmost and rightmost knots there is no data, but the polynomial
extrapolates as a cubic, which can shoot off rapidly. **Natural splines**
add the constraint that the function is *linear* (not cubic) outside the
boundary knots. This trims 4 degrees of freedom and dramatically reduces
boundary variance.

```r
library(splines)
fit_bs <- lm(wage ~ bs(age, knots=c(25, 40, 60)), data=Wage)
fit_ns <- lm(wage ~ ns(age, df=4), data=Wage)
```

### Choosing knots

Two questions: how many, and where?

**How many**: more knots = more flexibility = lower bias but higher variance.
Use cross-validation. Or specify the degrees of freedom (df) and let R place
knots at the appropriate quantiles automatically: `bs(x, df=6)` puts knots at
the 25th, 50th, and 75th percentiles for a cubic spline.

**Where**: at quantiles of X (so each region has roughly equal data).
Specifying df = 6 gives 3 internal knots automatically.

### Splines vs polynomial regression

A degree-15 polynomial has 15 degrees of freedom and can wiggle anywhere. A
cubic spline with 12 knots also has 15 df but uses them *locally* — flexibility
is concentrated where it's needed. The result is much better behavior at the
boundaries and less spurious oscillation. Splines are almost always preferable
to high-degree polynomials.

## 7.5 Smoothing Splines

A different motivation: instead of choosing knots, what if we put knots
*everywhere* and apply a smoothness penalty?

The smoothing spline is the function g that minimizes

$$ \sum_{i=1}^{n} (y_i - g(x_i))^2 + \lambda \int g''(t)^2\, dt. $$

The first term wants a fit; the second penalizes "wiggliness" (the second
derivative measures curvature). λ ≥ 0 controls the trade-off:

- λ = 0: the minimizer interpolates every point — extremely wiggly.
- λ → ∞: the second term dominates; the only function with zero second
  derivative everywhere is a straight line, so we get OLS.

It is a remarkable fact that the minimizer is a **natural cubic spline with
knots at every unique x_i**. So smoothing splines have n knots in principle —
but the smoothness penalty pulls the function back toward simplicity.

The effective number of parameters is the **effective degrees of freedom**:

$$ \text{df}_\lambda = \text{trace}(S_\lambda), $$

where S_λ is the matrix mapping y to ŷ. Despite n knots, df_λ can be small
(say 4 to 10) for moderate λ. We choose λ — equivalently df_λ — by
cross-validation.

```r
fit <- smooth.spline(Wage$age, Wage$wage, cv=TRUE)
fit$df    # the effective df chosen by CV
```

## 7.6 Local Regression

At each target point x₀:
1. Find the s · n nearest training points to x₀ (the **span** s ∈ (0, 1]).
2. Assign weights to those points using a kernel: weight 1 at x₀, fading to
   0 at the boundary of the neighborhood.
3. Fit a weighted least squares regression (typically polynomial, often degree
   1 or 2) using just those points.
4. Set ŷ₀ to the value of the weighted fit at x₀.

Move x₀ across the range of X to trace out the smoother. Implemented in R as
`loess()`.

The span controls flexibility: small s = local, wiggly fit; large s = global,
smooth fit. Choose by CV.

Local regression generalizes to multiple predictors but suffers the curse of
dimensionality: in high dimensions every "local" neighborhood is essentially
the whole dataset.

## 7.7 Generalized Additive Models (GAMs)

How do we extend any of these one-dimensional smoothers to multiple
predictors? The cleanest answer is the **generalized additive model**:

$$ Y = \beta_0 + f_1(X_1) + f_2(X_2) + \cdots + f_p(X_p) + \varepsilon. $$

Each f_j is a smooth function of the j-th predictor — could be a polynomial,
a spline, a smoothing spline, a local regression, anything. The crucial
restriction is **additivity**: the effect of X_j on Y doesn't depend on the
other X's.

### Why additivity?

Two reasons:

**Interpretability.** With a GAM we can *plot each f_j separately* and see
how Y depends on X_j marginally. With a fully nonparametric multivariate
estimate of f(X₁, …, X_p) we can't.

**Statistical efficiency.** Avoiding the curse of dimensionality. Each one-
dimensional f_j needs only enough data to fit one function, regardless of p.
A truly nonparametric multidimensional fit needs exponentially more data.

The cost: GAMs miss interactions. If the effect of age on wage really does
depend on education, an additive model can't capture it. We can add explicit
interactions when needed: f₁(X₁) + f₂(X₂) + f₃(X₁, X₂) for a 2D smoother.

### Fitting GAMs

Software (the `gam` and `mgcv` packages in R) fits GAMs via **backfitting**:
iteratively refit each f_j on the residuals from all the other f's, until
convergence. Or, in `mgcv`, by penalized likelihood with automatic smoothness
selection.

```r
library(gam); library(mgcv)
gam.m1 <- gam(wage ~ s(year, df=4) + s(age, df=5) + education, data=Wage)
plot(gam.m1, se=TRUE, col="blue")     # one panel per smooth predictor

gam.m2 <- mgcv::gam(wage ~ s(year) + s(age) + education, data=Wage)
summary(gam.m2)
```

### GAMs for classification

Replace the linear predictor in logistic regression with an additive model:

$$ \log\!\left(\frac{p(X)}{1 - p(X)}\right) = \beta_0 + f_1(X_1) + \cdots + f_p(X_p). $$

```r
gam(I(wage > 250) ~ year + s(age, df=5) + education,
    data=Wage, family=binomial)
```

### Pros and cons of GAMs

**Pros:**
- More flexible than linear regression.
- Each f_j is one-dimensional → interpretable, plottable, statistically
  efficient.
- Drops easily into the GLM framework (works for binary, count, survival
  responses).

**Cons:**
- Restricted to additive functions; misses interactions unless explicitly
  modeled.
- Less flexible than tree-based methods (Chapter 8) for highly interactive
  problems.

## 7.8 R Lab: Non-linear Modeling

```r
library(ISLR2); library(splines); library(gam); library(mgcv)
attach(Wage)

# Polynomial:
fit <- lm(wage ~ poly(age, 4), data=Wage)
agelims <- range(age)
agegrid <- seq(agelims[1], agelims[2])
preds <- predict(fit, list(age=agegrid), se=TRUE)
plot(age, wage, pch=20, col="darkgrey")
lines(agegrid, preds$fit, lwd=2, col="blue")
matlines(agegrid, cbind(preds$fit + 2*preds$se.fit,
                         preds$fit - 2*preds$se.fit), lty=3, col="blue")

# ANOVA to choose degree:
fit1 <- lm(wage ~ age, data=Wage)
fit2 <- lm(wage ~ poly(age, 2), data=Wage)
fit3 <- lm(wage ~ poly(age, 3), data=Wage)
fit4 <- lm(wage ~ poly(age, 4), data=Wage)
anova(fit1, fit2, fit3, fit4)

# Step function:
table(cut(age, 4))
fit <- lm(wage ~ cut(age, 4), data=Wage)

# Splines:
fit_bs <- lm(wage ~ bs(age, knots=c(25, 40, 60)), data=Wage)
fit_ns <- lm(wage ~ ns(age, df=4), data=Wage)

# Smoothing spline:
fit_ss <- smooth.spline(age, wage, df=16)
fit_ss_cv <- smooth.spline(age, wage, cv=TRUE)
fit_ss_cv$df

# LOESS:
fit_loess <- loess(wage ~ age, span=0.2, data=Wage)

# GAM:
gam.m1 <- gam(wage ~ s(year, 4) + s(age, 5) + education, data=Wage)
par(mfrow=c(1,3))
plot(gam.m1, se=TRUE, col="blue")
anova(gam.m1, gam(wage ~ year + s(age, 5) + education, data=Wage),
      gam(wage ~ s(year, 4) + s(age, 5) + education, data=Wage))   # nested tests
```

We've now seen how to handle nonlinear effects of individual predictors. The
methods of Chapter 8 — trees, forests, boosting — handle nonlinearity *and*
interactions automatically, at the cost of less interpretability.

---

# Chapter 8 — Tree-Based Methods

A **decision tree** carves the predictor space into a set of rectangular
regions and predicts a constant in each region. It's the simplest non-linear
non-parametric method. By itself a tree is a weak predictor, but combining
many trees — through **bagging**, **random forests**, **boosting**, or
**BART** — gives some of the most accurate methods we have.

The geometric idea: start with the full space of X. Choose one predictor and
one threshold. Split the space into the points where that predictor is below
the threshold and the points where it's above. Now do the same to each piece.
Continue recursively, building a binary tree. At the leaves of the tree,
predict the average response (regression) or the majority class
(classification).

## 8.1 The Basics of Decision Trees

### Regression trees

For Hitters again — predict log Salary from Years and Hits — a tree might
say:

- If Years < 4.5, predict log salary 5.11.
- Else if Hits < 117.5, predict 6.00.
- Else predict 6.74.

Three regions, three predictions. The two predictors create a partition of
the (Years, Hits) plane into three rectangles. Within each rectangle, the
prediction is a constant (the mean of training y in that region).

### How the tree is built

We need to choose splits. Two questions: which predictor to split on, and at
what threshold?

The greedy answer: at each node, try every predictor and every possible
threshold. For each candidate split into regions R₁ and R₂, compute the
**RSS reduction**:

$$ \sum_{i \in R_1} (y_i - \bar y_{R_1})^2 + \sum_{i \in R_2} (y_i - \bar y_{R_2})^2. $$

Pick the split that minimizes this. Repeat in each child node.

The procedure is called **recursive binary splitting**. It is greedy: at each
step we make the locally best split, ignoring how this affects future splits.
Greedy isn't optimal, but it's fast and works well.

### When to stop?

Naively: stop when leaves are very small (say 5 observations). But a stopping
rule may halt the splitting before a worthwhile structure emerges later.
Better strategy: grow a deep tree, then **prune** it back.

### Cost-complexity (weakest-link) pruning

For a tree T, define

$$ \sum_{m=1}^{|T|} \sum_{i: x_i \in R_m} (y_i - \hat y_{R_m})^2 + \alpha |T|, $$

where |T| is the number of leaves. The penalty α |T| penalizes complexity. As
α grows, we're willing to merge leaves (collapse subtrees) to reduce |T|. As
α → 0, we get the original deep tree. As α → ∞, we get the root only.

For each α there is a unique smallest subtree that minimizes the criterion;
the path of pruned trees as α grows is computed efficiently. Choose α by
cross-validation.

### Classification trees

Same recursive splitting, different splitting criterion. For classification
we cannot use RSS. Two common alternatives:

**Gini index** in node m:
$$ G_m = \sum_{k=1}^{K} \hat p_{mk} (1 - \hat p_{mk}), $$
where p̂_{mk} is the fraction of class k in node m. G_m measures node
**impurity**: 0 if all observations in the node are one class, larger if the
classes are mixed.

**Entropy** in node m:
$$ D_m = -\sum_{k=1}^{K} \hat p_{mk} \log \hat p_{mk}. $$

Both are very similar in practice. Splits are chosen to minimize the weighted
average impurity in the children. (Why not classification error? It is less
sensitive to changes in node probabilities — Gini and entropy reward making
a node *more* pure even if the majority class doesn't change.)

For the final tree-pruning step we *can* use classification error, since by
then we just want to compare tree sizes.

### Trees: pros and cons

**Pros:**
- Very easy to explain to non-experts.
- A nice visual representation (tree diagram).
- Handle qualitative predictors and missing data without ado.
- No scaling needed.
- Automatically capture interactions (a deeper split implicitly conditions
  on the values of all predictors above it).

**Cons:**
- Predictive accuracy is generally lower than methods like ridge or random
  forests.
- Trees can be **non-robust**: a small change in the data can produce a
  completely different tree (a different split at the root cascades all
  the way down).

The non-robustness motivates the next sections. The cure for high-variance
methods is averaging many of them.

## 8.2 Bagging

**Bagging** = "bootstrap aggregation". The idea:

1. Draw B bootstrap samples from the training data.
2. Fit a deep, unpruned tree to each.
3. To predict at a new x: take the average prediction across the B trees
   (regression) or the majority vote (classification).

A single tree has high variance. The average of B independent trees has
variance σ²/B — much lower. Even though the bootstrap trees aren't fully
independent (they all see the same training data), averaging still reduces
variance dramatically.

Bagging works for any method, but it shines for trees because trees benefit
most from variance reduction.

### Out-of-bag error

Each bootstrap sample uses on average ~63.2% of the training points (since
P(any one point is in a sample) = 1 − (1 − 1/n)^n → 1 − 1/e ≈ 0.632). The
remaining ~36.8% are **out-of-bag (OOB)** — not used for fitting that tree.

For each training point i, we predict using only the trees where i was OOB.
Average those predictions. The resulting OOB error is an unbiased estimate of
test error, essentially for free. No need for a separate test set or
cross-validation. With B large, OOB error is essentially LOOCV.

### Variable importance

Bagging hides interpretability — there's no single tree to read. We recover
some interpretability with **variable importance** scores.

For each predictor X_j, sum the RSS reduction (regression) or Gini reduction
(classification) over all splits on X_j across all B trees. A large value
means X_j was used often or in important splits.

```r
library(randomForest)
bag.fit <- randomForest(medv ~ ., data=Boston, mtry=12, ntree=500,
                         importance=TRUE)
importance(bag.fit)
varImpPlot(bag.fit)
```

## 8.3 Random Forests

A subtle problem with bagging: if there is one strong predictor in the data,
nearly every bagged tree splits on it at the root. The B trees end up highly
correlated. Averaging correlated quantities reduces variance less than
averaging independent ones. Bagging captures only part of the available
variance reduction.

**Random forests** fix this by *decorrelating* the trees. At each split,
consider only a **random subset of m predictors** (m < p). Different splits
get different random subsets, so the strong predictor doesn't dominate at
the root every time. The trees now disagree more, average to a lower variance.

**Default m**:
- Regression: m = p/3.
- Classification: m = √p.

Tune m by CV if accuracy matters.

m = p reduces random forest to bagging. So bagging is a special case of
random forests, and there is essentially no reason to use bagging instead of
RF.

```r
rf.fit <- randomForest(medv ~ ., data=Boston, mtry=4, ntree=500,
                        importance=TRUE)
yhat <- predict(rf.fit, Boston.test)
mean((yhat - Boston.test$medv)^2)
importance(rf.fit)        # %IncMSE and IncNodePurity
varImpPlot(rf.fit)
```

Random forest tuning checklist:
- **mtry** (m): try a small grid. The default is usually fine.
- **ntree**: large enough that test error stabilizes. 500 is usually plenty.
- **nodesize**: minimum number of observations per leaf. Larger = smaller
  trees, smoother fit.

Random forests are remarkably robust: they often deliver excellent
performance with default settings.

## 8.4 Boosting

A different aggregation idea. Where bagging trains B trees independently and
averages them, **boosting** trains trees *sequentially*, each one focused on
the mistakes of the previous ensemble. The final prediction is a *weighted
sum* of all trees.

### Boosting algorithm for regression

1. Initialize f̂(x) = 0 and residuals r_i = y_i for all i.
2. For b = 1, 2, …, B:
   a. Fit a tree T̂^b with d splits to (X, r).
   b. Update f̂(x) ← f̂(x) + λ T̂^b(x).
   c. Update residuals: r_i ← r_i − λ T̂^b(x_i).
3. Output the boosted model: f̂(x) = Σ_{b=1}^B λ T̂^b(x).

Three tuning parameters:

- **B (number of trees)**: more is better up to a point; too many overfits.
  Choose by CV.
- **λ (learning rate / shrinkage)**: typically 0.01 or 0.001. Smaller λ
  means slower learning, requiring larger B, but usually higher final
  accuracy.
- **d (interaction depth)**: number of splits per tree. d = 1 makes "stumps"
  — each tree depends on a single variable, so the boosted model is purely
  additive (no interactions). d = 2 captures two-way interactions. Often
  d = 1 to 6 works well.

Each individual tree is **weak** (small d) and only slightly improves the
fit. Stacking many weak trees produces a strong learner. This is why
boosting is sometimes called "the strength of weak learners."

### Why boosting works

Bagging reduces variance. Boosting reduces bias *and* variance. Each new
tree is fit to the part of Y that the previous ensemble didn't explain
(the residuals), so the ensemble gradually approximates the true f. The
shrinkage λ keeps each step small to avoid overfitting.

### Boosting vs random forests

| | Random forests | Boosting |
|--|---------------|---------|
| Fitting | Independent (parallelizable) | Sequential |
| Tree depth | Deep | Shallow |
| Tunes | mtry, ntree | B, λ, d |
| Robust to tuning | Very | Less |
| Best with care | RF | Boosting |
| Out of the box | RF | XGBoost is competitive |

Boosting often slightly outperforms RF when carefully tuned. RF is often more
forgiving.

```r
library(gbm)
set.seed(1)
boost.fit <- gbm(medv ~ ., data=Boston[train, ],
                 distribution="gaussian",
                 n.trees=5000, interaction.depth=4,
                 shrinkage=0.01, cv.folds=5)
best.iter <- gbm.perf(boost.fit, method="cv")
yhat <- predict(boost.fit, Boston[-train, ], n.trees=best.iter)
mean((yhat - Boston$medv[-train])^2)
summary(boost.fit)        # variable importance
```

For binary classification, set `distribution="bernoulli"`.

## 8.5 Bayesian Additive Regression Trees (BART)

BART is the most recent of the tree ensemble methods. The model is

$$ Y = \sum_{b=1}^{B} T^b(X) + \varepsilon. $$

A sum of B trees, like boosting. But BART is **Bayesian**: it puts a prior
distribution over each tree (favoring small trees) and over the leaf values
(favoring small effects). Inference is by Markov chain Monte Carlo (MCMC):
we draw many posterior samples of the trees, giving a *distribution* over
predictions rather than a single point estimate.

Compared to boosting:

- BART has effectively no tuning (priors handle regularization).
- BART gives **uncertainty quantification**: posterior credible intervals on
  predictions for free.
- BART is slower (MCMC).

```r
library(BART)
x <- Boston[train, -14]
y <- Boston$medv[train]
xtest <- Boston[-train, -14]
set.seed(1)
bart.fit <- gbart(x, y, x.test=xtest)
yhat <- bart.fit$yhat.test.mean
mean((yhat - Boston$medv[-train])^2)
```

## 8.6 Summary

A hierarchy of tree methods, from worst to best in typical predictive
performance:

```
Single tree → Bagging → Random forest → Boosting / BART
```

But:
- A single tree is the most interpretable.
- Random forests are the most "set-and-forget".
- Boosting (especially XGBoost) often wins Kaggle competitions when tuned.
- BART is preferred for Bayesian uncertainty.

## 8.7 R Lab: Trees

```r
library(ISLR2); library(tree); library(randomForest); library(gbm); library(BART)

# A classification tree:
data(Carseats)
High <- factor(ifelse(Carseats$Sales <= 8, "No", "Yes"))
Carseats <- data.frame(Carseats, High)

tree.fit <- tree(High ~ . - Sales, data=Carseats)
summary(tree.fit)
plot(tree.fit); text(tree.fit, pretty=0)

# Cross-validate to choose tree size:
set.seed(7)
cv.tree.fit <- cv.tree(tree.fit, FUN=prune.misclass)
cv.tree.fit
plot(cv.tree.fit$size, cv.tree.fit$dev, type="b")
prune <- prune.misclass(tree.fit, best=9)

# Regression tree (Boston):
set.seed(1)
train <- sample(1:nrow(Boston), nrow(Boston)/2)
tree.fit <- tree(medv ~ ., data=Boston, subset=train)
plot(tree.fit); text(tree.fit, pretty=0)

# Random forest:
set.seed(1)
rf.fit <- randomForest(medv ~ ., data=Boston, subset=train,
                        mtry=6, importance=TRUE)
mean((predict(rf.fit, Boston[-train, ]) - Boston$medv[-train])^2)
importance(rf.fit)
varImpPlot(rf.fit)

# Boosting:
set.seed(1)
boost.fit <- gbm(medv ~ ., data=Boston[train, ],
                 distribution="gaussian", n.trees=5000,
                 interaction.depth=4, shrinkage=0.2, verbose=FALSE)
mean((predict(boost.fit, Boston[-train, ], n.trees=5000) -
      Boston$medv[-train])^2)
summary(boost.fit)

# BART:
x <- Boston[, -14]; y <- Boston$medv
xtrain <- x[train, ]; ytrain <- y[train]
xtest  <- x[-train, ]; ytest <- y[-train]
set.seed(1)
bart.fit <- gbart(xtrain, ytrain, x.test=xtest)
mean((bart.fit$yhat.test.mean - ytest)^2)
```

We end Chapter 8 with the strongest *out-of-the-box* methods in the book.
Chapter 9's SVMs offer a different angle — geometry rather than averaging.

---

# Chapter 9 — Support Vector Machines

The support vector machine is one of the great inventions of 1990s machine
learning. It was the dominant classifier from about 1995 to 2010, when deep
learning took over for problems with abundant data. SVMs remain excellent for
small-to-medium problems with structured features and were arguably the most
mathematically beautiful classifier ever devised.

There are three nested ideas, in increasing order of generality:

1. **Maximal margin classifier**: works only when classes are linearly
   separable. Geometric intuition.
2. **Support vector classifier (SVC)**: linear, but allows misclassifications.
3. **Support vector machine (SVM)**: SVC plus the **kernel trick** for
   nonlinear boundaries.

We build them up in order.

## 9.1 Maximal Margin Classifier

### Hyperplanes

In p dimensions a **hyperplane** is a flat affine subspace of dimension p − 1.
In 2D it's a line. In 3D it's a plane. The defining equation is

$$ \beta_0 + \beta_1 X_1 + \beta_2 X_2 + \cdots + \beta_p X_p = 0. $$

A hyperplane separates ℝ^p into two halves. The sign of β₀ + Σ β_j x_j tells
you which side a given x is on, and its magnitude (divided by ||β||) tells
you how far from the hyperplane.

### Classification using a separating hyperplane

Code Y as ±1. We want a hyperplane such that

$$ y_i (\beta_0 + \sum_{j} \beta_j x_{ij}) > 0 \quad \text{for all } i. $$

(Positives are on the positive side, negatives on the negative side.) This is
the **separating hyperplane**. There can be infinitely many of them.

### The maximal margin classifier

Choose the separating hyperplane that is **farthest from any training point**.
For a candidate hyperplane, the **margin** is the smallest perpendicular
distance from any training point to the hyperplane. The maximal margin
classifier maximizes this margin.

It can be written as the optimization

$$ \max_{\beta_0, \beta_1, \ldots, \beta_p,\, M} M \quad \text{subject to} \quad
   \sum_{j=1}^{p} \beta_j^2 = 1, \quad y_i \left(\beta_0 + \sum_{j} \beta_j x_{ij}\right) \ge M \,\,\forall i. $$

The training points that lie *exactly* on the margin boundary are the
**support vectors**. They are the only points that determine the hyperplane;
moving any other point (without crossing the margin) doesn't change the
optimal hyperplane at all. The classifier depends on a small fraction of the
data — a kind of natural sparsity.

When the data are linearly separable, the maximal margin classifier exists
and is unique. When they aren't, no separating hyperplane exists, and we need
something more flexible.

## 9.2 Support Vector Classifier

Even when classes are nearly linearly separable, the maximal margin
classifier can be undesirable. A single noisy point near the boundary can
swing the hyperplane wildly. We want to allow some points to be on the wrong
side of the margin (or even the wrong side of the hyperplane), in exchange
for a more robust fit.

The SVC formulation:

$$ \max_{\beta_0, \beta_1, \ldots, \beta_p,\, \varepsilon_1, \ldots, \varepsilon_n,\, M} M \quad \text{subject to} $$
$$ \sum_{j=1}^{p} \beta_j^2 = 1, $$
$$ y_i \left(\beta_0 + \sum_{j} \beta_j x_{ij}\right) \ge M (1 - \varepsilon_i), \quad \varepsilon_i \ge 0, \quad \sum_{i=1}^{n} \varepsilon_i \le C. $$

The **slack variables** ε_i measure violations:
- ε_i = 0: i is on the correct side of the margin (good).
- 0 < ε_i ≤ 1: i is inside the margin but on the correct side of the
  hyperplane.
- ε_i > 1: i is on the wrong side of the hyperplane (misclassified).

C is the **budget** for total slack — a tuning parameter. Larger C =
broader budget, wider margin, more tolerance for violations. Smaller C =
strict, narrower margin.

This is one of the very few cases where "larger tuning parameter = wider
margin = lower complexity" cuts opposite to most regularization parameters
(λ in ridge, where larger means more regularization). In `sklearn` and
many other implementations the parameter is reversed — the user-facing C is
the inverse of this textbook C. Read the docs.

### Support vectors in the SVC

Training points with ε_i > 0 (on or inside the margin) are the support
vectors. The classifier depends only on them. Increase C → more SVs → more
robust (averaging effect) but possibly higher bias.

### Bias–variance via C

- Small C: narrow margin, few SVs, low bias, high variance. Each prediction
  rests on only a handful of points.
- Large C: wide margin, many SVs, higher bias, lower variance.

Choose C by cross-validation.

## 9.3 Support Vector Machines

The SVC has a **linear** decision boundary. What if the truth is curved?
We can manually add nonlinear features (X², X³, X·Z, etc.) and apply SVC in
the enlarged feature space. The boundary is linear in the enlarged space and
nonlinear in the original space.

The SVM does this automatically and elegantly via the **kernel trick**.

### Inner products and the dual formulation

It turns out the SVC optimization can be solved equivalently in terms of
**inner products** of pairs of training points. Specifically, the linear
classifier can be written as

$$ f(x) = \beta_0 + \sum_{i=1}^{n} \alpha_i \langle x, x_i \rangle, $$

where the α_i are coefficients (most are zero — only the support vectors
have nonzero α_i), and ⟨x, x_i⟩ is the standard inner product Σ_j x_j x_{ij}.

### Generalizing the inner product: kernels

A **kernel function** K(x, x′) is a function that measures similarity
between points. Two examples beyond the standard inner product:

**Polynomial kernel** of degree d:
$$ K(x, x') = \left(1 + \sum_{j} x_j x'_j\right)^d. $$
Equivalent to fitting an SVC in a (high-dimensional) space of polynomial
features up to degree d, but without ever computing those features.

**Radial basis function (RBF) kernel**:
$$ K(x, x') = \exp\!\left(-\gamma \sum_{j} (x_j - x'_j)^2\right). $$
With γ > 0. The RBF kernel measures local similarity: K is close to 1 for
nearby points and decays exponentially with distance.

The SVM's predictor becomes

$$ f(x) = \beta_0 + \sum_{i \in S} \alpha_i K(x, x_i), $$

a linear combination of similarities to support vectors. We never explicitly
compute the high-dimensional feature space; we just plug a kernel into the
formula. This is the **kernel trick**.

### RBF kernel — geometric intuition

For RBF, K(x, x_i) is large only when x is close to x_i (in the original
space). So f(x) is essentially "majority vote of nearby support vectors" with
distance-weighted voting. Two parameters tune the result:

- **γ** (gamma): controls *locality*. Large γ → narrow influence of each
  support vector → wiggly, very local boundary → high variance.
- **C**: controls margin width as before.

Both must be tuned, usually by grid search with cross-validation.

### SVMs with > 2 classes

SVMs are inherently binary. Two strategies for K > 2 classes:

- **One-vs-one**: fit C(K, 2) classifiers, one for each pair. Classify x by
  the class winning the most pairwise contests.
- **One-vs-all**: fit K classifiers, each separating class k from the rest.
  Classify x to the class with the largest f_k(x).

`e1071`'s `svm()` uses one-vs-one by default.

## 9.4 SVMs vs Logistic Regression

There is a deeper connection. The SVC objective can be rewritten as

$$ \min_{\beta} \sum_{i=1}^{n} \max[0, 1 - y_i (\beta_0 + \sum_{j} \beta_j x_{ij})] + \lambda \sum_{j} \beta_j^2. $$

The first term is the **hinge loss**. The second is a ridge penalty. With
just λ replaced by 1/C, we have a regularized linear classifier with hinge
loss. Logistic regression, on the other hand, minimizes

$$ \sum_{i=1}^{n} \log(1 + e^{-y_i (\beta_0 + \sum_{j} \beta_j x_{ij})}) + \lambda \sum_{j} \beta_j^2. $$

Hinge loss vs logistic loss. They are nearly identical in shape (both are
convex, both penalize misclassifications and small margins). For most
problems SVMs and logistic regression give similar results. Logistic
regression provides probability estimates; SVMs provide a classifier with
a clear margin interpretation.

When to use SVM:
- Classes are clearly separable, hard boundary desired.
- High-dimensional p (text classification with thousands of features).
- Kernel structure (e.g., string kernels for sequences) makes geometric sense.

When to use logistic regression:
- You need calibrated probabilities, not just class labels.
- Inference / interpretation matters.

## 9.5 R Lab: Support Vector Machines

```r
library(e1071); library(ISLR2)

# Two-class linear SVC:
set.seed(1)
x <- matrix(rnorm(20*2), ncol=2)
y <- c(rep(-1, 10), rep(1, 10))
x[y == 1, ] <- x[y == 1, ] + 1
dat <- data.frame(x=x, y=as.factor(y))

svc.fit <- svm(y ~ ., data=dat, kernel="linear", cost=10, scale=FALSE)
plot(svc.fit, dat)
svc.fit$index           # indices of support vectors

# Tune C:
set.seed(1)
tune.out <- tune(svm, y ~ ., data=dat, kernel="linear",
                 ranges=list(cost=c(0.001, 0.01, 0.1, 1, 5, 10, 100)))
summary(tune.out)
best <- tune.out$best.model

# Nonlinear (radial) kernel:
set.seed(1)
x <- matrix(rnorm(200*2), ncol=2)
x[1:100, ] <- x[1:100, ] + 2
x[101:150, ] <- x[101:150, ] - 2
y <- c(rep(1, 150), rep(2, 50))
dat <- data.frame(x=x, y=as.factor(y))

svm.fit <- svm(y ~ ., data=dat, kernel="radial", cost=1, gamma=1)
plot(svm.fit, dat)

# Tune C and gamma:
set.seed(1)
tune.out <- tune(svm, y ~ ., data=dat, kernel="radial",
                 ranges=list(cost=c(0.1, 1, 10, 100, 1000),
                             gamma=c(0.5, 1, 2, 3, 4)))
summary(tune.out)
table(true=dat$y, pred=predict(tune.out$best.model, dat))

# ROC curves:
library(pROC)
fitted.values <- attributes(predict(svm.fit, dat, decision.values=TRUE))$decision.values
roc(dat$y, fitted.values)

# Multi-class (Khan dataset, 4 classes):
data(Khan)
dat <- data.frame(x=Khan$xtrain, y=as.factor(Khan$ytrain))
out <- svm(y ~ ., data=dat, kernel="linear", cost=10)
table(out$fitted, dat$y)
dat.te <- data.frame(x=Khan$xtest, y=as.factor(Khan$ytest))
table(predict(out, newdata=dat.te), dat.te$y)
```

The SVM is the last "shallow" supervised method in the book. Chapter 10 turns
to deep learning.

---

# Chapter 10 — Deep Learning

Neural networks have a long history (the perceptron is from 1958), but they
became dominant only in the 2010s, when three things converged: large labeled
datasets, GPU computing, and clever regularization tricks (dropout, batch
norm, ReLU). Today, deep learning is the default for problems with
**unstructured** data — images, audio, text, video — and increasingly for
tabular data as well.

This chapter develops the ideas from a standard regression starting point,
shows the modern building blocks (convolution, recurrence, transformers), and
walks through three labs: a fully connected network for tabular data, a CNN
for image classification, and an RNN for time series.

## 10.1 Single Layer Neural Networks

Start with a single hidden layer. With p inputs X₁, …, X_p and K hidden units:

$$ A_k = h_k(X) = g\left(w_{k0} + \sum_{j=1}^{p} w_{kj} X_j\right), \quad k = 1, \ldots, K, $$
$$ f(X) = \beta_0 + \sum_{k=1}^{K} \beta_k A_k. $$

The hidden activations A_k are nonlinear transformations of linear
combinations of X. The output f(X) is a linear combination of the hidden
activations. The activation function g is the only thing keeping this from
being a complicated linear model.

### Activation functions

**Sigmoid**: g(z) = 1/(1 + e^{−z}). Smooth S-shape from 0 to 1. Used in early
networks but has the **vanishing gradient** problem: outside ±5 or so the
gradient is essentially zero, so backpropagation can't update earlier layers.

**Hyperbolic tangent (tanh)**: g(z) = (e^z − e^{−z})/(e^z + e^{−z}). Like
sigmoid but ranges from −1 to 1. Slightly better than sigmoid because it's
zero-centered.

**Rectified Linear Unit (ReLU)**: g(z) = max(0, z). The default for hidden
layers in modern networks. Cheap to compute, no vanishing gradient on the
positive side, and induces sparsity (many activations are zero). The
"dying ReLU" problem — neurons stuck at 0 — is mitigated by **leaky ReLU**
(g(z) = max(αz, z) for small α).

**Softmax** (for K-class classification output):
$$ f_k(X) = \frac{e^{Z_k}}{\sum_{l=1}^{K} e^{Z_l}}, \quad Z_k = \beta_{k0} + \sum_{m} \beta_{km} A_m. $$
The softmax outputs sum to 1 and can be interpreted as probabilities.

**Linear** (for regression output): no transformation, ŷ = Z.

### Universal approximation

A single-hidden-layer network with enough hidden units can approximate any
continuous function arbitrarily well. So why bother with deep networks?
Because "enough" is often astronomical. **Depth is a far more efficient way
to add capacity than width**: a deep network with modest width can represent
functions that would require an exponentially wide shallow network.

## 10.2 Multilayer Neural Networks

Stack L hidden layers. Layer ℓ has K_ℓ units. The activations are

$$ A^{(\ell)}_k = g\left(w^{(\ell)}_{k0} + \sum_{j=1}^{K_{\ell-1}} w^{(\ell)}_{kj} A^{(\ell-1)}_j\right), $$

with A^{(0)} = X (the input). The output is f(X) = β_0 + Σ_k β_k A^{(L)}_k for
regression, or a softmax over the final layer for classification.

### Why deep?

In a CNN trained on images, layer 1 learns simple features (edges, color
blobs), layer 2 combines those into textures, layer 3 into object parts,
deeper layers into objects. Each layer composes features from the previous
layer. This **hierarchical composition** is what gives deep networks their
power. Empirically, deeper networks (with appropriate regularization) almost
always beat shallow ones.

### Training: stochastic gradient descent and backpropagation

The loss for a regression network is

$$ L = \frac{1}{n} \sum_{i=1}^{n} (y_i - f(x_i))^2. $$

For classification (with softmax) the loss is **cross-entropy**:

$$ L = -\frac{1}{n} \sum_{i=1}^{n} \sum_{k=1}^{K} y_{ik} \log f_k(x_i), $$

where y_{ik} = 1 if observation i is in class k, else 0.

Total parameters in the network: tens of thousands to billions. Gradient
descent: compute ∂L/∂w for every weight w, take a small step in the negative
direction. Repeat until convergence (or until you stop improving).

**Backpropagation** computes the gradient efficiently. Forward pass:
compute activations layer by layer. Backward pass: apply the chain rule from
the output back to the inputs, accumulating partial derivatives. The
algorithm is O(network size) per training example — same as the forward
pass.

**Stochastic gradient descent (SGD)**: instead of computing the gradient on
the full dataset, use a **mini-batch** (32, 64, 128 observations). The
mini-batch gradient is a noisy estimate of the full gradient, and the noise
acts as implicit regularization.

One full pass through the dataset is an **epoch**. Typical training: 50 to
1,000 epochs.

**Adam optimizer**: an adaptive variant of SGD that keeps per-parameter
running averages of gradients and squared gradients. Excellent default.

### Regularization

**Weight decay**: add λ Σ w² to the loss (ridge regularization on weights).

**Dropout**: during training, randomly set a fraction (e.g., 0.5) of
activations to 0. The network can't rely on any one neuron, so it learns
redundant representations — equivalent to averaging an ensemble of thinned
networks. At test time, all neurons fire and weights are scaled.

**Early stopping**: monitor validation loss; halt training when it plateaus
or starts climbing. Implicit regularization that costs nothing.

**Batch normalization**: normalize each layer's activations within each
mini-batch to mean 0 and SD 1, then learn a scale and shift. Stabilizes
training, allows higher learning rates, and provides mild regularization.

**Data augmentation** (for images): random crops, flips, rotations, color
jitter. Vastly expands the effective training set without new labels.

## 10.3 Convolutional Neural Networks

For image data, the input is a 3D tensor (height × width × channels). A
fully connected network would treat each pixel as an independent feature —
ignoring the spatial structure. Two key ideas make convolutional networks
the right choice:

**Local connectivity**: a neuron in layer 1 sees only a small spatial
neighborhood of the input. (Edges and textures are local.)

**Parameter sharing**: the same set of weights is applied across all spatial
positions. (An edge detector should work the same way wherever the edge is.)

### The convolutional layer

A **filter** (or kernel) is a small (e.g., 3 × 3 × C_{in}) tensor of weights.
The convolution operation slides the filter across the input, computing dot
products at each position. The result is a 2D **feature map** of activations.

A typical conv layer has many filters (32, 64, 128, …). Each filter
produces a feature map; together they form a 3D output (H × W × #filters).

Key parameters:
- **Filter size**: usually 3 × 3 or 5 × 5.
- **Number of filters**: 32 to 512 or more.
- **Stride**: how far the filter slides between positions. Stride 1 is
  default; stride 2 halves spatial dimensions.
- **Padding**: zero-pad the edges so the filter can apply at boundary
  positions.

### Pooling

A **max-pooling** layer takes the maximum of small (e.g., 2 × 2) regions in
each feature map, halving spatial dimensions. Reduces compute and creates a
small amount of translation invariance: a slight shift in the input leaves
the pooled output unchanged.

### Typical CNN architecture

```
Input (32×32×3)
→ Conv (3×3, 32 filters) → ReLU → BatchNorm
→ Conv (3×3, 64 filters) → ReLU → BatchNorm → MaxPool (2×2)
→ Conv (3×3, 128 filters) → ReLU → BatchNorm → MaxPool (2×2)
→ Flatten
→ Dense (256) → ReLU → Dropout(0.5)
→ Dense (10) → Softmax
```

Famous architectures:
- **LeNet-5** (1998): the original handwritten-digit network.
- **AlexNet** (2012): broke ImageNet; popularized ReLU and dropout.
- **VGG-16** (2014): proves you can go deep with simple 3×3 conv blocks.
- **ResNet** (2015): residual / skip connections allow networks of 100+
  layers to train. Almost universal today.
- **Vision Transformer (ViT)** (2020): replaces convolution with attention.
  Surpasses CNNs on large image datasets.

## 10.4 Recurrent Neural Networks

Sequence data (text, time series, speech) has temporal structure. A
**recurrent neural network (RNN)** maintains a hidden state that summarizes
everything it has seen so far.

### Basic RNN

At time step t with input x_t and hidden state h_{t−1}:

$$ h_t = g(W_h h_{t-1} + W_x x_t + b), $$
$$ o_t = W_o h_t + b_o. $$

The hidden state h_t is a learned summary of x_1, …, x_t. Output o_t depends
on h_t. The same weights W_h, W_x, W_o are used at every time step
(parameter sharing across time).

### Vanishing and exploding gradients

Backpropagating through many time steps repeatedly multiplies the same weight
matrix. If its largest eigenvalue is < 1, gradients vanish; > 1, they
explode. Either way, the network can't learn long-range dependencies.

### LSTM and GRU

The **Long Short-Term Memory (LSTM)** unit (Hochreiter & Schmidhuber, 1997)
introduces a separate **cell state** c_t alongside the hidden state h_t. Three
sigmoid **gates** decide what to add to the cell state, what to remove, and
what to expose:

- **Forget gate** f_t: decides what to discard from the previous cell state.
- **Input gate** i_t: decides what new information to add.
- **Output gate** o_t: decides what cell state info to output as h_t.

The cell state has a more direct path through time, with element-wise
gating, so gradients flow with much less attenuation. LSTMs handle
dependencies hundreds of time steps long.

The **Gated Recurrent Unit (GRU)** is a slightly simpler alternative with
two gates and no separate cell state. Often as good as LSTM in practice.

### Applications

- **Text classification, sentiment analysis** (IMDb): embed words into
  vectors, run an LSTM, feed the final hidden state into a dense classifier.
- **Translation, summarization**: encoder–decoder RNNs.
- **Time series forecasting**: predict next value from past sequence.

### Transformers

In 2017 the **transformer** architecture replaced recurrence with **self-
attention** — a mechanism that lets every position in a sequence directly
attend to every other position. Transformers are fully parallelizable
(no sequential time steps), train faster, and capture long-range
dependencies better than RNNs. They power modern NLP (BERT, GPT, T5) and
increasingly vision and biology.

ISLR v2 covers RNNs/LSTMs in depth and discusses transformers briefly.

## 10.5 When to Choose Deep Learning

Deep learning is the default for **unstructured** data: images, audio, text,
video. For these problems no other family comes close.

For **structured (tabular)** data, deep learning is often *worse* than
gradient boosting (XGBoost, LightGBM). Trees are excellent at handling
heterogeneous features, missing data, mixed types — exactly the things
tabular data has. Deep learning needs careful feature engineering to be
competitive.

A practical rule:
- Lots of structured data: try gradient boosting first.
- Lots of unstructured data: try deep learning.
- Modest data of any kind: lean on the simpler methods of Chapters 3–9.

## 10.6 Fitting a Neural Network

In R, the `keras3` package wraps Google's Keras (which sits on TensorFlow).
Setup is non-trivial but worthwhile.

```r
library(keras3)
# Once: install_keras()  # creates Python environment
```

### Tabular network on Hitters

```r
library(ISLR2); library(keras3)
Gitters <- na.omit(Hitters)

n <- nrow(Gitters)
set.seed(13)
ntest <- trunc(n / 3)
testid <- sample(1:n, ntest)

x <- scale(model.matrix(Salary ~ . - 1, data=Gitters))
y <- Gitters$Salary

modnn <- keras_model_sequential() |>
  layer_dense(units=50, activation="relu", input_shape=ncol(x)) |>
  layer_dropout(rate=0.4) |>
  layer_dense(units=1)

modnn |> compile(loss="mse",
                 optimizer=optimizer_rmsprop(),
                 metrics=list("mean_absolute_error"))

history <- modnn |> fit(x[-testid, ], y[-testid],
                         epochs=1500, batch_size=32,
                         validation_data=list(x[testid, ], y[testid]),
                         verbose=0)

plot(history)
preds <- modnn |> predict(x[testid, ])
mean(abs(y[testid] - preds))
```

### CNN on CIFAR100

```r
cifar100 <- dataset_cifar100()
x_train <- cifar100$train$x / 255
g_train <- to_categorical(cifar100$train$y, 100)
x_test  <- cifar100$test$x / 255
g_test  <- to_categorical(cifar100$test$y, 100)

model <- keras_model_sequential() |>
  layer_conv_2d(filters=32, kernel_size=c(3,3), padding="same",
                activation="relu", input_shape=c(32,32,3)) |>
  layer_max_pooling_2d(pool_size=c(2,2)) |>
  layer_conv_2d(filters=64, kernel_size=c(3,3), padding="same",
                activation="relu") |>
  layer_max_pooling_2d(pool_size=c(2,2)) |>
  layer_conv_2d(filters=128, kernel_size=c(3,3), padding="same",
                activation="relu") |>
  layer_max_pooling_2d(pool_size=c(2,2)) |>
  layer_conv_2d(filters=256, kernel_size=c(3,3), padding="same",
                activation="relu") |>
  layer_max_pooling_2d(pool_size=c(2,2)) |>
  layer_flatten() |>
  layer_dropout(rate=0.5) |>
  layer_dense(units=512, activation="relu") |>
  layer_dense(units=100, activation="softmax")

model |> compile(loss="categorical_crossentropy",
                 optimizer=optimizer_rmsprop(),
                 metrics="accuracy")

history <- model |> fit(x_train, g_train,
                         epochs=30, batch_size=128,
                         validation_split=0.2)
```

### RNN on IMDb sentiment

```r
imdb <- dataset_imdb(num_words=10000)
x_train <- imdb$train$x; y_train <- imdb$train$y
x_test  <- imdb$test$x;  y_test  <- imdb$test$y

x_train <- pad_sequences(x_train, maxlen=500)
x_test  <- pad_sequences(x_test,  maxlen=500)

model <- keras_model_sequential() |>
  layer_embedding(input_dim=10000, output_dim=32) |>
  layer_lstm(units=32) |>
  layer_dense(units=1, activation="sigmoid")

model |> compile(optimizer="rmsprop",
                 loss="binary_crossentropy",
                 metrics="accuracy")
history <- model |> fit(x_train, y_train,
                         epochs=10, batch_size=128,
                         validation_split=0.2)
```

## 10.7 Practical Wisdom

- **Start small**: shallow network, sensible defaults, get a baseline.
- **Standardize inputs**: zero mean, unit variance.
- **Adam optimizer** is a great default. Learning rate 1e-3 to start.
- **Use early stopping** with patience 5–20 epochs.
- **Monitor training and validation loss** every epoch. Diverging curves
  signal overfitting.
- **GPU** (or Apple Silicon): essential for anything beyond toy networks.
- **Reproducibility**: set random seeds in R, NumPy, and the deep learning
  framework. Even then, GPU non-determinism can cause small variations.

Deep learning is its own art. ISLR's chapter is a starting point; specialized
texts (Goodfellow/Bengio/Courville, *Deep Learning*; Chollet, *Deep Learning
with R/Python*) go further.

---

# Chapter 11 — Survival Analysis and Censored Data

Survival analysis is for time-to-event data. The "event" might be death,
mechanical failure, customer churn, default, recurrence of disease — anything
that happens at a specific moment. The defining feature is **censoring**:
some observations did not experience the event by the end of the study, so we
know only that their event time is *greater than* some value.

Standard regression cannot handle censoring. If we drop censored observations,
we throw away most of the information; if we treat censoring time as event
time, we systematically underestimate survival.

Examples in the book:
- **BrainCancer**: time from diagnosis to death for 88 brain cancer patients.
  35 died during follow-up; 53 were still alive at the end.
- **Publication**: time from submission to publication for medical research
  papers, with censoring for unpublished work.

## 11.1 Survival and Censoring Times

Each subject has:
- **T**: the true event time (might not be observed).
- **C**: the censoring time (when we stop observing).
- **Y = min(T, C)**: the observed time.
- **δ**: the indicator that we saw the event (δ = 1 if T ≤ C, else 0).

The data we see is (Y_i, δ_i), i = 1, …, n.

For valid analysis, we assume censoring is **independent** of the event
process — censoring time C contains no information about T. Random dropout
satisfies this; loss-to-follow-up because of getting sicker does not.

## 11.2 The Kaplan-Meier Survival Curve

The **survival function** is

$$ S(t) = P(T > t). $$

S(t) starts at 1 and monotonically decreases to 0. We want to estimate it
from censored data.

The **Kaplan-Meier estimator** does so without any parametric assumption.
Sort the unique event times t_1 < t_2 < …. At each event time t_k:

- d_k = number of events at time t_k.
- r_k = number of subjects "at risk" just before t_k (those still being
  followed and not yet event-experienced).

The estimated probability of surviving past t_k, *given* survival up to t_k,
is (r_k − d_k)/r_k. Multiplying these conditional probabilities:

$$ \hat S(t) = \prod_{k:\, t_k \le t} \frac{r_k - d_k}{r_k}. $$

Censored observations contribute to r_k for as long as they're observed, but
never appear in any d_k. They are "at risk" but never experience the event
in the data.

Ŝ(t) is a step function that drops at each event time.

```r
library(survival); library(ISLR2)
fit.km <- survfit(Surv(time, status) ~ 1, data=BrainCancer)
plot(fit.km, xlab="Months", ylab="Estimated S(t)")
```

### Comparing groups

To compare survival curves across groups (e.g., treatment vs control), use
the **log-rank test**:

$$ \text{Test statistic} = \frac{(\sum_{k} (d_{1k} - E_{1k}))^2}{\sum_{k} V_{1k}}, $$

where d_{1k} is the number of events in group 1 at time k, E_{1k} is the
expected number under the null (no group effect), and V_{1k} is the variance.
Under H₀: same survival curves, the statistic is approximately χ²₁.

```r
survdiff(Surv(time, status) ~ sex, data=BrainCancer)
```

## 11.3 Regression Models with a Survival Response

We want to model how T depends on covariates X. The challenge: T is censored.
The most common solution is the proportional hazards model.

### The hazard function

Define the **hazard function**:

$$ h(t) = \lim_{\Delta t \to 0} \frac{P(t \le T < t + \Delta t \mid T \ge t)}{\Delta t}. $$

Intuitively: among those still at risk at time t, h(t) is the instantaneous
rate of events. The hazard, the survival, and the density of T are
mathematically equivalent — knowing one determines the others. The hazard is
often the easiest to model.

### The Cox proportional hazards model

Cox (1972) proposed:

$$ h(t \mid X) = h_0(t) \cdot \exp(\beta_1 X_1 + \cdots + \beta_p X_p). $$

A baseline hazard h_0(t) (left unspecified — its functional form doesn't
matter), multiplied by exp(β · X), the **hazard ratio** for an individual
with covariates X.

The "proportional hazards" name: the ratio of hazards for two individuals
at any time t is

$$ \frac{h(t \mid X)}{h(t \mid X')} = \exp(\beta \cdot (X - X')) $$

— constant in t. Two individuals with covariates X and X' have hazards in a
fixed proportion at every time. (If this assumption fails, the model is
misspecified.)

### The partial likelihood

The genius of Cox's method is that we can estimate β *without* estimating
h_0(t). The **partial likelihood**, evaluated only at event times, is

$$ \text{PL}(\beta) = \prod_{i:\, \delta_i = 1} \frac{\exp(\beta \cdot x_i)}{\sum_{j: y_j \ge y_i} \exp(\beta \cdot x_j)}. $$

The interpretation: at each event time, of the subjects at risk, we ask "how
likely was *this* particular subject to be the one who failed?". The
denominator is over all at-risk subjects. h_0(t) cancels out in the ratio.

We maximize PL(β) numerically. The result behaves like a maximum likelihood
estimate: asymptotic normality, standard errors, hypothesis tests.

### Interpreting hazard ratios

If β̂_j = 0.405, then exp(β̂_j) = 1.5. A one-unit increase in X_j multiplies
the hazard by 1.5 — a 50% higher event rate, holding other covariates fixed.

```r
fit <- coxph(Surv(time, status) ~ sex + age + diagnosis, data=BrainCancer)
summary(fit)
exp(coef(fit))           # hazard ratios
exp(confint(fit))        # 95% CIs for HRs
```

### Predicted survival curves

The Cox model gives us β̂ but not h_0(t). Software (e.g., `survfit(coxfit)`)
recovers an estimate of h_0(t) and produces predicted survival curves for
specified covariate values.

## 11.4 Shrinkage for the Cox Model

The Cox partial likelihood is concave in β, so it generalizes naturally to
penalized estimation. Lasso and ridge are both available:

```r
library(glmnet)
x <- model.matrix(~ age + sex + diagnosis - 1, data=BrainCancer)
y <- with(BrainCancer, Surv(time, status))
cv.fit <- cv.glmnet(x, y, family="cox", alpha=1)   # lasso
coef(cv.fit, s="lambda.min")
```

Lasso-Cox is widely used in genomics, where p ≫ n.

## 11.5 Additional Topics

### Time-dependent covariates

The Cox model can incorporate covariates that change over time (e.g.,
laboratory measurements that update). Use long-format data with start/stop
times.

### Other parametric models

For some applications it is useful to assume a parametric form for h_0(t):
Weibull, exponential, Gompertz. These give fully specified models and direct
predictions of survival probabilities, but require correct distributional
assumptions.

### Areas under survival curves

The **median survival time** — the t at which S(t) crosses 0.5 — is a common
summary, but is not always defined (if many subjects are censored before the
median, S(t) may not reach 0.5).

The **restricted mean survival time** (mean of T capped at some τ) is an
increasingly popular alternative.

## 11.6 R Lab: Survival Analysis

```r
library(survival); library(ISLR2)

# Kaplan-Meier:
data(BrainCancer)
fit.km <- survfit(Surv(time, status) ~ 1, data=BrainCancer)
plot(fit.km, xlab="Months", ylab="S(t)")
fit.km   # median survival, n events

# By group:
fit.km2 <- survfit(Surv(time, status) ~ sex, data=BrainCancer)
plot(fit.km2, col=c("blue","red"), xlab="Months", ylab="S(t)")
legend("bottomleft", c("Female","Male"), col=c("blue","red"), lty=1)

# Log-rank:
survdiff(Surv(time, status) ~ sex, data=BrainCancer)

# Cox:
fit.cox <- coxph(Surv(time, status) ~ sex + diagnosis + loc + ki + gtv +
                                       stereo, data=BrainCancer)
summary(fit.cox)

# Predicted survival for specific covariate values:
modaldata <- data.frame(diagnosis=levels(BrainCancer$diagnosis),
                        sex=rep("Female", 4),
                        loc=rep("Supratentorial", 4),
                        ki=rep(80, 4),
                        gtv=rep(mean(BrainCancer$gtv), 4),
                        stereo=rep("SRT", 4))
plot(survfit(fit.cox, newdata=modaldata),
     col=2:5, xlab="Months", ylab="S(t)")
```

Chapter 11 is the only place the book treats time-to-event data, but the
ideas — Bayesian updating with conditional risk, partial likelihoods,
non-parametric baseline hazards — recur in several modern methods.

---

# Chapter 12 — Unsupervised Learning

In supervised learning we always had a Y to chase. In unsupervised learning
there is no Y. We just have X and questions like:

- Are the observations structured into a few groups (**clusters**)?
- Are most variables redundant — could we summarize the data with fewer
  variables (**dimension reduction**)?
- Can we visualize 6,000-dimensional data in 2D?

Unsupervised learning is harder to evaluate than supervised: there's no test
error to minimize. We rely on more subjective judgment about whether the
discovered structure is real and useful.

The two main running examples:

- **USArrests**: per-state arrest rates for assault, murder, rape, and the
  proportion of urban population. n = 50, p = 4. We use it to demo PCA.
- **NCI60**: 64 cancer cell lines, each measured on 6,830 gene expressions.
  No labels (or rather: labels exist but we ignore them and check whether
  unsupervised methods recover them).

## 12.1 The Challenge of Unsupervised Learning

Some honest disclaimers up front:

- "Right answer" is fuzzy. There may be no objective truth about how many
  clusters exist or what they mean.
- Methods are **descriptive**: they produce summaries of the data, not
  predictions to be compared to held-out truth.
- Validity often comes from **scientific interpretation**: do the discovered
  clusters / components correspond to known biology, geography, customer
  segments, etc.?

## 12.2 Principal Components Analysis

### The first principal component

Given a centered (mean-zero) data matrix X, the **first principal component
direction** is the unit-length vector φ_1 ∈ ℝ^p that maximizes the variance
of the projection Z_1 = X φ_1:

$$ \phi_1 = \arg\max_{\|\phi\| = 1} \, \frac{1}{n} \sum_{i=1}^{n} \left(\sum_{j=1}^{p} \phi_j x_{ij}\right)^2. $$

Geometrically: φ_1 is the direction along which the data varies most. Z_1 is
the corresponding **score vector** of length n. The element z_{i1} is the
position of observation i along the PC1 direction.

### Subsequent principal components

The second principal component direction φ_2 is the unit vector orthogonal
to φ_1 that maximizes Var(Xφ). Then φ_3 orthogonal to both, etc. We end up
with p orthogonal directions. They form an orthonormal basis of ℝ^p adapted
to the variance structure of the data.

The **loadings** φ_j tell you which original variables contribute to each PC.
The **scores** z_{ij} = x_i · φ_j tell you the position of each observation
along each PC.

### Computation

The PCs are eigenvectors of the sample covariance matrix XᵀX/n, equivalently
the right singular vectors of X. Software computes them via SVD.

### Standardize first?

Yes, almost always. Variance is scale-dependent: a variable measured in
millimeters will have far higher variance than the same variable in meters.
If we don't standardize, the variable with largest scale dominates PC1.
Standardize to mean 0, SD 1 (set `scale = TRUE` in R) unless all variables
are in the same units and the relative scales matter.

### Proportion of variance explained

After PCA, the **total variance** is

$$ \sum_{j=1}^{p} \text{Var}(X_j) = \sum_{m=1}^{p} \text{Var}(Z_m). $$

(Total variance is preserved; we've just expressed it in a new basis.) The
**proportion of variance explained (PVE) by PC m** is

$$ \frac{\text{Var}(Z_m)}{\sum_{m'=1}^{p} \text{Var}(Z_{m'})}. $$

A **scree plot** plots PVE against m. It always starts high and decreases. We
look for an **elbow** — a sharp bend after which additional PCs contribute
little. The number of PCs before the elbow is a reasonable choice for
dimension-reduction purposes.

For USArrests, PC1 explains 62% of variance and PC2 explains 25%. The first
two PCs together capture 87% of the total variation — enough that a 2D plot
of the score (z_{i1}, z_{i2}) is a useful summary of all 4 dimensions.

### Biplots

A **biplot** displays scores (observations) and loadings (variables) on the
same plot. Each variable becomes an arrow from the origin; its direction
indicates the loading vector, and observations close to that direction have
high values of that variable.

```r
pr.out <- prcomp(USArrests, scale=TRUE)
pr.out$rotation        # loadings (φ matrix)
pr.out$x               # scores (Z matrix)
biplot(pr.out, scale=0)
```

For USArrests, PC1 has roughly equal loadings on Murder, Assault, Rape, and a
small loading on UrbanPop. So PC1 ≈ "overall crime level". PC2 has large
loading on UrbanPop with smaller loadings on the others — "urban-ness".
States plot in (PC1, PC2) according to these two summary axes.

### Other interpretations of PCA

**Approximation in low rank.** PCA gives the best low-rank approximation of
X in the sense of Frobenius (sum of squared) error. Specifically, if we
truncate the SVD to its top M components, we get the matrix of rank M
closest to X.

**Removing redundancy.** PCs are uncorrelated. If we use the first M PCs as
new features (in PCR, Chapter 6), we remove correlation among predictors —
a benefit when fitting subsequent regressions.

## 12.3 Missing Values and Matrix Completion

What if some of X is missing? Throw away rows with any missing values?
Often we lose most of the data.

A better idea: use PCA to *fill in* the missing values, by solving

$$ \min_{A,\, B,\, X} \sum_{(i,j) \in O} \left(x_{ij} - \sum_{m=1}^{M} a_{im} b_{jm}\right)^2, $$

where O is the set of observed entries. The matrix factorization AB^T is a
rank-M approximation of X using only observed entries; missing entries are
predicted by the corresponding entry of AB^T.

This is **matrix completion**, the foundation of recommender systems
(Netflix Prize): rows are users, columns are movies, observed entries are
ratings, missing entries are predictions of unrated movies.

### Iterative PCA-style algorithm

1. Initialize missing entries (e.g., column means).
2. Run PCA on the (now-complete) matrix; truncate to M components.
3. Replace the missing entries with the values from the PCA reconstruction.
4. Repeat until convergence.

```r
# Toy example: introduce some missingness in USArrests
X <- data.matrix(scale(USArrests))
nomit <- 20
set.seed(15)
ina <- sample(seq(50), nomit)
inb <- sample(1:4, nomit, replace=TRUE)
Xna <- X
index.na <- cbind(ina, inb)
Xna[index.na] <- NA

# Iterative imputation
fit.svd <- function(X, M=1) {
  svdob <- svd(X)
  with(svdob, u[, 1:M, drop=FALSE] %*% (d[1:M] * t(v[, 1:M, drop=FALSE])))
}

ismiss <- is.na(Xna)
Xhat <- Xna
Xhat[ismiss] <- mean(Xna[!ismiss])

thresh <- 1e-7; rel_err <- 1; iter <- 0
mssold <- mean((scale(Xna, scale=FALSE)[!ismiss])^2)
mss0 <- mean(Xna[!ismiss]^2)

while (rel_err > thresh) {
  iter <- iter + 1
  Xapp <- fit.svd(Xhat, M=1)
  Xhat[ismiss] <- Xapp[ismiss]
  mss <- mean(((Xna - Xapp)[!ismiss])^2)
  rel_err <- (mssold - mss) / mss0
  mssold <- mss
}
cor(Xapp[ismiss], X[index.na])   # how well did imputation recover the truth?
```

## 12.4 Clustering Methods

A **clustering** of the n observations is a partition of them into K groups,
such that observations within a group are similar and observations across
groups are different. The two big methods: **K-means** and **hierarchical**.

### K-means clustering

We choose K in advance. We seek an assignment of each observation to one of
K clusters that minimizes within-cluster variation:

$$ \min_{C_1, \ldots, C_K} \sum_{k=1}^{K} W(C_k), \qquad
   W(C_k) = \frac{1}{|C_k|} \sum_{i, i' \in C_k} \sum_{j=1}^{p} (x_{ij} - x_{i'j})^2. $$

Equivalently, W(C_k) = 2 Σ_{i ∈ C_k} ||x_i − x̄_k||², where x̄_k is the
cluster centroid.

This is a hard combinatorial optimization (K^n possible assignments). We
use **Lloyd's algorithm**:

1. Randomly assign each observation to one of K clusters.
2. Repeat until assignments stop changing:
   a. Compute the centroid of each cluster.
   b. Reassign each observation to the cluster of its nearest centroid.

Each iteration decreases the objective, so the algorithm converges. But it
converges to a **local** minimum. The result depends on the initial
assignment — bad luck on initialization can give a poor clustering.

**Solution**: run with many random starts (`nstart=20` or more) and keep
the best. R's `kmeans()` does this.

### Choosing K

There is no universally correct answer. Some heuristics:

- **Elbow method**: plot total within-cluster variation vs K. Look for an
  elbow.
- **Gap statistic**: compares within-cluster variation to that of a null
  reference (uniform random data). The K with the largest gap is preferred.
- **Silhouette score**: combines cohesion and separation; higher is better.
- **Domain knowledge**: a market segmentation might require 4 clusters
  (premium, mid, value, churn-risk) regardless of any statistic.

```r
set.seed(2)
km <- kmeans(USArrests, centers=3, nstart=20)
km$cluster
km$centers
km$tot.withinss

library(factoextra)
fviz_nbclust(scale(USArrests), kmeans, method="wss")    # elbow
fviz_nbclust(scale(USArrests), kmeans, method="gap_stat")
fviz_cluster(km, data=USArrests)
```

### Hierarchical clustering

A different idea: don't pre-specify K. Build a tree of clusters by merging
them one at a time.

**Agglomerative hierarchical clustering**:
1. Start with n clusters, each containing one observation.
2. Compute pairwise distances.
3. Merge the two closest clusters.
4. Update distances between the new cluster and all others.
5. Repeat until one cluster remains.

The result is a **dendrogram**: a tree showing the sequence of merges. The
height at which two observations are joined indicates how dissimilar they
are. Cutting the dendrogram horizontally at any height yields a clustering.

### Linkage: how to measure cluster distance

When we merge two clusters into one, we need to recompute the distance to
all other clusters. Different choices ("linkages") give different shapes of
clusters:

| Linkage | Distance(A, B) | Tendency |
|---------|---------------|----------|
| **Single** | min over i ∈ A, j ∈ B of d(x_i, x_j) | "Chains": elongated clusters |
| **Complete** | max over i ∈ A, j ∈ B of d(x_i, x_j) | Tight, ball-shaped |
| **Average** | mean of pairwise distances | Compromise |
| **Centroid** | distance between centroids | Can produce "inversions" |
| **Ward.D2** | increase in within-cluster SS | Minimizes variance; popular |

Complete and average are the most common. Ward.D2 often produces well-
balanced clusters.

### Distance metric

| Metric | Form | Used when |
|--------|------|-----------|
| **Euclidean** | √Σ(x_j − x'_j)² | Default; continuous variables |
| **Correlation** | 1 − cor(x, x') | Profile shape matters more than magnitude (gene expression) |
| **Manhattan** | Σ|x_j − x'_j| | Robust to outliers |

Standardize variables before clustering, unless they're on the same scale.

```r
d <- dist(scale(USArrests))
hc <- hclust(d, method="complete")
plot(hc, main="USArrests", xlab="", sub="")
abline(h=4, col="red")            # cut at height 4
clusters <- cutree(hc, k=4)
```

### Practical considerations in clustering

- **Small choices have big effects**. Linkage, distance metric, scaling, and
  K all change results. Try multiple combinations.
- **Don't over-interpret**. A clustering algorithm will always *produce*
  clusters. The clusters may not reflect any real structure.
- **Cluster validation**:
  - Compare different methods/parameters; do they agree?
  - Subsample and re-cluster; are clusters stable?
  - For labeled data, check whether clusters recover known groups (NCI60).

## 12.5 Practical Issues in Unsupervised Learning

- **Standardization** is almost always needed.
- **Outliers** dominate variance — consider removing them or using robust
  methods.
- **Sample-size dependence**: with small n, even random data appears
  structured. Check by running on shuffled data.
- **Reproducibility**: K-means depends on initialization; use seeds and
  multiple starts.

## 12.6 R Lab: Unsupervised Learning

```r
library(ISLR2)

# PCA on USArrests
pr.out <- prcomp(USArrests, scale=TRUE)
names(pr.out)
pr.out$center        # column means
pr.out$scale         # column SDs
pr.out$rotation      # loadings
pr.out$x             # scores

biplot(pr.out, scale=0)
pr.var <- pr.out$sdev^2
pve <- pr.var / sum(pr.var)
plot(pve, type="b", xlab="PC", ylab="Proportion of Variance Explained")
plot(cumsum(pve), type="b", xlab="PC", ylab="Cumulative PVE")

# K-means
set.seed(2)
km2 <- kmeans(USArrests, centers=2, nstart=20)
km3 <- kmeans(USArrests, centers=3, nstart=20)
km4 <- kmeans(USArrests, centers=4, nstart=20)

# Hierarchical
hc.complete <- hclust(dist(USArrests), method="complete")
hc.average  <- hclust(dist(USArrests), method="average")
hc.single   <- hclust(dist(USArrests), method="single")

par(mfrow=c(1,3))
plot(hc.complete); plot(hc.average); plot(hc.single)

cutree(hc.complete, 4)

# Standardized data:
xsc <- scale(USArrests)
plot(hclust(dist(xsc), method="complete"),
     main="Hierarchical Clustering with Scaled Features")

# Correlation-based distance (works only for p > 2):
x <- matrix(rnorm(30*3), ncol=3)
dd <- as.dist(1 - cor(t(x)))
plot(hclust(dd, method="complete"))

# NCI60 cancer cell lines
data(NCI60)
nci.labs <- NCI60$labs
nci.data <- NCI60$data
dim(nci.data)             # 64 × 6830
table(nci.labs)

pr.out <- prcomp(nci.data, scale=TRUE)
plot(pr.out$x[, 1:2], col=as.numeric(as.factor(nci.labs)),
     pch=19, xlab="PC1", ylab="PC2")
# Cell lines from the same cancer cluster together in PC space — even
# though no labels were used in fitting.

# Hierarchical clustering of NCI60
sd.data <- scale(nci.data)
hc.fit <- hclust(dist(sd.data), method="complete")
plot(hc.fit, labels=nci.labs)
hc.clusters <- cutree(hc.fit, 4)
table(hc.clusters, nci.labs)

# K-means
set.seed(2)
km.out <- kmeans(sd.data, centers=4, nstart=20)
table(km.out$cluster, hc.clusters)   # do they agree?
```

The point of Chapter 12 is exploratory: PCA shows you the dominant patterns
in your data; clustering shows you how observations group. They are tools for
*describing* data rather than predicting from it. In many applied projects,
they are the first thing you should run.

---

# Chapter 13 — Multiple Testing

The classical statistical test produces a p-value. We compare it to a
significance level α (often 0.05). If p < α we reject the null hypothesis.
The interpretation: under the null, there is at most a 5% chance of seeing
such evidence by accident.

This logic breaks when we run many tests. With m = 100 tests at α = 0.05,
even if every null is true, we expect 5 "discoveries" by chance alone. With
m = 10,000 (think: every gene in a microarray study), we expect 500 false
positives. **The 0.05 threshold is no longer interpretable as a 5% chance of
any error.** We need methods that adjust for the multiplicity.

## 13.1 A Quick Review of Hypothesis Testing

Setup:
- A null hypothesis H_0 (no effect, no association).
- A test statistic T.
- A null distribution of T (what we'd see if H_0 were true).
- A p-value: P(T as extreme as observed | H_0 true).

Decision rule: reject H_0 iff p < α. Two ways to be wrong:
- **Type I error**: reject when H_0 is actually true. Controlled at α.
- **Type II error**: fail to reject when H_0 is actually false. The
  complementary probability is **power**.

A good test maximizes power for a fixed α.

## 13.2 The Challenge of Multiple Testing

We test m hypotheses H_{0,1}, …, H_{0,m}. We get m p-values. We want a
procedure that decides which to reject. Possible outcomes:

| | H_0 true | H_0 false | Total |
|--|---------|----------|-------|
| Reject | V (false +) | S (true +) | R |
| Don't reject | U (true −) | W (false −) | m − R |
| Total | m_0 | m − m_0 | m |

We *observe* R and m − R. We don't observe V, S, U, W. The procedure must
make a decision based only on the p-values.

Two error rates we'd like to control:

**Family-wise error rate (FWER)**: P(V ≥ 1) — probability of any false
positive at all.

**False discovery rate (FDR)**: E[V/R] — expected fraction of rejections that
are false positives. (With the convention 0/0 = 0.)

FWER ≤ FDR; controlling FWER is more stringent than controlling FDR.

## 13.3 The Family-Wise Error Rate

### Bonferroni correction

The simplest approach: reject H_{0,j} iff p_j < α/m. Then by Boole's
inequality

$$ P(V \ge 1) = P\!\left(\bigcup_{j: H_{0,j} \text{ true}} \{p_j < \alpha/m\}\right) \le \sum_{j} P(p_j < \alpha/m) = m_0 \cdot \alpha/m \le \alpha. $$

So FWER ≤ α regardless of how the p-values are correlated. Bonferroni is
**conservative** — usually FWER is much less than α — and consequently has
low power.

### Holm's step-down procedure

A uniformly more powerful FWER-controlling procedure:

1. Sort p-values: p_(1) ≤ p_(2) ≤ … ≤ p_(m).
2. Find the smallest j such that p_(j) > α/(m − j + 1).
3. Reject H_{0,(1)}, …, H_{0,(j−1)}.

Holm controls FWER at α and is always at least as powerful as Bonferroni.
Use Holm by default if you want FWER control.

### When to use FWER control

- Small m (a few tests).
- Confirmatory analyses where any false positive is expensive (e.g., FDA
  approval).
- The cost of a single false positive is much higher than the cost of
  missing a true effect.

## 13.4 The False Discovery Rate

For exploratory analyses with many tests, FWER is too strict. Genomics
typically tests 10,000+ hypotheses; allowing ~5% of those *rejected* to be
false (FDR control) is more useful than allowing only 5% chance of *any*
false rejection (FWER control).

### Benjamini-Hochberg procedure

1. Sort p-values: p_(1) ≤ p_(2) ≤ … ≤ p_(m).
2. Find the largest j such that p_(j) ≤ (j/m) · q.
3. Reject H_{0,(1)}, …, H_{0,(j)}.

If no such j exists, reject nothing.

Benjamini and Hochberg proved (1995) that this procedure controls FDR at
level q when the tests are independent, and (Benjamini and Yekutieli, 2001)
under "positive regression dependency" — which covers most realistic
correlation structures.

### Interpretation

If we control FDR at q = 0.10, then *among the rejected hypotheses*, we
expect about 10% to be false positives. So if we reject 200, about 20 are
false. This is a much more useful framing for science with many candidate
effects than Bonferroni's "almost certainly no false positives".

```r
pvals <- c(0.0001, 0.0021, 0.012, 0.040, 0.057, 0.079, 0.110, 0.230, 0.490, 0.890)
p.adjust(pvals, method="bonferroni")
p.adjust(pvals, method="holm")
p.adjust(pvals, method="BH")          # Benjamini-Hochberg
sum(p.adjust(pvals, method="BH") < 0.05)
```

## 13.5 A Resampling Approach

Sometimes we don't trust the theoretical null distribution of T. We can
construct it empirically by **permutation**.

### Permutation test for one hypothesis

To test H_0: no association between X and Y:
1. Compute the observed test statistic T_obs.
2. Randomly permute the y-labels B times. For each permutation, compute the
   statistic T*_b on (X, permuted Y).
3. p-value = (1 + #{T*_b ≥ T_obs}) / (B + 1).

The permuted statistics give the null distribution exactly, with no
distributional assumptions.

### Permutation test with multiple hypotheses

Apply the permutation procedure to *all m* test statistics jointly. This
preserves the dependence structure across tests. Compute the null
distribution of max_j |T_j| under permutations to control FWER.

```r
set.seed(1)
B <- 10000
t.obs <- with(df, t.test(y ~ group)$statistic)
t.perm <- replicate(B, {
  perm <- sample(df$group)
  t.test(df$y ~ perm)$statistic
})
mean(abs(t.perm) >= abs(t.obs))    # permutation p-value
```

## 13.6 Practical Notes

- Plan your tests in advance. **Pre-registering** the hypotheses and analysis
  plan removes the temptation to p-hack.
- For large m, BH (FDR) is the standard choice.
- Always report effect sizes and confidence intervals, not just p-values.
- A "significant" result in a multiple-testing context may still be small.
  Effect size matters.

---

# Appendix A — A General Workflow

For any predictive modeling problem, this is a sensible recipe.

```
1. Define the goal: prediction or inference?
2. Define the metric: MSE, AUC, F1, log-loss, business KPI.
3. Split: train / validation / test, or a CV scheme.
4. Explore: summary statistics, plots, missingness patterns.
5. Preprocess (consistently, inside CV folds!):
   - Impute missing values
   - Handle outliers if needed
   - Encode categoricals
   - Scale if the method requires it (KNN, SVM, ridge, lasso, PCA, NN)
6. Fit a baseline model: linear regression, logistic regression, or
   random forest with default settings.
7. Try a few alternatives:
   - Regularized linear (lasso, ridge, elastic net)
   - Tree ensembles (RF, gradient boosting)
   - Possibly SVM, GAM, or deep network
8. Tune hyperparameters with cross-validation.
9. Apply the 1-SE rule for parsimony if appropriate.
10. Diagnose: residual plots, calibration, fairness, error analysis.
11. Lock in the final model.
12. Evaluate on the test set ONCE.
13. Report effect sizes, intervals, and uncertainty — not just point
    estimates.
```

# Appendix B — Method Selection Cheat Sheet

### For regression

| Setting | First try |
|---------|-----------|
| n ≫ p, want inference, roughly linear truth | OLS linear regression |
| n ≫ p, want inference, nonlinear marginal effects | GAM |
| Many predictors, small/moderate effects | Ridge |
| Many predictors, sparse truth | Lasso |
| Many correlated predictors | Elastic Net or PCR |
| Small p, smooth nonlinear | Splines / GAM |
| Best predictive accuracy | Gradient boosting (XGBoost) or BART |
| Good predictive accuracy with little tuning | Random Forest |

### For classification

| Setting | First try |
|---------|-----------|
| Roughly linear boundary, want inference | Logistic regression |
| Linear boundary, small n, K > 2 classes | LDA |
| Nonlinear boundary, moderate n | QDA, SVM-RBF, or GBM |
| High-dim, sparse features (text) | Naive Bayes or logistic with ℓ¹ |
| Best accuracy | GBM, XGBoost, or RF |
| Imbalanced classes | Adjust threshold via ROC; consider class weights |

### For unsupervised

| Setting | First try |
|---------|-----------|
| Reduce dimensions, visualize | PCA (then plot PC1 vs PC2) |
| Find clusters with known K, roughly spherical | K-means (nstart ≥ 20) |
| Find clusters, K unknown, complex shapes | Hierarchical (Ward.D2 or complete) |
| Gene expression-like profile data | Hierarchical with correlation distance |
| Recommender system / fill missing entries | Matrix completion |

### For survival

| Setting | First try |
|---------|-----------|
| Compare two or more groups, no covariates | Kaplan-Meier + log-rank |
| Continuous covariates | Cox proportional hazards |
| p ≫ n (genomics) | Lasso-Cox via glmnet |

# Appendix C — Glossary of Key Terms

- **Bayes classifier**: assigns x to argmax_k P(Y=k|X=x); achieves the
  Bayes error rate (lowest possible test error).
- **Bias** of an estimator: difference between its expected value and the
  true parameter. A simple model has high bias if the truth is complex.
- **Bootstrap**: resampling with replacement to estimate the variability of
  a statistic.
- **Confidence interval**: a range that, with stated probability over
  repeated samples, contains the true parameter.
- **Cross-validation**: estimating test error by repeatedly fitting on a
  training subset and scoring on the held-out subset.
- **Curse of dimensionality**: the phenomenon that high-dimensional spaces
  become sparse — neighbors are far away — making local methods fail.
- **Degrees of freedom**: a count of the effective number of parameters in
  a model.
- **Effective rank**: the number of meaningful directions in a matrix.
- **F-test**: a test that several coefficients are jointly zero.
- **FDR**: false discovery rate; expected fraction of false positives among
  rejected hypotheses.
- **FWER**: family-wise error rate; probability of *any* false positive.
- **Hazard function**: the instantaneous event rate among those still at
  risk.
- **Hierarchical principle**: when including an interaction X_jX_k, also
  include the main effects X_j and X_k.
- **Kernel function**: a generalized inner product; underlies SVMs and
  kernel methods.
- **Lasso**: linear regression with an ℓ¹ penalty; performs variable
  selection.
- **Likelihood**: the probability of the observed data under a parametric
  model, viewed as a function of the parameters.
- **MLE**: maximum likelihood estimate.
- **OOB**: out-of-bag; observations not used in a particular bootstrap
  sample.
- **One-hot encoding**: same as dummy-encoding for K-level factors.
- **PCA**: principal components analysis; orthogonal directions of maximum
  variance.
- **p-value**: probability under the null of seeing a test statistic at
  least as extreme as observed.
- **Ridge**: linear regression with an ℓ² penalty; shrinks but does not
  zero coefficients.
- **ROC curve**: TPR vs FPR across thresholds; AUC summarizes.
- **Standardize**: subtract the mean and divide by the standard deviation
  of each predictor.
- **Studentized residual**: residual divided by its estimated standard
  deviation.
- **Support vector**: a training point on or inside the margin in an SVM.
- **VIF**: variance inflation factor; diagnoses collinearity.

# Appendix D — Common Pitfalls and Their Fixes

| Pitfall | Fix |
|---------|-----|
| Tuning hyperparameters on the test set | Use CV; never touch test set until final evaluation |
| Forgetting to scale for KNN, SVM, ridge, lasso, PCA, neural nets | Always `scale()` first, or use a method that does it internally |
| Reporting training error as model quality | Always report CV or test error |
| Comparing models with R² when they have different sizes | Use adjusted R², BIC, or CV |
| Choosing K in clustering by visual inspection | Gap statistic, silhouette, or elbow + domain knowledge |
| Including an interaction without main effects | Hierarchical principle |
| Running 100 t-tests, reporting p < 0.05 | Apply BH to control FDR |
| Interpreting an OLS coefficient causally on observational data | Be careful; consider confounding |
| Deciding "n is large so everything is significant" | Report effect sizes and CIs, not just p |
| Selecting polynomial degree by eye | Use CV or ANOVA F-test |
| Bootstrapping a time series ignoring autocorrelation | Block bootstrap |
| Imbalanced classes, default 0.5 threshold | ROC curve; choose threshold by cost |
| Applying lasso without standardizing | `glmnet` does it; check that you didn't bypass it |
| Inferring causation from PCA loadings | PCA is descriptive, not causal |
| Letting feature screening leak into CV | CV the entire pipeline, including screening |

# Appendix E — Quick R Reference

```r
# Modeling
lm(y ~ ., data=df)                              # OLS
glm(y ~ ., data=df, family=binomial)            # logistic
MASS::lda(y ~ ., data=df)                       # LDA
MASS::qda(y ~ ., data=df)                       # QDA
e1071::naiveBayes(y ~ ., data=df)               # Naive Bayes
class::knn(train.X, test.X, train.Y, k=5)       # KNN

# Regularization
glmnet::glmnet(x, y, alpha=0)                   # ridge
glmnet::glmnet(x, y, alpha=1)                   # lasso
glmnet::cv.glmnet(x, y, alpha=1)                # cv for lambda

# Subset
leaps::regsubsets(y ~ ., data=df, nvmax=p)

# Splines / GAMs
splines::bs(x, df=6); splines::ns(x, df=4)
gam::gam(y ~ s(x1, 4) + s(x2, 4))
mgcv::gam(y ~ s(x1) + s(x2))

# Trees
tree::tree(y ~ ., data=df)
randomForest::randomForest(y ~ ., data=df, mtry=4)
gbm::gbm(y ~ ., data=df, distribution="gaussian", n.trees=5000,
         interaction.depth=4, shrinkage=0.01)
BART::gbart(x, y, x.test=xnew)

# SVMs
e1071::svm(y ~ ., data=df, kernel="radial", cost=1, gamma=1)
e1071::tune(svm, y ~ ., data=df, kernel="radial",
            ranges=list(cost=..., gamma=...))

# Resampling
boot::cv.glm(df, fit, K=10)$delta[1]            # k-fold CV
boot::boot(df, stat.fn, R=1000)                 # bootstrap

# Survival
survival::survfit(Surv(time, status) ~ group, data=df)   # KM
survival::coxph(Surv(time, status) ~ ., data=df)         # Cox

# Unsupervised
prcomp(x, scale=TRUE)
kmeans(x, centers=K, nstart=20)
hclust(dist(x), method="complete")
cutree(hc, k=K)

# Multiple testing
p.adjust(pvals, method="BH")
p.adjust(pvals, method="holm")

# Deep learning
keras3::keras_model_sequential() |> layer_dense(...) |> compile(...) |> fit(...)
```

---

End of reference.


