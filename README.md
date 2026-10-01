# AI-Assisted Random Sampling & Propensity Score Matching

A reusable methodological Skill for structured, reproducible workflows involving:

- Random and stratified sampling
- Binary-treatment propensity score matching (PSM)
- Continuous-treatment generalized propensity scores (GPS) and dose-response analysis
- Multivalued-treatment designs
- Overlap and balance diagnostics
- Missing-data and covariate-timing audits
- Reproducibility and provenance checks
- Human-in-the-loop methodological decisions
- Code/report reconciliation and independent QA

The Skill is designed around a simple principle:

> **AI assists → statistical software computes → diagnostics assess → human QA/review → interpretation is cautious**

It is intended to provide methodological structure and guardrails, not to automate causal judgment.

---

## What this Skill does

The Skill provides a structured workflow for moving from raw data and study design to a reproducible sampling or matching analysis.

It can help with:

1. **Sampling-frame audits**
2. **Simple random sampling**
3. **Stratified random sampling**
4. **Treatment and outcome audits**
5. **Covariate timing classification**
6. **Missing-data assessment**
7. **Binary propensity-score matching**
8. **Continuous-treatment GPS / dose-response analysis**
9. **Multivalued-treatment matching**
10. **Common-support and overlap assessment**
11. **Covariate-balance diagnostics**
12. **Matching reuse/concentration diagnostics**
13. **Estimand and retained-population accounting**
14. **Sensitivity analysis**
15. **Inference and standard-error checks**
16. **Reproducibility and provenance**
17. **Code/report reconciliation**
18. **Independent numerical QA**

The workflow can also stop when the available data or study design do not support a defensible downstream analysis.

---

# Core workflow

The Skill follows this sequence:

