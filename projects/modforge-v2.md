# Case study: ModForge V2

**Area:** Deterministic compilation, Python, semantic validation, mutation testing, external verification

**Domain:** Cyberpunk 2077 mod tooling

**Status:** Active domain-specific engineering system; full production source remains private.

## The failure that triggered V2

An earlier validation run reported **210 passing checks and no failures**, while three target mods still exhibited broken behavior. The tests proved that the tooling generated files as expected; they did **not** prove that the game would use those files as intended.

This is a general engineering problem: **a passing test can be perfectly accurate about the wrong requirement**.

## What changed

1. **Parse actual record bodies, not only record names.** Resolve inherited records and collection operations from game-provided data.
2. **Validate using authoritative constraints.** Model schema/type evidence separately from domain behavior.
3. **Make checks capable of refusing.** Missing corpus or unsupported semantics must not quietly become success.
4. **Inject defects.** Verify that the validation gate detects deliberately broken input.
5. **Ask an external oracle.** Compare selected predictions with read-only observations from the game runtime.

```text
Specification -> parse -> resolve inheritance -> schema / semantic checks
                                      |                 |
                                      v                 v
                            planned effective state  REFUSE / UNKNOWN
                                      |
                            independent runtime probe
                                      |
                              agreement / mismatch /
                           missing / inconclusive
```

## Why it matters outside gaming

The patterns transfer directly to infrastructure-as-code validation, policy-as-code, configuration compilers, data-pipeline quality gates and AI-generated artifact verification. They demonstrate the difference between **self-consistency** and **external correctness**.

## Dated evidence and limitations

- Historical V1 observation (2026-08-11): 210 checks passing while three target mods were not working.
- V2 source-state checkpoint (2026-09-05): 845 tests passed, 13 skipped, 0 failed for a bounded implementation milestone.
- Passing source tests did **not** imply that the newer sealed production runtime had been activated.
- Runtime probes validate particular record/field states; they **do not guarantee every gameplay effect**.
- A false-positive/false-negative calibration episode is retained as a design lesson, rather than hidden.

Full engine code and game-specific assets are not republished here. Copyrighted game resources are not redistributed.

**Related:** [Evidence register](../EVIDENCE.md).