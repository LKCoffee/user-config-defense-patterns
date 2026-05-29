# v2 Research Progress Log

> **Append-only.** New entries go at the **bottom**. Never edit or delete a past
> entry — if something was wrong, add a new dated entry that corrects it and say
> so. The timeline is the point: it should show how the design evolved, including
> mistakes and their fixes. Each entry is stamped `YYYY-MM-DD` and carries the
> git commit it corresponds to (if any).
>
> Scope: the v2 study (the RCT that validates the Always-Loaded Guard Rules
> pattern at n≥30 with proper controls). v1.0 / v1.0.1 history lives in
> `../CHANGELOG.md`; this log starts where v2 design begins.
>
> This is the public, machine/reviewer-facing log (English). A private
> Chinese-language HTML companion may be added later for the operator's own
> reading; it does not replace this file.

---

## 2026-05-19 — v2 exists only as an unrun skeleton prereg

**State.** v1.0.1 is published (GitHub, tag `v1.0.1`). v2 is *designed-but-not-run*:
`PREREG_v2_template.md` is a `template-0` skeleton — hypotheses H1–H4 sketched,
all concrete parameters (δ, ε, n, attack battery, benign corpus) marked TBD. No
v2 trials have been executed; the only data in existence is the v1.0 pilot
(header placement 5/5 fire, n=5, Wilson 95% CI [56.6%, 100%]; footer ~1/3, n≈3;
Fisher exact 5/5 vs ~1/3 → p ≈ 0.07, does not clear α = 0.05).

**Known open gaps carried into v2** (from paper §6/§7, the v1.1 red-team report,
and the 0519 handoff): indirect injection untested; GCG/AutoDAN not run; context
flooding untested; multi-turn only T1 validated; single model only; n=5 wide CI.
Two gaps were noted in prose but **not yet designed into the prereg**: (a) no
no-block control arm — baseline alignment did most of the blocking in the pilot,
so the guard block's marginal contribution is not isolable; (b) false-positive /
over-refusal rate "not measured."

---

## 2026-05-29 — template-1: added control + specificity + operating-point arms (commit `a851324`)

**What changed.** Extended the prereg skeleton to close the two noted-but-undesigned
gaps, plus a statistical-test change:

- **No-block control arm** — H1 made three-arm (none / footer / header); primary
  contrast set to marginal efficacy π_header − π_none.
- **Specificity arm** — new H5: benign prompt corpus (ordinary / hard-negative /
  edge-pop-culture) to measure over-refusal; confusion-matrix benign row added.
- **Operating point** — new H6 (deployability metric).
- **Test change** — replaced the skeleton's two-proportion z-test with McNemar's
  test, on the reasoning that the data are paired/within-subjects; Bonferroni
  changed to α = 0.05/6.

**Status at this point: directionally right, but not yet audited.** The three
new arms address real gaps. The statistical specification had not yet been
checked. (See next entry — it did not survive audit.)

---

## 2026-05-29 — audit: template-1's statistics were mis-specified

**Process.** Two independent reviews of `a851324`, deliberately blind to each
other:
1. Same-model quality review (WHIP) + two same-model adversarial sub-reviews.
2. Cross-model **blind** review (Codex / different model family), shown only the
   neutral design facts — not the first review's findings.

**Convergent findings** (both reviews, independently — high confidence these are
real):
- **McNemar is wrong here.** Trials are clustered within ~5 vectors (repeated
  measures), not independent matched pairs. McNemar (and the older two-proportion
  z) treat clustered trials as independent → inflated Type-I error. Correct class:
  cluster-aware models (logistic GLMM or GEE, vector as cluster).
- **Bonferroni miscounted.** Dividing α by the number of hypothesis *labels* is
  wrong: some labels carry several tests, H6 is derived, H4 is descriptive.
  Correct: hierarchical gatekeeping on the actual confirmatory-test count.
- **Precision is base-rate-dependent** and meaningless on the study's artificial
  attack:benign mixture. Balanced accuracy / Youden's J (prevalence-robust) are
  the correct operating-point metrics. (BA/J were *not* a problem — the earlier
  worry about them was misdirected; precision was the actual error.)
