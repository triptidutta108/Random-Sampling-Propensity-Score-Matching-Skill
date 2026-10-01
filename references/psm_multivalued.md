# Multivalued-Treatment Workflow

> Reference module for the `random-sampling-psm` skill. Read this before
> executing a task with a 3+ category treatment. Assumes the treatment
> classification gate in SKILL.md has already routed here (treatment
> confirmed multivalued — do not reach this file by collapsing categories
> yourself).

## State
INTAKE → AUDIT → DESIGN → HUMAN GATE → COMPUTE → DIAGNOSTICS → QA → INFERENCE → INTERPRETATION → REPRODUCIBILITY → COMPLETE/BLOCKED

## 1. Classify treatment

Determine whether treatment categories are: nominal/unordered;
ordinal/ordered; dose/intensity categories.

Do not infer ordering from codes such as 0/1/2.

## 2. Reference group

Ask whether a natural control/reference exists. If the user cannot
establish one, do not silently use category 0 as control.

## 3. Define estimand

Possible designs include: generalized/multinomial treatment framework;
explicitly defined pairwise contrasts.

If pairwise PSM is selected, define each comparison separately, e.g.: 1 vs
0; 2 vs 0; 2 vs 1.

Do not call all three a single ATT without specifying the contrast.
**Pairwise contrasts are separate causal questions, not multiple estimates
of one common treatment effect** — three pairwise comparisons produce three
separate estimands, each with its own identifying assumptions, not a
single "the effect of treatment" broken into parts.

## 4. Covariate timing

Confirm timing for the covariates in each comparison. If timing cannot be
confirmed and causal adjustment is the objective, BLOCK.

## 5. Pairwise implementation

For each comparison:
1. subset to the two relevant treatment groups;
2. refit the propensity model;
3. apply the selected matching design;
4. check overlap;
5. check balance;
6. estimate the pair-specific effect;
7. obtain estimator-specific inference;
8. document the comparison separately.

Do not reuse a propensity model from another pair unless the
estimator/design explicitly supports that approach and it has been
justified.

If extreme propensity-score thresholds are used as diagnostics, report the
threshold explicitly and treat it as a diagnostic rule rather than a causal
stopping criterion by itself. Stopping should rest on the combined evidence
from model fit/separation, support, retained population, and balance.

For each pair, report as a distinct block:

```
Contrast (e.g. "1 vs 0")
Reference group
Treatment group
Analysis population
Covariates
Propensity model
Overlap
Balance
Estimate
Inference
```

## 6. Interpretation

Report pairwise effects separately. State the population and reference
group for every estimate.

If substantive group meaning remains unknown, describe the results as
statistical contrasts and avoid policy-specific causal language.
