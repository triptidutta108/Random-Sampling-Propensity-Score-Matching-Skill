---
name: random-sampling-psm
description: Structured, reproducible workflow for AI-assisted random/stratified sampling, propensity-score matching (PSM) on binary treatment, generalized propensity score (GPS)/dose-response for continuous treatment, and multivalued-treatment designs. Use when the user wants to draw a random or stratified sample, design or execute a probability sample, select beneficiaries/respondents/units for a survey or evaluation, or estimate a treatment effect via matching (binary, continuous dose/intensity, or 3+ category treatment). Also trigger for auditing a sampling frame or covariate timing, calculating stratified allocation, checking balance/overlap diagnostics, classifying covariates by treatment timing, assessing common support, reconciling reported results against code, or producing reproducible R code and QC reports. Use this skill even if the user just says "help me pick a random sample of X from this list" or "I need to match treatment and control groups" without naming "sampling" or "PSM" explicitly.
---

# AI-Assisted Sampling & Propensity Score Matching

**Validation revision: post-Test-19 adversarial hardening**

## Purpose

Provide a reusable, evaluator-facing workflow in which AI assists with data
auditing, design organization, code generation, computation, diagnostics,
QA, and documentation while the researcher/evaluator retains substantive
methodological control.

Core principle: **AI assists → statistical software computes → diagnostics
assess → human QA/review → interpretation is cautious.**

This is a stateful analysis process with explicit gates, blockers,
decisions, computation status, and an audit trail — not a generic prompt
collection.

## Workflow state

**INTAKE → AUDIT → DESIGN → HUMAN GATE → COMPUTE → DIAGNOSTICS → QA →
INTERPRETATION → REPRODUCIBILITY → COMPLETE/BLOCKED**

Do not silently skip a required gate. A BLOCKING issue prevents downstream
causal computation until resolved or the user explicitly changes the design
to a defensible alternative.

## Global rules

1. Never invent observations, IDs, sample sizes, treatment assignments, outcomes, covariates, results, study-design facts, or file contents.
2. Never fabricate a random sample; use actual computational selection.
3. Never silently modify the original dataset. Work from a preserved analysis copy/subset.
4. Record filters, transformations, seeds, software, package/version information when available, methodological decisions, and code.
5. Distinguish at minimum: OBSERVED, COMPUTED, INFERRED, RECOMMENDED, USER-DECIDED, ASSUMED, and UNVERIFIED.
6. Treat information that cannot be established from the dataset as unknown. Say explicitly when the data cannot answer a substantive question.
7. Give a concise defensible recommendation when a choice is needed, but let the user override it.
8. Do not make irreversible substantive methodological decisions without user input when reasonable alternatives exist.
9. Retain resolved decisions. Do not re-ask unless data, design, or circumstances materially change.
10. Before executing pending computation, resolve the complete current design from the latest user instruction. A new explicit instruction overrides inherited pending design.
10a. **Hard human-gate stop:** when any material human decision is pending, halt the entire downstream causal workflow. Do not apply already-decided items, re-key, filter, impute, recode, estimate, match, run sensitivity analyses, or partially execute settled branches while another gate is unresolved. Record settled decisions as DECIDED/RECORDED but INERT until the blocking gate is resolved.
11. If a design parameter changes before execution, invalidate the pending computation based on the old parameter and recompute under the new design.
12. Report all material blockers before asking how to proceed.
13. Use statistical software/code for numerical computation whenever execution is available. The hierarchy is: AI reasoning → generate/inspect code → statistical software executes → diagnostics → human QA → interpretation. AI-generated numerical results must never be treated as verified merely because the calculation is mathematically plausible. Clearly distinguish computations actually executed and verified from code supplied for later execution or replication.
14. Never report PASS unless the relevant check was actually computed and verified.
15. Never claim an output file exists unless it was actually created and is available.
16. Report warnings, failures, and incomplete verification rather than hiding them.
17. Never present a computed point estimate as a validated causal effect when the identification/design diagnostics do not support that interpretation.
18. PSM/matching does not recreate randomization and does not eliminate unmeasured confounding.
19. Matching diagnostics do not prove causal validity.
20. Never choose covariates, transformations, matching settings, trimming rules, or functional forms solely because they improve balance or produce a preferred treatment effect.
21. Never infer substantive meaning, ordering, control status, treatment timing, mediator status, or causal relevance from a variable name, numeric code, or apparent demographic meaning alone.
22. Never claim a mechanism for why a diagnostic changed unless the evidence actually establishes that mechanism. Prefer: "the diagnostic changed; the available evidence does not establish why."
23. Numerical similarity is not evidence of estimator equivalence. Two implementations producing similar point estimates must not be treated as having replicated each other unless their treatment definition, propensity model, matching algorithm, trimming, distance metric, tie handling, replacement, and inference procedure are actually reconciled.
24. Every reported numerical result must carry a provenance record (see `references/reproducibility.md`) — not just a number. The record must identify the input dataset/version, analysis population or filters, transformation/estimator settings that generated the number, execution status, and software when relevant.
25. Every reported matching distance, caliper, trimming threshold, transformation, and diagnostic threshold must match the variable and scale actually supplied to the estimator. A stated logit-PS caliper, for example, must be computed and applied on the logit-PS scale; do not describe a raw-PS caliper as a logit-PS caliper.
26. Before accepting numerical results, reconcile data identity and sample accounting: reported N, treatment counts, exclusions, retained units, and output populations must agree with the stated input file and preprocessing. If the exact input/version or computation path cannot be established, label the result NOT REPRODUCED rather than assuming the discrepancy is harmless.

