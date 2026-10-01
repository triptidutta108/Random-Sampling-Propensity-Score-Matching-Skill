# Binary Propensity Score Matching Workflow

> Reference module for the `random-sampling-psm` skill. Read this before
> executing a binary-treatment PSM task. Assumes the treatment
> classification gate in SKILL.md has already routed here (treatment
> confirmed genuinely binary).

## State
INTAKE → AUDIT → DESIGN → HUMAN GATE → COMPUTE → DIAGNOSTICS → QA → INFERENCE → INTERPRETATION → REPRODUCIBILITY → COMPLETE/BLOCKED

## 1. Treatment/outcome audit

Confirm: binary treatment; outcome definition and timing; target
population; estimand; treatment assignment timing.

If treatment is continuous or multivalued, route to the relevant workflow
instead of recoding silently.

## 2. Covariate audit

For every candidate covariate classify: confirmed pre-treatment; confirmed
post-treatment/mediator; baseline measure requiring design-specific review;
timing uncertain; substantive role uncertain.

Confirmed post-treatment variables are excluded from the primary
adjustment set.

Timing uncertainty is BLOCKING for causal PSM unless the user explicitly
changes the objective to a noncausal analysis.

Do not infer timing from variable names.

## 3. Estimand

Require explicit selection of ATT, ATE, or supported alternative.

After trimming, report the actual retained estimand population — not just
the point estimate. Mandatory block whenever trimming occurs:

```
Original treated N
Treated outside support
Treated retained
Retained share (%)
Resulting estimand population (one sentence)
```

Example:

```
Original treated: 105
Outside support: 12
Retained: 93
Retained share: 88.6%
Interpretation: ATT for supported treated observations
```

Not simply "ATT = -1.98." (See `diagnostics.md` for the full overlap/
positivity distinction this reporting requirement follows from.)

## 4. Propensity model

Default baseline:

```r
glm(treatment ~ justified_pre_treatment_covariates,
    family = binomial(), data = analysis_data)
```

Audit convergence, separation, extreme scores, sparse cells, and
functional-form concerns.

Do not add interactions or nonlinearities solely because they improve
balance.

## 5. Matching design

Record: MatchIt or other implementation; distance/model; ratio;
replacement; caliper and scale; exact matching; trimming; tie handling.

For EvalCommunity-style work, use MatchIt as the primary reference
implementation for matching design and diagnostics — this is the default,
not merely one option, and should not be displaced by
`Matching::Match()` simply because a given session's testing happened to
use it. If `Matching::Match()` is selected for estimator-specific inference
(typically because Abadie-Imbens SEs are explicitly required), record it
separately and use its own inference machinery (see `inference.md`).

**Caliper reporting, if a caliper is used** — the stated caliper scale must
match the variable supplied to the matching estimator. If a logit-PS caliper is
specified, compute and apply it on the logit propensity-score scale, and report:

```
Caliper scale (e.g. logit PS)
Caliper width (e.g. 0.2 SD)
Numerical threshold (the actual value)
Matches affected
Treated observations dropped
Caliper binding: YES/NO
```

**Mathematical consistency requirement.** `Matching::Match()`'s `caliper`
argument is auto-standardized: per its documentation, `caliper = 0.25` means
"all matches not within .25 standard deviations of each covariate in X are
dropped," where the SD is computed internally from the `X` actually
supplied. This means the numeric caliper value does **not** need to be
pre-multiplied by an SD, or `X` pre-standardized — but it also means the
caliper's real-world meaning depends entirely on **what scale `X` is on**,
since "0.2 SD" is 0.2 SD *of whatever distribution `X` has*.

