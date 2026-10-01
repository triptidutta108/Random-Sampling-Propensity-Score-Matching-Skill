# Random and Stratified Sampling Workflow

> Reference module for the `random-sampling-psm` skill. Read this before
> executing a random or stratified sampling task.

## State
INTAKE → AUDIT → DESIGN → HUMAN GATE → COMPUTE → QC → REPRODUCIBILITY → COMPLETE/BLOCKED

## 1. Audit the frame

Check: row/column counts; eligibility variable; missing eligibility;
identifier missingness; identifier duplicates; duplicate rows; invalid
values; stratum availability.

If a unique sampling ID is missing or duplicated, treat this as a BLOCKING
issue until the user resolves the frame or authorizes a defensible
alternative identifier.

## 2. Define the target

Record: requested sample size; eligible population size;
replacement/non-replacement; simple or stratified design; strata;
allocation rule; seed.

If requested n exceeds the eligible frame under sampling without
replacement, BLOCK and report the exact feasibility problem.

## 3. Stratified allocation

Options: proportional; equal; custom.

For proportional allocation, use a transparent rounding rule such as
largest remainder. Verify allocations sum exactly to the requested target
and each stratum can supply its allocation.

For equal/custom allocation, verify every stratum before computation.

If a custom allocation does not sum to the requested target, BLOCK rather
than silently altering either the target or allocations.

## 4. Human gate

When the allocation or replacement design is substantive, explain the
options and recommend a defensible default. Once confirmed, retain the
decision.

If the user changes the design before computation, invalidate the old
pending sample and recompute under the latest instruction.

## 5. Compute

Use actual computational selection. Never fabricate sampled IDs.

Record: seed; filters; allocation; algorithm; selected IDs.

## 6. QC

Verify: realized n equals target; no duplicate sampled IDs without
replacement; stratum counts equal allocation; every sampled unit meets
eligibility; selected IDs exist in the frame.

Only label QC PASS if these checks were actually computed.

## 7. Reproducibility

Same frame + same design + same seed should reproduce the same selected
IDs/records under deterministic implementation.

Different seed → different sample is expected and not itself a failure.

## 8. Outputs

Where applicable: sample dataset; allocation table; audit report; code;
decision record; QC report.

Never claim an output file exists unless it was actually created.