27. **Temporal-order evidence rule:** establish pre/post status from study design, collection dates, source metadata, measurement documentation, or explicit human confirmation. SMDs, correlations, distributional divergence, predictive power, treatment association, model fit, variable names, and apparent treatment-pattern differences are diagnostic clues only and cannot establish temporal ordering.
28. **Treatment-intensity role rule:** distinguish treatment, treatment component, dose/intensity, exposure, mediator, and treatment-timing information. Do not automatically label dose/intensity as a mediator or confounder; its substantive role must come from the design/data-generating process. A variable that is a deterministic function of treatment status cannot be included as a propensity-score covariate, regardless of its temporal classification.
29. **Duplicate-ID rule:** a duplicate identifier is a data-integrity issue, not evidence that one row is erroneous. Do not silently delete a row. If the observations are known to be distinct and re-keying is explicitly authorized, create distinct IDs and preserve `original_id`; otherwise pause for source verification. No downstream causal computation may use an unresolved duplicate-ID problem.
30. **Missingness/complete-case rule:** complete-case analysis is an explicit design choice, not a default claim of unbiasedness. Report missingness by treatment, outcome, covariate, ID, and design information; account for overlapping exclusion sets; and state that balance among complete cases does not establish representativeness of the original population.
31. **Local-positivity rule:** assess subgroup-specific overlap only for subgroups defined by the supplied study design/data. Never invent a subgroup definition or cutpoint merely to satisfy a requested analysis. If a substantively important subgroup has poor overlap, do not silently drop it; trigger a design/interpretation gate where necessary.
32. **Reuse rule:** quantify control reuse and concentration when replacement is used, but do not impose an arbitrary universal reuse threshold. High reuse is a design-credibility warning requiring review; robust/clustered standard errors do not repair a weak comparison design.
33. **Estimand-retention rule:** if matching/trimming/calipers remove treated units, explicitly redefine the computed ATT as applying to the retained treated population. Track original eligible, original treated, exclusions, support exclusions, caliper exclusions, retained treated, and relevant controls. Never silently generalize the retained ATT to all originally treated units.
34. **Specification-shopping rule:** do not search over covariate sets, transformations, functional forms, calipers, matching settings, or outcome models to obtain preferred balance, ATT, SE, p-value, CI, or substantive interpretation. If multiple scientifically defensible specifications remain, report them co-equally unless a primary specification was established independently of results, and reconcile diagnostics for each.
35. **Outcome-leakage rule:** exclude variables derived from the outcome, post-treatment outcomes, or post-treatment measures from the primary propensity model. A variable with uncertain timing and no design documentation is blocking when its eligibility affects the adjustment set.
36. **Outcome-adjustment language rule:** a post-match outcome regression may be described as a covariate-adjusted or doubly-adjusted outcome analysis. Do not claim that regression 'corrects away' residual imbalance or unmeasured confounding.
37. **Estimator/report audit rule:** when code and a report disagree, audit treatment definition, estimand, covariates, matching ratio, replacement, distance metric and scale, caliper and standardization, support/trimming, weights, outcome estimator, SE/inference, clustering, and software/version. If the code fails before the estimator runs, do not patch it silently or reproduce the reported estimate by substitution.
38. **Independent-QA rule:** a QA check is independent only when its calculation path is substantively separate from the calculation being verified. If a QA script has a bug, disclose it, correct the QA path, and rerun the check; do not silently relabel the original result.
39. **Workflow-complete vs causal-validity rule:** `COMPLETE` means the requested workflow executed and its QA/provenance requirements were satisfied. It does not mean that causal identification has been proven or that every causal assumption/subgroup positivity condition was verified. State unresolved design limitations explicitly.
40. **Diagnostic-threshold rule:** SMD thresholds such as 0.10, extreme propensity-score flags, reuse concentration, and other diagnostic cutoffs are review criteria, not universal proofs of validity or automatic invalidity. Use the full design and diagnostic evidence.
41. **Aggregate-count provenance rule:** every reported aggregate diagnostic count (e.g. "N covariates with |SMD|>0.10," "N terms worsened," "N dropped for lack of match") must be computed directly from the underlying diagnostic table or object by filtering/summing it — never hand-counted or stated from memory. If a manually stated count does not reconcile exactly with what the table itself produces, treat that as a provenance failure on the count and correct it before reporting.

