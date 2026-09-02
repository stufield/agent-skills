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

