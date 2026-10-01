# Reproducibility and Audit Trail

> Reference module for the `random-sampling-psm` skill. Read this when
> assembling the final record for any completed analysis, and whenever
> reconciling reported results against code/output.

## Minimum record

### Data
- source file
- N and variables
- identifier checks
- missingness
- filters/subsets

### Design
- treatment
- outcome
- estimand
- covariates
- timing confirmation
- matching/GPS method
- ratio
- replacement
- caliper
- trimming
- inference method

### Computation
- software
- packages/versions
- seed
- code
- execution status
- warnings/errors

### Results
- point estimate
- SE/CI
- diagnostics
- result status
- limitations
- interpretation

## Result provenance

Every numerical result must be traceable, not just stated. Every reported
number should be able to answer "where did this come from?" — e.g.:

```
ATT = 7.98
Status = VERIFIED
Estimator = Matching::Match()
Data = A_post_treatment_covariate.csv
N = 1,800
Matched treated = 649
Replacement = TRUE
Ratio = 1
Seed = 20260913
```

If a component isn't actually verified, say so in the same record rather
than omitting it:

```
ATT = 7.98
Status = COMPUTED — INFERENCE NOT VERIFIED
SE = NOT COMPUTED / NOT VERIFIED
```

This is more informative than simply saying "computed" — it tells the
reader exactly which parts of the result they can rely on.

## Reconciliation

Before finalizing, compare reported results with executable code and saved
outputs. Any material disagreement must be labeled NOT REPRODUCED/BLOCKED.

If multiple specifications are being reported — including specifications
the user has designated "co-equal" or deferred a primary choice among —
each one requires its own complete reconciliation record (see the ATT
example format above, one block per specification) and its own status.
Verifying one specification and extending "REPRODUCED" to the set is not
permitted; an unreconciled specification stays at its actual status
(e.g. NOT REPRODUCED, or SUPPLIED/UNEXECUTED) independent of any other
specification's status.

**Numerical similarity is not evidence of estimator equivalence.** Two
implementations that produce similar point estimates must not be treated
as having replicated each other unless their treatment definition,
propensity model, matching algorithm, trimming, distance metric, tie
handling, replacement, and inference procedure are actually reconciled —
not merely assumed equivalent because the numbers landed close together.
Conversely, do not treat a *different* result as automatically indicating
an error: before concluding two implementations disagree, check whether
they were actually specified identically. A quantified sensitivity check
(e.g. perturbing inputs by a plausible numerical-precision margin and
re-running) is more informative than asserting a discrepancy is or isn't
implementation noise.

## File integrity

Never claim an output file was created unless it exists and is available.
Preserve the source dataset and create separate analysis/matched/trimmed
outputs.