## Evidence and result-status system

Use explicit statuses where relevant:

- **VERIFIED:** computation was actually executed and independently checked or reconciled.
- **COMPUTED — INFERENCE NOT VERIFIED:** point estimate was computed, but estimator-specific inference was not verified.
- **DIAGNOSTIC ONLY:** diagnostics were computed but no treatment effect should be interpreted.
- **NOT REPRODUCED:** reported result cannot be reconstructed from the available code/specification/output.
- **BLOCKED:** required methodological or data information is missing.
- **NOT CREDIBLE FOR CAUSAL INTERPRETATION:** a numerical estimate may exist, but design/overlap/balance/identification problems prevent a credible causal interpretation.

Never upgrade a result status without actual evidence.

## Issue register

For every material issue, record: issue ID; severity (BLOCKING / WARNING /
INFORMATIONAL); evidence; why it matters; current status; decision
required; user decision, if any; resolution or stopping reason.

A BLOCKING issue must prevent downstream causal computation until resolved.
When new information arrives, re-evaluate the register and invalidate stale
pending computations if necessary.

## Numerical and execution provenance gate

For every computed result, preserve the chain **input dataset/version → filters/exclusions → transformations → estimator inputs → executed computation → reported result**. If a categorical covariate expands into multiple balance terms, document the mapping so substantive covariate counts can be reconciled with displayed diagnostic-term counts.

Use execution labels precisely: **EXECUTED/VERIFIED** means the stated computation was actually run on the identified input; **SUPPLIED/UNEXECUTED** means code was provided but not run; **INDEPENDENTLY REPRODUCED** means a separate implementation reproduced the result after the design and inputs were reconciled. Do not describe unexecuted replication code as the code that generated a verified result.

When randomization or sampling reproducibility matters, record the software/language, seed, and RNG method when available. RNG claims must correspond to the software actually used.

## Human-in-the-loop gate

When a substantive methodological choice is needed: explain the choice in
plain language; give concise options; recommend a defensible default and
why; allow a custom choice; pause for the user's decision; retain the
decision and proceed without redundant confirmation. Do not use human gates
for trivial implementation details that do not materially affect the
estimand or design.

**Gate enforcement:** if the user selects a pause/source-documentation option,
enter `BLOCKED / WAITING FOR HUMAN DECISION` and stop all downstream causal
computation. Decisions already made remain recorded but inert. Resume only
after every material blocker in the current issue register is resolved or the
user explicitly changes the design.

## Data intake (all workflows)

Before sampling or causal analysis, inspect: row/column counts; variable
names and types; missingness; duplicate rows; identifier uniqueness,
missing IDs, duplicate IDs; invalid or impossible values; treatment/outcome
coding; eligibility indicators; categorical levels and sparse cells;
obvious data-integrity conflicts. Never silently repair substantive data
errors — flag them and request a decision.

## Causal/PSM eligibility audit (before routing to a causal reference file)

Before estimating an effect, explicitly identify: treatment/exposure;
outcome; target population; estimand; candidate adjustment variables;
treatment timing; covariate timing; outcome timing; comparison definition;
available design information. If a causal question cannot be defined from
the available information, block rather than guessing.

**Treatment classification gate** — classify as binary, continuous,
ordinal, nominal/multivalued, time-varying, or unclear, then route:

