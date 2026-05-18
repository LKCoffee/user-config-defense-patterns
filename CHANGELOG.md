# Changelog

All notable changes to this work are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this work adheres to semantic-versioning-adjacent conventions:

- **Major** (e.g., v2.0): new attack surface tested, new defense pattern introduced, or backwards-incompatible reframing
- **Minor** (e.g., v1.1): additional evidence (e.g., replication at higher n), additional runtime mappings, scope clarifications
- **Patch** (e.g., v1.0.1): editorial corrections, typo fixes, link repairs

Filename `v1.0` suffix is **not** updated in place; future releases live alongside or replace this version via git tags. See README "About CY Future" for details.

---

## [v1.0] — 2026-05-19

Initial public release.

### Added

- **Threat model** (paper §1) — user-controlled config layer of an LLM coding agent runtime
- **Attack catalogue** (paper §2) — V1-V6c, eight vectors grouped by config layer; numbering note explains internal-tracking provenance
- **Results table** (paper §3) — 7 refused, 1 partial persona shift (payload undelivered)
- **Four architectural insights** (paper §4):
  1. Lazy-loading of memory entries
  2. Skill init path ≠ agent recall path (with the caveat that agent recall *can* read entries)
  3. System-context attacks trigger high-alert state (behavioral observation, mechanism not quantified)
  4. Placement determines reliability (5/5 always-loaded vs ~1/3 stochastic footer in small samples)
- **Defense pattern** (paper §5) — Always-Loaded Guard Rules with explicit "How the schema lock works" section clarifying it is a behavioral lock, not runtime enforcement
- **OWASP LLM01 mapping** (paper §1.5)
- **v2 roadmap** (paper §7) with four explicit committed targets: indirect injection, context flooding, multi-turn T2-T7, GCG/AutoDAN comparison
- **Template** with **Adapting to your runtime** section covering OpenAI Assistants API, LangChain, and self-built agents
- **Examples** folder with sample deployed memory index
- **LICENSE** — CY Future Open Defense License v1.0
- **CITATION.cff** for GitHub-native citation
- **SECURITY.md** disclosure policy
- **ZH/** — Traditional Chinese parallel translation of paper, template, and README

### Audit history

This v1.0 release passed two independent audits prior to publication:

- **Rigor audit** (academic over-claim, statistical hygiene, EN-ZH consistency, sanitization, narrative residue, license clarity)
- **Smoke-test audit** (stranger downloads from GitHub: can they use it? what breaks? template drop-in to non-Claude runtimes)

Audit-driven changes captured in this release include: vendor-neutral terminology, attack-catalogue numbering note, denylisted-language replacement, BibTeX author field added, runtime-adaptation guide added, schema-lock mechanism explained, OWASP mapping added, About / contact section added, OPEN DERIVATIVES clause clarified as non-binding.

### Known limitations (carried forward to v2)

See paper §6 and README "Issues we expect" for the full list. Headline:

- Indirect injection — not tested
- GCG / AutoDAN comparison — not run
- Context flooding — not tested
- Multi-turn T2-T7 — not validated
- Cross-model generalization — single model only
- n=5 — wide CI; replication at n≥30 required

---

## [v1.0.1] — 2026-05-19

Patch release responding to external Codex audit of v1.0.

### Audit history

Codex external review of v1.0 returned a **HOLD** verdict (not GO, not ABORT): 3 critical + 5 substantive + 2 minor findings. An internal counter-audit (WHIP rigor + tech-debt) reclassified the set against v1.0.1's scope:

- **5 findings adopted here**: all 3 critical + both minor — assessed as valid and cheap to fix.
- **2 substantive findings pushed back**: runtime mappings are *architectural adaptation guidance*, not empirical claims; the v2 prereg file is explicitly a template skeleton.
- **4 nice-to-have findings deferred to v1.0.2**: see "Deferred" below.

We publish the audit history rather than conceal it: external review found real issues, the appropriate response is correction.

### Changed

- **§1.2 Attacker capability** — split attacker write-capability into content-level vs file-level / load-order. The pattern defends against content reframing within attacker-writable surfaces; file integrity (deletion, displacement, load-order tampering) is delegated to non-LLM controls (file ACL, deployment process, hash checks). *Codex Critical #1*
- **§1.6 (new)** — Reproducibility & Responsible Disclosure stated up-front: what is reproducible from this paper (threat model, abstracted catalogue, defense pattern, runtime mappings) vs what is not published by design (concrete attack tokens, raw transcripts). §8 reduced to cross-reference. *Codex Critical #2*
- **§5.6 Reliability caveat** — "significantly more reliable" replaced with "directionally consistent with a large reliability asymmetry"; added Fisher exact p ≈ 0.07 (does not clear α = 0.05). *Codex Critical #3*
- **`examples/sample_memory_index.md`** — `reference_apis.md — API credentials location pointer` replaced with `reference_runtime_endpoints.md — Non-sensitive runtime endpoint URLs (no credentials, no tokens)`. *Codex Minor #1*
- **`SECURITY.md`** — added deployer-facing note on the "out of scope" bullet: scope decision applies to what *this paper* defends, not to what *your deployment* should worry about. *Codex Minor #2*
- **Metadata consistency** — bumped paper Version header (1.0 → 1.0.1) and end-of-paper string (`End of v1.0` → `End of v1.0.1`) in both EN and ZH. *Codex re-review cosmetic finding*
- **§1.1 cross-reference correction** — removed incorrect `§5.4` reference for runtime mappings (§5.4 covers schema-lock semantics, not mappings); runtime mappings live in the accompanying template only. EN and ZH. *Codex re-review cosmetic finding*

### Pushed back (deliberately not changed)

- **Codex Substantive #3 (runtime mappings without evidence backing)**: the template's OpenAI Assistants / LangChain / self-built mappings are explicitly framed as architectural adaptation guidance, not empirical reliability claims. Evidence-backing across runtimes is a v2 cross-replication deliverable, not a v1.0.1 patch deliverable.
- **Codex Substantive #4 (prereg "TBD" too many)**: `v2_planning/PREREG_v2_template.md` is explicitly a template skeleton — locking δ, ε, sample size, and commit hash *is* the v2 design exercise the template scaffolds. Treating template TBDs as a deficiency inverts the purpose of a prereg template.

### Deferred to v1.0.2

- §3 V1 footnote — make explicit that baseline alignment is the dominant refusal mechanism on V1-V3 and the defense layer's marginal contribution is not isolable in this design
- §3 V4 row — re-frame as integrity failure (partial persona shift), not "payload undelivered" mechanism downplay
- New §1.7 Related Work — positioning vs Greshake 2023 (indirect injection), Zou 2023 (GCG), Liu 2023 (AutoDAN), OWASP LLM01
- Abstract — soften 5/5 framing to "5/5 in pilot, replication required" to reduce v2-cite liability
- §4 Insight 4 table — drop or expand "~1/3 in n≈3" (n=3 footer events is anecdote, not data)
- §4 / §5.6 — Wilson CI 56.6% lower-bound caveat written explicitly into the prose

---

## Unreleased

See `Defense_Pattern_Paper_v1.0.md` §7 for v2 commitments. v1.0.2 patch is queued (see [v1.0.1] "Deferred to v1.0.2" above).