The checkable requirement, stated precisely: if the caliper is described as
a "0.2 SD logit-PS caliper," the `X` argument passed to `Match()` must
actually be the logit-transformed propensity score (e.g. `qlogis(pscore)`
in R, `log(p/(1-p))` in Python), not the raw probability-scale score. These
are different distributions with different SDs, so `caliper = 0.2` applied
to raw `pscore` and `caliper = 0.2` applied to `logit_ps` are two different,
non-interchangeable calipers — even though the code differs only in which
column is passed as `X`. **This exact inconsistency has occurred in
practice** (narrative said "0.2 SD logit-PS caliper" while the code passed
raw, untransformed `pscore` as `X`) — treat it as a recurring failure mode
to check for explicitly: confirm `X` is actually logit-transformed before
accepting a "logit-PS caliper" label, don't assume it from the narrative
text alone.

"Caliper binding: NO" is itself an important, reportable finding. Report
that the specified caliper did not affect the matching result; do not infer
why unless the evidence establishes the mechanism.

## 6. Overlap

Report: treated/control PS ranges; common support; units outside support;
retained treated units; support restrictions.

Do not interpret successful matching as proof of positivity.

## 7. Balance

Report before/after balance for every adjustment variable. If categorical
covariates are expanded into multiple diagnostic terms, explicitly report
the mapping and distinguish substantive covariate counts from displayed
balance-term counts. Prefer: SMD;
variance ratios where appropriate; Love plot; distributional diagnostics;
propensity overlap; reuse/matched sample information.

Treat 0.10 as a descriptive benchmark, not a hard pass/fail.

Flag any covariate whose balance worsens.

(See `diagnostics.md` for the shared overlap/balance/reuse reporting
detail and the stopping rule.)

## 8. Sensitivity — tiered, not open-ended

Sensitivity analysis follows an explicit hierarchy; do not let it become
unstructured specification search:

**Tier 1 — primary design.** The user-confirmed, substantively justified
design. This is the design of record — *unless* the user's decision on
which specification to treat as primary is itself "co-equal," "defer," or
equivalent. In that case, do not use "Tier 1"/"primary" framing at all:
present each specification with parallel structure (same tier depth, same
diagnostic detail, no ordering that implies preference) until the user
designates one. See "Gate enforcement" (under Human-in-the-loop gate) in `SKILL.md`.

**Tier 2 — prespecified sensitivity.** Pre-specified or substantively
justified alternatives only: caliper; replacement; ratio; exact matching;
alternative overlap restriction. Each alternative is a distinct design —
report its own retained population and diagnostics, not just its point
estimate.

**Tier 3 — diagnostic stopping.** If no Tier 1 or Tier 2 design produces
defensible overlap/balance, stop (see Stopping rule below). Do not keep
generating further alternatives hoping one will look better.

## 9. Stopping rule

If credible overlap/balance cannot be achieved under defensible designs
(Tier 1 or Tier 2):

**STOP. Do not continue automated tuning.**

A numerical estimate may be reported as computed/diagnostic, but not as a
credible causal effect. This is a hard stop, not a suggestion to keep
trying more specifications — see the final self-audit question in
`SKILL.md`: "Were any alternative specifications selected because they
produced a preferred estimate?"

## 10. Inference

Inference must match the estimator actually used.

Do not label generic regression SEs, naive pair-difference SEs, or generic
bootstrap intervals as Abadie–Imbens unless that estimator was actually
computed/validated. (See `inference.md`.)

If estimator-specific inference cannot be computed:

**SE/CI: NOT COMPUTED / NOT VERIFIED.**

## 11. QA

Reconcile code against reported: N; treatment counts; trimming; matching
settings; estimate; inference; seed.

Any material mismatch → NOT REPRODUCED/BLOCKED.

If multiple specifications are being reported (including co-equal/deferred
ones), reconcile each independently and report its own status. A
"REPRODUCED" verdict for one specification does not extend to any other —
see "Reconciliation" in `reproducibility.md`.

## 12. Interpretation

Separate: computed estimate; diagnostic verdict; causal interpretation.

Never claim matching proves causality or eliminates unmeasured
confounding.
