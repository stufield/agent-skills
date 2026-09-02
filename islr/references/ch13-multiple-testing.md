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

