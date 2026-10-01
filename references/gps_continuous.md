# Continuous-Treatment GPS / Dose-Response Workflow

> Reference module for the `random-sampling-psm` skill. Read this before
> executing a continuous-treatment task. Assumes the treatment
> classification gate in SKILL.md has already routed here (treatment
> confirmed genuinely continuous — do not reach this file by
> dichotomizing a continuous treatment yourself).

## State
INTAKE → AUDIT → DESIGN → HUMAN GATE → COMPUTE → DIAGNOSTICS → QA → INTERPRETATION → REPRODUCIBILITY → COMPLETE/BLOCKED

## 1. Eligibility

Confirm treatment is genuinely continuous. Do not dichotomize without an
explicit substantive decision.

Define: treatment dose; outcome; covariates; dose-support region; target
ADRF/dose contrast.

## 2. Covariate timing

Confirm that adjustment variables precede treatment assignment. Unknown
timing is BLOCKING for causal interpretation.

## 3. Conditional treatment model

Choose a defensible conditional distribution with human input when
substantive assumptions matter.

For a Normal model, inspect: residual distribution; Q-Q plot; skewness;
heteroskedasticity; model fit; conditional plausibility.

Do not treat a single Shapiro-Wilk p-value as proof of normality — combine
residual diagnostics, Q-Q/distributional evidence, model fit, and
substantive plausibility. A non-significant test is evidence against a
particular departure, not proof of the assumption.

## 4. GPS and overlap

Estimate the GPS and inspect treatment/GPS support. Avoid extrapolation
into unsupported dose regions.

## 5. Dose-response model

A quadratic dose/GPS model can be used as a prespecified baseline when
justified. Record the functional-form assumption explicitly.

If alternative functional forms are tested, label them sensitivity
analyses and do not select among them solely because one produces a
preferred effect.

## 6. Balance

Use an appropriate GPS balance diagnostic, such as stratification across
treatment and GPS strata where justified. Every covariate included in the
conditional treatment model must be covered by the balance diagnostics,
including categorical variables; if a categorical variable is represented
by multiple terms, document that representation. Report both magnitude-based
balance and any inferential summaries used, rather than relying on p-values
alone. Report residual imbalance rather than treating GPS adjustment as
automatically successful.

## 7. ADRF reporting

Report:

```
Observed dose range
Supported dose range
Functional form
Residual diagnostics
GPS balance
ADRF specification
Extrapolation warning (if the ADRF is being evaluated near/beyond the
  supported dose range for any subgroup)
```

Plus: full ADRF over the observed/supportable dose range; selected dose
contrasts where useful; uncertainty if actually computed; support
limitations.

If the ADRF is nonlinear, do not summarize it as a constant per-unit
effect — e.g. do not say "treatment increases outcome by X per unit" when
the fitted curve is not a straight line. Report the shape instead (e.g.
"outcome rises across most of the range but dips at very low doses").

## 8. Interpretation

Distinguish the fitted dose-response pattern from a causal dose-response
claim. Causal interpretation remains conditional on identification
assumptions and adequate overlap/balance.

A statistically acceptable conditional-treatment model does not by itself
establish causal identification. The GPS model, dose-response
specification, overlap, covariate balance, and identifying assumptions
must be considered jointly — passing any one of these checks in isolation
is not sufficient.
