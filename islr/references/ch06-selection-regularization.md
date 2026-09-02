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

