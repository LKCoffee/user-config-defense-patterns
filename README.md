# User-Controlled Config Defense Patterns

A pattern paper and deployable defense template for LLM coding-agent runtimes that expose a user-controlled memory and system-context layer.

**Status**: Pilot study, n=5 on the defense reliability claim. Pattern paper, not a comprehensive red team.

**Languages**: English (canonical) · [繁體中文](./ZH/README.md)

**Project**: This is v1.0 of a **long-term defense research project**. The trigger for starting it was the visible proliferation of public-facing jailbreak tutorials on social platforms aimed at non-expert audiences — the attacker population is moving from skilled adversaries to commodity users following step-by-step guides. Defense research should keep pace.

---

## Quick start (30 seconds)

1. Identify the **always-loaded preamble** of your LLM agent's context. (Memory index file? System prompt? Assistant instructions? See [template](./always_loaded_guard_rules_template.md) for runtime-specific mappings.)
2. Copy the defense block from [`always_loaded_guard_rules_template.md`](./always_loaded_guard_rules_template.md) to the top of that location.
3. Read [§5.4 of the paper](./Defense_Pattern_Paper_v1.0.md) to understand the schema lock is a *soft* (behavioral) lock, not a runtime enforcement.
4. Maintain your own private threat-list of the concrete attack tokens you observe — do not publish it.

If you can't find an always-loaded surface in your runtime, this pattern doesn't apply; see the template's "Adapting to your runtime" section.

---

## What this is

We red-teamed eight attack vectors against the user-controlled config layer of an LLM coding agent. Seven were detected/refused at the model's alignment layer. One produced a partial persona shift but its payload was not delivered — an architectural side-effect, not active defense.

From the test we extracted four architectural insights and one deployable defense pattern.

## What this is not

- **Not a comprehensive red-team report.** Indirect injection (web/PDF/file-embedded), academic SOTA adversarial suffixes (GCG / AutoDAN family), cross-modal payloads, tool/function-call manipulation, and context-window flooding are **not** in scope. See OWASP LLM Top 10 mapping in paper §1.5.
- **Not a cross-model claim.** Only one model family tested.
- **Not production-ready.** n=5 with a Wilson 95% CI lower bound of 56.6%. Replicate at n≥30 before deploying as your only line of defense.

## Contents

| File | What |
|------|------|
| [`./Defense_Pattern_Paper_v1.0.md`](./Defense_Pattern_Paper_v1.0.md) | The main paper. Threat model, attack catalogue (abstracted), four architectural insights, defense pattern, limitations |
| [`./always_loaded_guard_rules_template.md`](./always_loaded_guard_rules_template.md) | The standalone defense template, with runtime-adaptation guide for OpenAI Assistants API, LangChain, and self-built agents |
| [`./examples/`](./examples/) | A sample deployed memory index showing the defense block in context |
| [`./LICENSE`](./LICENSE) | CY Future Open Defense License v1.0 |
| [`./CITATION.cff`](./CITATION.cff) | GitHub-native citation metadata |
| [`./SECURITY.md`](./SECURITY.md) | Responsible disclosure channel |
| [`./CHANGELOG.md`](./CHANGELOG.md) | Version history |
| [`./ZH/`](./ZH/) | Traditional Chinese parallel translation |

## The four insights, in one paragraph each

1. **Lazy-loading.** Memory entry bodies are not loaded at session start — only the index header and pointers. Attacks placed in entry bodies depend on the agent recalling them.
2. **Skill init ≠ agent recall.** When a skill is invoked, it loads its own profile, not memory entries. But the main agent loop can recall entries in later turns. The two paths have different trust semantics.
3. **System-context attacks self-defeat.** Attacks at the system-context layer trigger a heightened-vigilance behavioral state in the agent that suppresses other simultaneous attacks. Obviousness brings detection.
4. **Placement determines reliability.** Defense rules at always-loaded positions (memory index header, system-context file) consistently engage. Defense rules at on-demand-loaded positions (entry footers) are stochastic. Placement matters more than wording.

## The pattern, in one sentence

**Put your defense rules in the memory index header — the always-loaded preamble — not in individual entry footers.**

## Why we abstract attack tokens

The template lists **categories** of attack indicators, not the specific tokens we observed. Publishing reusable attack tokens shifts the marginal cost of attack down more than it helps defense. Each implementer should maintain a private threat-list of the concrete tokens they observe in their own environment.

## Reproducibility

The threat model and abstracted attack catalogue are sufficient to construct a comparable test harness. Specific attack content used in our tests is not published.

## License

**CY Future Open Defense License v1.0** — see [`LICENSE`](./LICENSE) for the full text.

Short version: free to use, modify, redistribute, and build upon for any purpose including commercial. **Attribution to CY Future required.** No warranty. Open derivatives encouraged but not legally required.

## About CY Future

CY Future is the author and maintainer of this work. v1.0 was prepared as an open research release; subsequent versions will be tagged via GitHub releases (this filename's `v1.0` suffix will not be updated in place — see [`CHANGELOG.md`](./CHANGELOG.md)).

For citation, attribution, or disclosure: open a GitHub issue, or reach out via the channel listed in [`SECURITY.md`](./SECURITY.md).

## Citation

```bibtex
@misc{cyfuture_user_config_defense_2026,
  title        = {Defending User-Controlled Config Against Prompt Injection:
                  Architectural Insights and the Always-Loaded Guard Rules Pattern},
  author       = {{CY Future}},
  year         = {2026},
  howpublished = {GitHub repository},
  note         = {Pilot study, v1.0},
  url          = {https://github.com/LKCoffee/user-config-defense-patterns}
}
```

(Replace `url` field with the actual repo URL on publish. Use a versioned tag for citing a specific release.)

GitHub-native citation metadata is also provided in [`CITATION.cff`](./CITATION.cff) for the "Cite this repository" button.

## Issues we expect — and our v2 roadmap

The four issues a competent reviewer will raise about v1.0 are **explicit targets for v2**, not deferred excuses:

| Reviewer issue | v2 commitment |
|----------------|---------------|
| "n=5 is too small" | RCT at n≥30, multiple context-fill ratios |
| "What about indirect injection?" | Test the Always-Loaded Guard Rules pattern against at least three indirect-injection carriers (web / PDF / structured file) |
| "Why no GCG / AutoDAN comparison?" | Run GCG (Zou et al. 2023) and AutoDAN (Liu et al. 2023) against the same defense; report transfer rate and defense engagement |
| "Multi-turn only tested T1?" | Run full T1-T7 cumulative chain with the defense in place; report failure turn (if any) |

See §7 of the paper for the full v2 roadmap.

## Why long-term

The visible shift in attacker population — from skilled adversaries to commodity attackers following public tutorials — means the marginal attacker now has lower skill but the same access. Defense research should produce **deployable patterns that a non-specialist can drop in**, not just academic findings. v1.0 is one such pattern. Subsequent versions aim for the same: every deliverable should be droppable into a real deployment by someone who is not an LLM security researcher.
