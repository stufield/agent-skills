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


