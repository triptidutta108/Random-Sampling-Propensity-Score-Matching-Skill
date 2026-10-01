# Matching Diagnostics and Stopping Rules

> Reference module for the `random-sampling-psm` skill. Shared by
> `psm_binary.md` and `gps_continuous.md` — read this whenever reporting
> overlap, balance, or reuse diagnostics, or applying the stopping rule.

## Overlap vs. positivity — a real distinction, not interchangeable terms

**Common support** identifies observations whose propensity scores lie
within the empirical range of the comparison group. It does not by itself
establish adequate positivity or guarantee good matches — it is a minimum
necessary check, not a sufficient one.

Report treated/control propensity distributions and common support.
Quantify observations removed and state the resulting estimand population.
After trimming, be explicit: **the estimand is now defined for the treated
observations retained within the analyzed support, not necessarily for the
original treated population.**

Retained-population reporting is mandatory whenever trimming occurs:

```
Original treated N
Treated outside support
Treated retained
Retained share (%)
Resulting estimand population (one sentence)
```

Poor overlap is an identification concern, not merely a technical
inconvenience.

## Balance

For every adjustment variable report: before SMD; after SMD; absolute
change; whether balance improved or worsened.

If categorical covariates are expanded into multiple balance terms, state the
expansion explicitly so the reader can reconcile the number of substantive
adjustment variables with the number of reported diagnostic terms.

In addition to the per-covariate table, report at the design level:

```
Maximum absolute SMD (value + which covariate)
Number of covariates exceeding 0.10
Covariates whose balance worsened after matching
Variance ratios, where relevant
```

Every count in that design-level summary must be computed directly from
the per-covariate table above it (e.g. by filtering/summing its rows) —
never stated by hand or carried over from memory. If a stated count does
not reconcile exactly against the table, that is a provenance failure on
the count, not a rounding matter — fix it before reporting.

Balance should be evaluated jointly across covariates and diagnostics — a
single covariate crossing 0.10 does not automatically invalidate a design,
and a maximum SMD just below 0.10 does not by itself establish adequate
balance. Do not report "maximum SMD = 0.099" as if that number alone
implies success; state which covariate it belongs to and how close it sits
to the benchmark.

Use visual diagnostics where available: Love plot; propensity overlap;
covariate distribution plots.

A |SMD| around 0.10 is a descriptive benchmark, not a universal pass/fail
threshold.

## Do not diagnose the cause without evidence

Report what a diagnostic shows. Do not infer *why* it changed unless the
available evidence actually identifies the mechanism — e.g. don't attribute
a caliper's lack of effect to "an inflated SD" without demonstrating that
directly. When in doubt: "the diagnostic changed; the available evidence
does not establish why."

## Reuse

When matching with replacement, report: number of matched treated units;
unique controls used; maximum control reuse; distribution of reuse where
useful.

Thin comparison pools should be explicitly discussed.

## Sensitivity discipline

Do not try arbitrary specifications until balance improves. Every redesign
must be substantively justified or pre-specified.

If the user declines to designate a primary specification among the
alternatives ("co-equal," "defer," or equivalent), no diagnostic table,
tier label, or summary here may imply one is preferred — see "Gate enforcement" in `SKILL.md`.

## Stopping rule

If repeated defensible designs fail to produce adequate overlap/balance,
stop and report that the available data/covariates do not support a
credible PSM estimate under the analyzed design.

Do not rescue a failed design by selecting the specification with the most
attractive point estimate.
