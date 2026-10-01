# Matching Inference Workflow

> Reference module for the `random-sampling-psm` skill. Read this before
> reporting any standard error, confidence interval, or significance
> statement for a matching estimate.

## Principle

**Inference is estimator-specific.** A point estimate and a standard error
are not interchangeable components that can be mixed across matching
estimators.

## Status hierarchy — a gate, not a checklist

Causal interpretation eligibility follows a strict sequence, not a loose
"point estimate → SE → causal effect" pipeline:

```
POINT ESTIMATE VERIFIED
        ↓
INFERENCE VERIFIED
        ↓
CAUSAL INTERPRETATION ELIGIBLE
```

Each stage gates the next. A verified point estimate without verified
inference does not become causally interpretable just because a number
exists — it stays at **COMPUTED — INFERENCE NOT VERIFIED** (see the
evidence/result-status system in `SKILL.md`) until inference is actually
verified.

## Required record

For every reported effect, record: estimator; matching algorithm;
replacement; ratio; treatment estimand; variance method; CI method;
software/package/version; seed where relevant.

## Abadie–Imbens

If the user selects Abadie–Imbens analytic inference through
`Matching::Match()`, the skill must actually execute or independently
verify that estimator-specific output before reporting its SE/CI as
computed.

If unavailable:

**Abadie–Imbens SE/CI: NOT VERIFIED.**

Do not replace it with: naive pair-difference SE; ordinary weighted OLS SE;
generic cluster-robust SE; generic bootstrap; and label the replacement
Abadie–Imbens.

## Fallback

If an exact estimator-specific implementation is unavailable, provide
executable code and clearly mark the missing inference as not
computed/verified.

## QA

If the code does not implement the claimed inference, the result is NOT
REPRODUCED.