- **Binary** → `references/psm_binary.md`
- **Continuous** → do NOT median-split, threshold, or otherwise dichotomize automatically; route to `references/gps_continuous.md` and obtain human agreement on the dose-response estimand/model
- **Multivalued/nominal or ordinal** → do NOT collapse categories automatically; numeric codes such as 0/1/2 do not establish ordering or a natural control group; route to `references/psm_multivalued.md`
- **Unknown treatment type** → BLOCK causal matching until treatment meaning/coding is resolved

**Covariate timing gate** — classify each candidate covariate as confirmed
pre-treatment; confirmed post-treatment/mediator; baseline outcome/measure
requiring design-specific consideration; timing uncertain; or substantive
role uncertain. Confirmed post-treatment variables are excluded from the
primary adjustment set. Timing uncertainty is BLOCKING for causal PSM
unless the user has an explicitly noncausal analysis objective. Do not
infer timing from names such as `baseline_`, demographic appearance, or
data type. Do not automatically include every available variable —
consider confounding relevance, temporal ordering, and possible
mediator/collider roles; do not add variables simply because they improve
balance.

**Missing data gate** — separate missing treatment, missing outcome,
missing covariates, missing identifiers, and missing design information.
Missing treatment generally excludes a unit from that treatment-defined
analysis. For missing outcomes, decide in advance how they affect the
propensity model, matching pool, and outcome estimator. For missing
covariates, require an explicit handling decision and document the
assumption. Do not casually state that missingness is MAR/MCAR merely
because no pattern is obvious — if the mechanism is not established, say so
and discuss possible selection bias. Do not state or imply that
complete-case analysis is unbiased merely because treatment/outcome/
covariate balance looks acceptable among the observed cases — balance among
survivors of listwise deletion says nothing about whether those cases are
representative of the full sample.

## Reference files

Read the relevant file(s) before executing that part of the workflow:

- `references/random_sampling.md` — simple and stratified random sampling
- `references/psm_binary.md` — binary-treatment PSM (estimand, propensity model, matching design, overlap, balance, sensitivity, stopping rule)
- `references/gps_continuous.md` — continuous-treatment GPS / dose-response
- `references/psm_multivalued.md` — 3+ category treatment (pairwise or generalized framework)
- `references/diagnostics.md` — shared overlap/balance/reuse diagnostics and the stopping rule, used by both binary PSM and GPS
- `references/inference.md` — estimator-specific inference rules (e.g. Abadie-Imbens), what may and may not substitute for it
- `references/reproducibility.md` — the minimum record to keep and the code/result reconciliation check

## Causal interpretation (applies across all causal reference files)

Use layered language: **Observed** (what is in the dataset), **Computed**
(what the specified algorithm actually estimated), **Design-supported**
(what the diagnostics permit the evaluator to say), **Causal
interpretation** (conditional on the identifying assumptions).

Never say matching "proves" causality. Never say a treatment effect is
identified merely because a propensity model converged, matches were
found, SMDs improved, a 0.10 threshold was crossed, the point estimate is
stable across specifications, or an inference statistic is significant —
stable estimates across flawed designs do not rescue identification. When
balance/overlap is inadequate, say so plainly. When a numerical estimate
exists but causal credibility fails, separate the **computed estimate**
from the **causal interpretation verdict**.

**Every PSM/GPS result presented to the user must separate three layers:**

1. **What was computed** — e.g. "ATT = 7.98."
2. **What the diagnostics show** — e.g. "649 treated observations retained;
   maximum SMD = 0.109; one treated observation outside support."
3. **What can be claimed** — e.g. "This should not be interpreted as a
   fully credible causal ATT because residual imbalance remains."

Do not collapse these into a single unqualified sentence like "the
treatment effect is 7.98."

## Resume protocol after a human gate

When a blocker is resolved, resume from the latest valid state rather than restarting blindly. Reconcile all retained decisions against the newly resolved design before computation. Apply decisions in a documented order (for example: source-data correction/re-key → missing-data rule → eligible covariate set → propensity model → matching → diagnostics → QA). Do not execute a previously invalidated computation. If a newly supplied fact changes a previously resolved decision, explicitly invalidate and replace the affected design element.

For subgroup analyses, if the requested subgroup is not defined in the supplied data/design, record the limitation and do not manufacture a threshold or composite definition.

## Output files

Where applicable produce: audit report; issue register; design/method
decision record; reproducible code; diagnostics tables/plots;
matched/sample datasets; final analysis summary. Never claim a file was
created, saved, or shared unless it actually exists and is available.

## Final self-audit (before calling any analysis complete)

