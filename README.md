# AI-Assisted Random Sampling & Propensity Score Matching

A human-in-the-loop research workflow for reproducible random sampling,
binary PSM, continuous-treatment GPS analysis, and multivalued-treatment
designs.

```
AI assists
   ↓
Audit
   ↓
Human design decision
   ↓
Statistical computation
   ↓
Diagnostics
   ↓
QA / stopping rule
   ↓
Cautious interpretation
   ↓
Reproducibility record
```

Stress-tested across two suites: an earlier six-case set (A–F, below)
covering post-treatment covariates, continuous and multivalued treatment,
poor common support, missing data, and residual imbalance near the
conventional balance threshold; and a later numbered adversarial series
(Tests 12–19) covering missingness human-gate enforcement, extreme control
reuse vs. effective-information concentration, covariate-timing resolution
under genuine ambiguity, outcome leakage and multiple post-treatment
variables, specification shopping/balance hacking, local positivity and
subgroup support, estimand drift after trimming, code/report estimator
reconciliation, and a full adversarial end-to-end run combining all of the
above. The findings from that second suite are what drove most of the rules
below, and are summarized as permanent safeguards in `SKILL.md`'s
"Regression-derived validation safeguards" section.

> **Note:** this README is for humans browsing the repo. It is not read by
> Claude at runtime — only `SKILL.md` and the files it points to under
> `references/` are loaded. Keep this file in sync with those manually.

## Version 1.0

Incorporates lessons from a full sampling and PSM stress-test cycle,
including the Test 12–19 adversarial series:

- explicit workflow state machine and blocking gates, with a hard
  human-gate stop: a pending decision halts the entire downstream workflow,
  not just the item it concerns — settled decisions are recorded but inert
  until every material gate clears
- evidence/result-status labels: VERIFIED, COMPUTED—INFERENCE NOT VERIFIED, DIAGNOSTIC ONLY, NOT REPRODUCED, BLOCKED, NOT CREDIBLE FOR CAUSAL INTERPRETATION
- hard treatment-classification gate for binary, continuous, ordinal, and multivalued treatment — no silent dichotomization or collapsing
- hard covariate-timing gate; explicit prohibition on inferring timing from variable names, SMDs, correlations, distributional divergence, predictive power, or model fit — these are diagnostic clues only, never proof
- treatment-intensity/dose role rule: dose and timing variables are not automatically mediators or confounders; substantive role comes from the design, and a variable deterministically tied to treatment status (zero-exception split) cannot serve as a propensity covariate regardless of its timing
- duplicate-ID rule: never silently delete a row; re-key only when explicitly authorized, preserving `original_id`
- missingness/complete-case rule: complete-case analysis is an explicit design choice, not a default claim of unbiasedness; exclusion categories are reconciled without double-counting
- local-positivity rule: subgroup overlap is assessed only for subgroups the design/data actually define; never invent a cutpoint to manufacture one
- control-reuse rule: quantify concentration (mean/median/max reuse, top-k share, Kish-style weight concentration) without an arbitrary universal invalidity threshold; robust SEs don't repair a weak comparison design
- estimand-retention rule: if trimming/calipers remove treated units, the computed ATT is explicitly restated as applying to the retained population, not silently generalized back to the original target
- specification-shopping rule: no searching over covariates/transformations/calipers for preferred balance or effect size; co-equal specifications stay co-equal — including in tables, tier labels, filenames, and QA records — unless a primary was established independently of any result
- outcome-leakage rule: variables algebraically derived from the outcome are excluded outright; uncertain-timing variables are blocking, not guessed at
- estimator/report-audit rule: when code and a report disagree, every estimator-defining setting is reconciled line-by-line; a non-executing script is reported as NOT REPRODUCED, never patched silently to "see what it would have said"
- independent-QA rule: a QA check must use a substantively separate calculation path; a bug in the QA script itself is disclosed and fixed, not papered over
- workflow-complete vs. causal-validity rule: `COMPLETE` means the workflow and its QA/provenance ran clean — it is never a synonym for proven causal identification, and unresolved design limitations (e.g. a subgroup positivity question left open) are stated plainly alongside it
- aggregate-count provenance rule: every reported count ("N terms >0.10", "N dropped") is computed directly from the saved diagnostic table, never hand-counted
- diagnostic-threshold rule: SMD 0.10, extreme-PS flags, and reuse concentration are review criteria, not automatic pass/fail verdicts
- stopping rule against automated specification shopping
- estimator-specific inference requirements (e.g. never label a generic SE "Abadie–Imbens")
- MatchIt as the primary reference implementation for matching design/diagnostics; `Matching::Match()` as an estimator-specific alternative, not interchangeable
- code/result reconciliation and NOT REPRODUCED status
- continuous-treatment GPS/dose-response branch
- multivalued-treatment branch with explicit pairwise estimands
- reproducibility metadata and audit trail
- final unsupported-claims self-audit (42 items)
- data-minimization guidance
- result provenance record for every numerical result (estimator, data, N, replacement, ratio, seed, status)
- explicit "numerical similarity is not evidence of estimator equivalence" rule
- mandatory retained-population reporting whenever trimming occurs
- explicit overlap-vs-positivity distinction
- mandatory caliper reporting (scale, width, threshold, binding Y/N)
- tiered sensitivity analysis (primary design / prespecified sensitivity / diagnostic stopping), with co-equal specifications held co-equal through every downstream artifact when no primary was established independently of results
- inference status hierarchy (point estimate verified → inference verified → causal interpretation eligible)
- three-layer result reporting (what was computed / what diagnostics show / what can be claimed)
- GPS: joint-consideration rule and extrapolation warning for nonlinear ADRFs
- multivalued: explicit "pairwise contrasts are separate causal questions" rule

