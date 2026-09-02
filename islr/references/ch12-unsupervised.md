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

