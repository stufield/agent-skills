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

