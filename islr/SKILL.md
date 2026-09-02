---
name: islr
description: >
  Textbook-depth reference for all of ISLR v2 (James, Witten, Hastie,
  Tibshirani, 2023). Covers supervised and unsupervised learning: linear
  regression, classification, resampling, regularization, splines/GAMs,
  trees, SVMs, deep learning, survival analysis, unsupervised learning,
  and multiple testing. Includes derivations, worked examples on the
  book's datasets (Advertising, Boston, Default, Smarket, Hitters, Wage,
  Carseats, OJ, USArrests, NCI60, BrainCancer, Khan, NYSE), and R lab
  code. Use when the user asks which statistical learning method to use,
  references ISLR/ISL or its chapters, or asks about bias-variance,
  cross-validation, ridge/lasso, trees, SVMs, neural networks, PCA,
  clustering, survival analysis, or multiple testing. Not for pure
  software/data engineering with no modeling component.
metadata:
  author: Stu Field
  version: "2.0"
  source: "An Introduction to Statistical Learning with Applications in R, 2nd Ed. (2023)"
---

# An Introduction to Statistical Learning

*Based on James, Witten, Hastie, Tibshirani (2023), 2nd Ed.*

Treat this as a self-contained textbook. Cite chapter and section
numbers (e.g. "ISLR §3.3.2") when relevant. Use the worked examples in
the linked chapter files as templates. For a new problem, start with
the workflow and method-selection table below, then open the chapter
file that matches.

Always match method to problem type: regression vs classification,
inference vs prediction, parametric vs nonparametric, n vs p regime.
Flag assumption violations proactively.

---

## Notation used throughout

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

## Datasets used in the book

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

## Chapters (load the matching file for full derivations, theory, and R lab code)

| Chapter | File | Covers |
|---|---|---|
| 1–2 | `references/ch01-02-introduction-statistical-learning.md` | What is statistical learning, bias-variance, train/test error, R basics |
| 3 | `references/ch03-linear-regression.md` | Simple/multiple linear regression, interactions, qualitative predictors, KNN comparison |
| 4 | `references/ch04-classification.md` | Logistic regression, LDA, QDA, Naive Bayes, KNN, GLMs for counts |
| 5 | `references/ch05-resampling.md` | Cross-validation (validation set, LOOCV, k-fold), the bootstrap |
| 6 | `references/ch06-selection-regularization.md` | Subset selection, ridge, lasso, PCR, PLS, high-dimensional data |
| 7 | `references/ch07-nonlinear.md` | Polynomials, step functions, splines, local regression, GAMs |
| 8 | `references/ch08-trees.md` | Decision trees, bagging, random forests, boosting, BART |
| 9 | `references/ch09-svm.md` | Maximal margin classifier, support vector classifier/machine, kernels |
| 10 | `references/ch10-deep-learning.md` | Single/multilayer networks, CNNs, RNNs, when to use deep learning |
| 11 | `references/ch11-survival.md` | Censoring, Kaplan-Meier, Cox proportional hazards, lasso-Cox |
| 12 | `references/ch12-unsupervised.md` | PCA, matrix completion, K-means, hierarchical clustering |
| 13 | `references/ch13-multiple-testing.md` | FWER, FDR, Bonferroni/Holm/Benjamini-Hochberg, resampling p-values |
| — | `references/glossary-and-r-reference.md` | Glossary of key terms + quick R code lookup by task |

---

## Workflow for any new modeling problem

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

## Method selection cheat sheet

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

## Common pitfalls and their fixes

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
