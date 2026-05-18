# Security Policy

This repository is a defense-research publication. It does not ship runnable code with attack surface of its own, but the patterns it documents may be deployed into production systems by third parties.

## Reporting a vulnerability in a deployment of this pattern

If you discover an attack that bypasses the Always-Loaded Guard Rules pattern in a real deployment, **please disclose responsibly**:

1. **Do not** post the bypass publicly first. Concrete attack tokens published to a public channel become reusable adversarial dictionaries — this is the same reason §5.2 of the paper does not list our own observed tokens.
2. Open a private GitHub Security Advisory on this repository, OR contact CY Future via the channel listed at the bottom of this file.
3. Provide:
   - The runtime / agent framework on which the bypass was observed
   - An abstracted description of the bypass mechanism (category, not the exact prompt string)
   - The defense version against which it was tested (link to a specific git tag / commit)
4. Allow CY Future a reasonable window to respond before any public disclosure — 90 days is the default expectation, shorter if there is active exploitation.

## What we will do

- Acknowledge receipt within 7 days
- Assess the bypass against v1.x and any unreleased v2 work
- If the bypass is generalizable, incorporate fixes / caveats into the next release
- Credit the reporter in the CHANGELOG, unless the reporter requests anonymity

## What is out of scope

- Reports targeting model vendors' baseline alignment (those go to the vendor, not to this repository)
- Reports targeting attack surfaces explicitly listed as out of scope in paper §1.4 (indirect injection, GCG/AutoDAN, context flooding, cross-modal, etc.) — these are v2 research targets, not v1.0 defects. **Note for deployers**: indirect-injection bypasses (and other out-of-scope surfaces) found against your own deployment are still real vulnerabilities of *your* system. Report them to your model vendor and runtime maintainer; this scope decision applies only to what this v1.0 paper claims to defend, not to what you should worry about.
- Reports consisting only of generic adversarial prompts without a defense-bypass framing
- Reports requesting publication of concrete attack tokens — see point 1 above

## Reporters

This policy benefits from the security research community. We thank in advance anyone who reports responsibly.

## Contact

Open a GitHub issue with the `security` label for non-sensitive coordination, or use GitHub Security Advisories (private) for vulnerability reports.