```text
Data / Study Design
        ↓
Data & Design Audit
        ↓
Human Design Decision
        ↓
Treatment / Sampling Method Selection
        ↓
Statistical Computation
        ↓
Diagnostics
        ↓
Independent QA
        ↓
Stopping / Sensitivity Decisions
        ↓
Cautious Interpretation
        ↓
Reproducible Output

A central requirement is that the Skill distinguishes between:
- AI reasoning
- statistical computation
- diagnostic evidence
- human decisions
- causal interpretation
AI should not be treated as a substitute for statistical software or human methodological review.
Human-in-the-loop design
Some decisions cannot be established reliably from variable names, correlations, balance statistics, or model output alone.
Examples include:
- Whether a variable was measured before treatment
- Whether an ambiguous variable is post-treatment
- Whether a variable represents treatment intensity, exposure, a mediator, or a baseline characteristic
- How missing data should be handled
- Whether duplicate IDs represent duplicate records or distinct units
- Whether a subgroup is substantively defined
- Whether alternative specifications should be treated as primary or co-equal
When such information is necessary, the Skill can pause and request a human decision or source documentation.
If the user chooses to pause, downstream causal computation remains blocked until the material issue is resolved or the design is explicitly changed.
Statistical methods covered
Random sampling
The Skill supports:
- Simple random sampling
- Stratified random sampling
- Proportional allocation
- Largest-remainder allocation
- Seeded reproducible selection
- Sampling-frame validation
- Duplicate-ID checks
- Sample-count reconciliation
- Reproducibility checks
Random samples are actually computed using statistical software rather than fabricated by the language model.
Binary-treatment PSM
The binary-treatment workflow includes:
- Treatment coding checks
- Outcome checks
- Covariate eligibility audits
- Pre-treatment timing checks
- Logistic propensity-score models
- Nearest-neighbor matching
- Matching with or without replacement
- Caliper specification
- Common-support assessment
- Post-match balance
- Control reuse diagnostics
- ATT/ATC/other estimand accounting
- Sensitivity specifications
- Estimator-specific inference
- Independent QA
The Skill explicitly distinguishes between:
- raw propensity-score distance
- logit propensity-score distance
- standardized calipers
- numerical caliper widths
A stated caliper must correspond to the scale actually supplied to the estimator.
Continuous treatment
For genuinely continuous treatment or dose variables, the Skill routes to a GPS/dose-response workflow rather than automatically dichotomizing the treatment.
The workflow considers:
- Treatment distribution
- Treatment-model specification
- Covariate timing
- Treatment-model diagnostics
- GPS construction
- Overlap
- Joint balance
- Dose-response functional form
- Nonlinearity
- Average dose-response functions
Normality or functional-form assumptions are not established from a single diagnostic test alone.
Multivalued treatment
For treatments with three or more categories, the Skill first distinguishes between:
- nominal treatment
- ordinal treatment
- genuinely continuous treatment
Numeric category codes are not automatically interpreted as meaningful treatment ordering.
For nominal multivalued treatment, the workflow uses explicit pairwise comparisons where appropriate.
Each pairwise comparison represents a separate causal question and therefore requires its own:
- treatment definition
- propensity-score model
- matching procedure
- overlap assessment
- balance assessment
- estimand
- estimate
- inference
Methodological guardrails
The Skill contains explicit safeguards against common analytical failures.
No fabricated samples
The language model does not invent a random sample or claim a random draw without computation.
No silent data deletion
Duplicate IDs, missing observations, and excluded observations must be accounted for explicitly.
No post-treatment covariate leakage
Variables measured after treatment or derived from post-treatment outcomes are not silently included in the primary propensity model.
Deterministic treatment variables
A variable that is a deterministic function of treatment status cannot be used as a propensity-score covariate, regardless of how it is temporally classified.
No balance hacking
The Skill does not search over covariates, transformations, calipers, matching settings, or outcome specifications simply to obtain a preferred balance statistic or treatment effect.
No automatic causal claims
Good balance or overlap does not by itself establish causal identification.
No automatic interpretation of mechanisms
The Skill does not infer mediation, mechanisms, treatment ordering, or substantive meaning merely from variable names or statistical patterns.
No estimand drift
If trimming, support restrictions, or caliper restrictions change the treated population, the resulting estimate is reported for the retained population rather than silently generalized to the original population.
Numerical provenance
Reported numerical results must be traceable to:
- the input dataset
- the relevant population and filters
- transformations
- estimator settings
- software
- execution status
- diagnostic objects or tables where applicable
Code/report reconciliation
Reported results are not accepted merely because they appear plausible. The reported method must be reconciled with the actual analysis code and execution.
Validation
The Skill was evaluated using 19 behavioral and adversarial tests.
The tests cover both the core methodological workflows and increasingly difficult failure modes.
Tests 1–11 — Core workflow and methodological validation
Test	Area	Main behavior tested
1	Random sampling	Simple random sampling
2	Sampling frame	Missing/duplicate identifiers and invalid frame handling
3	Stratified sampling	Stratified allocation and reproducible selection
4	Reproducibility	Seed reproducibility and numerical provenance
5	Binary PSM	Basic propensity-score matching workflow
6	Post-treatment variables	Exclusion of post-treatment/mediator variables
7	Continuous treatment	GPS and dose-response workflow
8	Multivalued treatment	Pairwise treatment comparisons and extreme propensity scores
9	Poor overlap	Failure to support causal matching under disjoint support
10	Missingness	Missing-data accounting and complete-case handling
11	Human gate	Blocking downstream computation when a required human decision is unresolved


These tests established the core sampling, binary PSM, continuous-treatment, multivalued-treatment, overlap, missingness, provenance, and human-gate behavior.
Tests 12–19 — Adversarial and publication-hardening validation
Test	Area	Main behavior tested
12	Missingness / reuse	Missingness gate, complete-case analysis, and control reuse
13	Timing	Treatment timing, ambiguous dates, and deterministic treatment variables
14	Outcome leakage	Post-treatment variables, outcome-derived variables, and unresolved timing
15	Specification shopping	Resistance to balance hacking and preferred-estimate selection
16	Local positivity	Subgroup-specific overlap and human review
17	Estimand drift	Retained treated population after support/caliper restrictions
18	Reproduction audit	Missing artifacts and code/report reconciliation
19	End-to-end workflow	Multiple simultaneous data, timing, duplicate-ID, and design issues


Tests 12–19 extended the earlier validation suite by introducing adversarial situations designed to test whether the workflow would resist methodological shortcuts and provenance failures.
Validation status
Tests 1–19 completed.
The validation suite is behavioral and adversarial rather than a formal proof of correctness for every possible dataset or research design.
A passing test means that the Skill exhibited the intended workflow behavior for that test case. It does not mean that the Skill can establish causal validity automatically.
Methodological lessons from validation
The validation suite specifically tested whether the workflow would:
- stop when treatment timing is unresolved
- stop when a material covariate's status is ambiguous
- preserve unresolved human decisions as blockers
- distinguish diagnostic clues from evidence of temporal ordering
- exclude deterministic treatment variables from propensity models
- avoid outcome leakage
- distinguish treatment intensity from a mediator
- avoid specification shopping
- distinguish overlap from positivity
- report the retained treated population after trimming
- quantify control reuse rather than hiding it
- reconcile aggregate diagnostic counts with underlying diagnostic tables
- distinguish numerical similarity from estimator equivalence
- detect discrepancies between reported methods and executable code
- avoid claiming reproduction when the actual computation was not reproduced
- preserve provenance for numerical results
Diagnostics
Diagnostics are treated as evidence for review rather than automatic proof of causal validity.
The Skill reports, where applicable:
- propensity-score overlap
- retained and excluded observations
- standardized mean differences
- variance ratios
- number of covariates exceeding descriptive balance thresholds
- improved/worsened balance
- control reuse
- concentration of matched controls
- effective comparison-population information
- dose-response diagnostics
- treatment-model diagnostics
A commonly used absolute SMD threshold of 0.10 may be reported as a descriptive benchmark.
It is not treated as a universal causal-validity threshold.
Aggregate diagnostic counts must be computed directly from the underlying diagnostic table rather than manually counted.
Reproducibility
The Skill aims to preserve a reproducible record of:
- Input data and version
- Sampling frame
- Filters
- Transformations
- Random seed
- Treatment definition
- Outcome definition
- Covariate set
- Matching specification
- Distance metric
- Caliper and scale
- Replacement setting
- Estimand
- Support restrictions
- Outcome estimator
- Standard-error method
- Clustering
- Software
- Package versions where available
- Human decisions
- Diagnostic results
- QA results
A result that cannot be traced to an executable computation is labelled accordingly rather than being presented as verified.
Limitations
This Skill provides methodological guardrails and reproducible workflow structure; it does not establish causal identification automatically.
Causal validity remains conditional on the study design, measurement quality, absence of relevant unmeasured confounding, treatment timing, overlap, appropriate model specification, and other identifying assumptions.
The validation suite is behavioral/adversarial rather than a formal proof of correctness across all possible datasets and research designs.
