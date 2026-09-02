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