1. Did I invent any data/design fact?
2. Did I infer treatment ordering/control status from codes?
3. Did I infer covariate timing from names?
4. Did I include a post-treatment variable?
5. Did I silently dichotomize a continuous exposure?
6. Did I silently collapse multivalued treatment?
7. Did I confuse overlap with balance?
8. Did I treat 0.10 SMD as a hard pass/fail?
9. Did I tune specifications because diagnostics/results were unfavorable?
10. Does the reported estimand match the retained population after trimming?
11. Does the inference method match the actual estimator?
12. Were SE/CI/significance actually computed?
13. Can every reported number be traced to computation/code?
14. Did I explain a diagnostic change without evidence for the explanation?
15. Did I overstate causal interpretation?
16. Did I preserve the original data and document transformations?
17. Did I report all blockers and warnings?
18. Does every numerical result have a provenance/status label?
19. Does the retained population match the stated estimand?
20. Does the software actually implement the estimator described (not a convenient approximation labeled as if it were the named estimator)?
21. Does every caliper, distance, trimming rule, transformation, and diagnostic threshold use the same scale and variable described in the analysis?
22. Do reported N, treatment counts, exclusions, retained units, and output populations reconcile to the identified input dataset/version?
23. Are executed/verified computations clearly distinguished from supplied but unexecuted code and independently reproduced results?
24. Were any alternative specifications selected because they produced a preferred estimate?
25. Can another researcher reproduce the result from the recorded code, seed, software/RNG information, data subset, and design specification?
26. Did any pending human gate remain unresolved while downstream computation proceeded?
27. Was every temporal-order classification supported by design documentation or explicit confirmation rather than diagnostic patterns?
28. Was treatment intensity/dose distinguished from mediator, treatment component, and confounder roles?
29. Were duplicate IDs resolved without silent deletion or unapproved re-keying?
30. Is missingness separately accounted for, including overlapping exclusion sets?
31. Was any complete-case analysis described without claiming it is unbiased merely because balance is acceptable?
32. Was any subgroup definition taken from the supplied design rather than invented from arbitrary cutpoints?
33. If local positivity was not assessable, was that limitation stated rather than hidden?
34. Was control reuse quantified without an arbitrary universal invalidity threshold?
35. Does the reported ATT explicitly correspond to the retained treated population after support/caliper restrictions?
36. Were multiple plausible specifications reported without result-driven ranking or selection?
37. Were outcome-derived and post-treatment variables excluded from the primary adjustment set?
38. Was any outcome regression described without claiming it removes residual confounding?
39. If code and report differed, was the estimator implementation audited before any numerical result was accepted?
40. Was QA genuinely independent, with any QA-code bug disclosed and corrected?
41. Is `COMPLETE` being used only for workflow completion, not as a synonym for proven causal identification?
42. Does every reported aggregate diagnostic count reconcile exactly with the underlying table it was computed from, rather than being hand-counted?

If any answer exposes a material problem, correct it or mark the analysis
appropriately before calling it complete.

## Regression-derived validation safeguards

The Skill has been stress-tested across clean and adversarial workflows. The following observed failure modes are treated as permanent safeguards rather than one-off test notes:

- Sampling reproducibility must reconcile seed, RNG/software, expected-vs-observed calculations, and executable code.
- Matching/caliper claims must match estimator inputs and distance scale.
- Unexecuted code must never be described as having generated a verified result.
- Post-treatment/mediator leakage must be blocked, while treatment-intensity variables require role classification rather than automatic mediator labeling.
- Timing cannot be inferred from SMDs, correlations, distributions, or model behavior.
- Human pauses are global blockers; settled decisions remain recorded but inert until all material gates resolve.
- Missingness requires an explicit handling decision and complete population accounting.
- Duplicate IDs require source verification or explicitly authorized re-keying; no silent deletion.
- Extreme control reuse is quantified and reviewed rather than rejected by an arbitrary universal cutoff.
- Poor overlap/local positivity must not trigger silent subgroup deletion or invented subgroup definitions.
- Result-driven specification shopping is prohibited.
- Retained-vs-dropped treated units must be reconciled so estimand drift is visible.
- Code/report discrepancies require estimator-level reconciliation before accepting numerical results.
- `COMPLETE` and `NOT CREDIBLE FOR CAUSAL INTERPRETATION` are distinct concepts and may coexist conceptually: a workflow can finish while its causal interpretation remains qualified or unsupported.

## Data governance

Minimize unnecessary personal/sensitive data in AI-assisted workflows.
Prefer anonymized identifiers and only the fields needed for the analysis,
subject to the researcher's organizational, ethical, contractual, and legal
requirements.