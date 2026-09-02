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

