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

## Unreleased

See `Defense_Pattern_Paper_v1.0.md` §7 for v2 commitments.
