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