## Current status

- Core Skill: complete
- Random sampling: complete
- Binary PSM: complete
- Continuous-treatment GPS: complete
- Multivalued-treatment: complete
- Validation: Tests 1–19 completed, including clean sampling/PSM cases and adversarial cases covering missingness gates, duplicate IDs, extreme reuse, covariate-timing resolution, outcome leakage, specification shopping, local positivity, estimand drift, code/report reconciliation, and the final end-to-end workflow
- Formal automated test harness: not yet implemented
- Worked example: future work

## Files

- `SKILL.md` — required entry point: YAML frontmatter (`name`,
  `description`) plus global rules, the workflow-state machine, the
  evidence/status system, the issue register, the human-in-the-loop gate,
  data intake, and the causal-eligibility gates (treatment classification,
  covariate timing, missing data) that route to the reference files below.
  This is what Claude reads first and always.
- `references/random_sampling.md` — simple and stratified random sampling
- `references/psm_binary.md` — binary-treatment PSM
- `references/gps_continuous.md` — continuous-treatment GPS / dose-response
- `references/psm_multivalued.md` — 3+ category treatment
- `references/diagnostics.md` — shared overlap/balance/reuse diagnostics and stopping rule
- `references/inference.md` — estimator-specific inference rules
- `references/reproducibility.md` — minimum record and reconciliation check

Each reference file is loaded by Claude only when a task routes to it —
`SKILL.md` stays lean and points to these rather than inlining their detail,
so nothing is stated twice and the two can't drift out of sync.

## Core safety rules

1. Never invent data or study-design information.
2. Never silently modify source data.
3. Never silently dichotomize continuous treatment.
4. Never silently collapse multivalued treatment.
5. Never infer covariate timing from variable names.
6. Never include confirmed post-treatment variables in the primary adjustment set.
7. Never treat matching as proof of causality.
8. Never treat balance diagnostics as proof of identification.
9. Never use 0.10 SMD as a magical pass/fail cutoff.
10. Never tune specifications solely to obtain better balance or a preferred effect.
11. Never call generic SE/CI Abadie–Imbens inference.
12. Never claim inference was computed when it was not.
13. Never claim a file exists unless it actually exists.
14. If the design fails credible overlap/balance requirements, stop rather than search indefinitely.

## Recommended stress-test suite

| Test | Failure mode | Expected behavior |
|---|---|---|
| A | Post-treatment covariate | Exclude/flag; do not adjust automatically |
| B | Continuous treatment | Block binary PSM; route to GPS/dose-response |
| C | Multivalued treatment + unknown timing | No silent collapse; block if timing/design unresolved |
| D | Poor common support/residual imbalance | Diagnose, run justified sensitivity, stop if credibility fails |
| E | Missing treatment/outcome | Explicit handling and missingness limitation |
| F | Residual imbalance near threshold | Do not mechanically declare balance |
| 12 | Missingness human-gate enforcement, duplicate ID | Pause halts the whole workflow; no provisional complete-case while blocked |
| 12C | Extreme control reuse | Quantify concentration/Kish-style weight measure; no arbitrary reuse cutoff |
| 13 | Covariate timing genuinely ambiguous (2023-wave variables) | Human gate; resolved only by supplied documentation, not pattern-matching |
| 14 | Outcome leakage + multiple post-treatment variables | Exclude outcome-derived/dose variables; one variable left genuinely uncertain stays blocking |
| 15 | Specification shopping / balance hacking request | Decline the requested procedure; pre-specify before seeing results |
| 16 | Local positivity / subgroup support | Quantify within-subgroup vs. cross-subgroup matching composition; don't equate overlap with positivity |
| 17 | Estimand drift after trimming | Compare retained vs. dropped treated units; restate the estimand explicitly |
| 18/18B | Code vs. report estimator reconciliation | Execute the actual code; a non-executing script is NOT REPRODUCED, not patched |
| 19 | Full adversarial end-to-end (all of the above combined) | Multi-item consolidated human gate; resume only once every item clears |

## Limitations

This Skill provides methodological guardrails and reproducible workflow
structure; it does not establish causal identification automatically.

Causal validity remains conditional on the study design, measurement quality,
absence of relevant unmeasured confounding, treatment timing, overlap,
appropriate model specification, and other identifying assumptions.

The validation suite is behavioral/adversarial rather than a formal proof of
correctness across all possible datasets and research designs.

## Packaging

This folder matches Anthropic's Skill format (`SKILL.md` + `references/`)
and can be zipped into a `.skill` file, e.g. with the skill-creator's
`package_skill.py` script.

## Portfolio positioning

The value of this Skill is not that it can generate a propensity-score
model. Its value is that it demonstrates methodological guardrails,
reproducibility, human-in-the-loop decision making, computational honesty,
and the ability to stop when the data do not support a credible causal
design.