- Decisions should compare a CI bound to a pre-registered margin, not a bare
  p-value; H5 is a non-inferiority claim, not an equality test.

**Cross-model-only findings** (caught by the blind Codex review, missed by the
same-model review — this is the value of cross-family audit):
- **Footer has no benign arm** → footer FP/TN unobservable → footer
  specificity / balanced-accuracy / Youden's J are unidentifiable; footer cannot
  be in H6.
- **Uncapped invalid-trial replacement** → selection bias if invalid rates
  differ by condition; also discards "model fails under attack" as an outcome.
- **H3 used ordinal logistic on a binary outcome** (category error).
- **FIRE coding conflated** non-execution with alert-visibility; silent
  non-execution was mis-coded as compliance.
- **H2 is one-sided non-inferiority**, not two-sided TOST.

**No conflicts.** No finding from one review was contradicted by the other.

**Verdict.** Push held. `a851324` is directionally correct but not safe to ship
as a methodology reference.

---

## 2026-05-29 — template-2: statistical plan corrected (commit `148fdc9`)

**What changed.** Rewrote the statistical plan to the union of both audits.
`a851324` is **kept in history** (not amended/squashed) so the mistake→fix
timeline is visible.

- Test → cluster-aware logistic GLMM/GEE (vector as cluster); omnibus + pre-planned
  contrasts for the 3-arm placement factor. Final GLMM-vs-GEE pick left **TBD**
  (a genuine design decision, not something to hard-code wrongly).
- Multiplicity → hierarchical gatekeeping; primary family (H1 efficacy, H5) at
  full α.
- Metrics → dropped precision from go/no-go; kept sensitivity / specificity /
  balanced accuracy / Youden's J; precision demoted to an optional prevalence
  curve.
- Decisions → CI-bound-vs-pre-registered-margin throughout.
- Footer removed from H6 (unidentifiable without a benign footer arm).
- Invalid replacement capped + CONSORT-style flow + invalids-as-failure
  sensitivity analysis.
- H3 → logistic on binary; H2/H5 → one-sided non-inferiority; H4 → chain-level
  endpoint; FIRE coding split (silent non-execution = defense success).
- Ceiling pre-screen moved out to a separate pre-study document (no pilot
  π_none exists yet — the v1.0 pilot had no no-block arm).
- δ split into δ_eff (efficacy margin) and δ_place (placement margin); primary
  hypotheses (H1, H5) reordered to the front.

**Status.** Template-2 is the current design baseline. Still design-only — **no
v2 experiments have been run.** Concrete parameters (δ_eff, δ_place, ε, ζ,
ζ_hard, locked n, GLMM-vs-GEE, invalid cap) remain TBD and are the next step.

**Caveat on this audit.** Both review passes are LLM-driven; the cross-model pass
reduces but does not eliminate shared-prior blind spots. Before the prereg is
*locked* (renamed `PREREG_v2.md` + tagged), the statistical plan should get one
human-statistician or third-tool pass — especially the small-cluster power
question (only ~5 vectors), which neither review fully resolved.

---

## Next steps (open — not yet done)

- [ ] Lock concrete parameters: δ_eff, δ_place, ε, ζ, ζ_hard, invalid-cap fraction.
- [ ] Resolve the small-cluster problem: raise cluster count beyond ~5 vectors, or
      pre-register a small-cluster correction; redo power at the adjusted α with an
      assumed ICC / discordant-pair structure.
- [ ] Decide GLMM vs GEE vs conditional logistic (and justify).
- [ ] If a ceiling pre-screen is used: collect a real pilot π_none (needs a
      no-block arm) and write it as a separate pre-study record before locking.
- [ ] One human / third-tool statistical pass before lock.
- [ ] Build the benign prompt corpus (3 strata) — needed for H5/H6.
- [ ] Only after all above: rename → `PREREG_v2.md`, tag, commit hash = canonical
      pre-registration record. (No trials before the prereg is locked.)
