# Contributing to XTime

XTime is alpha software, so right now, honest feedback is worth more than a pull request. If you only have time for one thing, open an issue about something that felt broken, unclear, or unsafe. That's a real contribution.

## Before you start

This project is built around a specific idea: no server, no accounts, nothing to trust but the architecture itself. If you're proposing something that quietly reintroduces a central point of trust (a server component, a phone-home feature, telemetry), open an issue and discuss it first. It might still be worth doing, but it needs to be argued for, not assumed.

## Reporting bugs

Open a GitHub issue with:

- What you did, step by step
- What you expected to happen
- What actually happened
- Your OS and how you're running XTime (from source, packaged build, etc.)
- Logs, if you have them and they don't contain anything you don't want public (XTime doesn't scrub logs automatically yet)

If the bug touches encryption, identity verification, or key handling, please label it `security` so it gets looked at first.

## Reporting security issues

Do **not** open a public issue for a vulnerability that could put current users at risk (for example, anything that breaks message signing, identity pinning, or exposes plaintext data that should be encrypted). Follow the process in [SECURITY.md](./SECURITY.md) instead.

## Suggesting features

Open an issue describing the problem you're trying to solve, not just the feature. "Add read receipts" is a request. "I want to know if a message actually made it through Tor without polling manually" is a problem, and there might be a better solution than the one you had in mind.

## Contributing code

1. Fork the repo and create a branch from `main`.
2. Keep changes focused. One fix or one feature per pull request, not a bundle.
3. Match the existing code style. This is a Python/Flet project; look at neighboring files before introducing a different pattern.
4. If you touch anything cryptographic (key generation, signing, verification, TOFU pinning), explain your reasoning in the PR description. Changes here get reviewed more carefully, and that's intentional.
5. Test manually against a real Tor connection where relevant. There isn't a full automated test suite yet, so describe what you tested and how.
6. Open the pull request against `main` and describe what changed and why, not just what.

## What's most useful right now

Given where the project is (alpha, pre-Windows-build, pre-audit), these are the highest-value contributions:

- Bug reports from actually running the alpha, especially anything around connection reliability, message delivery, or the app hanging/crashing.
- Threat model review. If you know Tor, P2P systems, or applied cryptography, reading [THREAT_MODEL.md](./THREAT_MODEL.md) critically and pointing out gaps is extremely valuable, possibly more than code.
- Platform-specific testing (Windows in particular, since packaging is in progress).
- Documentation fixes. If something in the README confused you, it'll confuse the next person too.

## Code of conduct

Be direct, be honest about problems, don't be a jerk about it. Disagreements about architecture or security tradeoffs are welcome and expected. Personal attacks aren't.
